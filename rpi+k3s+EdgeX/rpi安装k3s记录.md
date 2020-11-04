# k3s安装记录
------
2020.10基于树莓派4B版本，Ubuntu20.04server，k3s版本v1.18.9+k3s1，验证可行。
> 注：containerd的manifest机制存在问题，在pull镜像时会识别错误的host arch，换用docker。
> 见文末：使用docker作为容器运行时。
------

## 系统配置

* 更改hostname

* ```bash
  $ hostnamectl
     Static hostname: ES0
           Icon name: computer
          Machine ID: e0e3******b8ea03
             Boot ID: 6814******c4ad8ed
    Operating System: Ubuntu 20.04 LTS
              Kernel: Linux 5.4.0-1008-raspi
        Architecture: arm64
  $ sudo hostnamectl set-hostname <your-hostname>
  ```

* change the ***preserve_hostname*** value to ***true***. This will cause the set and update hostname module to not operate.
  
* ```bash
  $ sudo vim /etc/cloud/cloud.cfg
  ```

## Enable Linux container feature:

* 启用cgroups：ARM hardware may run into difficulties because cgroups
  (required!) are not enabled by default. This can be remedied on the Rasberry Pi
  by editing the boot parameters:

* ```bash
  $ sudo vim /boot/firmware/cmdline.txt
  ```

* add: 

  ```vim
  cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1 swapaccount=1  
  ```
  
* Configure Networking in Ubuntu 20.04 with NetPlan

## Static IP address

* disable **dhcp** and provide a static IP.

```bash
$ sudo vim /etc/netplan/50-cloud-init.yaml
~                                                                                                            
network:
    ethernets:
        eth0:
            dhcp4: false
            optional: true
            addresses: [192.168.137.110/16]
            gateway4: 192.168.137.1 #网关
            nameservers:
                    addresses: [192.168.137.1,223.5.5.5,223.6.6.6] #阿里云DNS
    version: 2
~
$ sudo netplan apply
```


## k3s airGap  离线安装server节点

* 下载airgap和二进制文件，放入以下路径，二进制文件重命名为k3s

* 下载安装脚本，安装单节点server

* ```bash
  $ sudo mkdir -p /var/lib/rancher/k3s/agent/images/
  $ sudo cp ./k3s-airgap-images-$ARCH.tar /var/lib/rancher/k3s/agent/images/
  $ sudo cp ./k3s-arm64 /usr/local/bin/k3s
  $ sudo chmod +x /usr/local/bin/k3s
  $ INSTALL_K3S_SKIP_DOWNLOAD=true ./install.sh # 单server安装方式
  ```
* 检查master节点是否启功
* ```bash
  $ sudo kubectl get nodes
  ~
  NAME   STATUS   ROLES    AGE    VERSION
  es0    Ready    master   200d   v1.18.9+k3s1
  ```
* or获取更详细信息
* ```bash
$ sudo kubectl get nodes -o wide
~
NAME   STATUS   ROLES    AGE    VERSION        INTERNAL-IP       EXTERNAL-IP   OS-IMAGE           
es0    Ready    master   200d   v1.18.9+k3s1   192.168.137.10    <none>        Ubuntu 20.04 LTS
KERNEL-VERSION     CONTAINER-RUNTIME
  5.4.0-1019-raspi   containerd://1.3.3-k3s2
  ~
  ```

## 安装agent节点 

* 获取server的token
* ```bash
$ sudo cat /var/lib/rancher/k3s/server/node-token
~
  K103**********0291::server:c5d3********7be23
  ```
* **防火墙**，（正常配置应该是开放端口6443、443或者URL），现在暂时不考虑安全问题，可以直接关闭；默认情况下防火墙应该未激活。
* ufw 配置见 https://www.itcoder.tech/posts/how-to-setup-a-firewall-with-ufw-on-ubuntu-20-04/
* ```bash
  #检查ufw状态
  $ sudo ufw status verbose
  ~
  Status: inactive
  #如果激活，禁用即可
  $ sudo ufw disable 
  ```

* 主机系统配置同server，同样通过离线包安装，在安装时配置INSTALL_K3S_SKIP_DOWNLOAD参数
* ```bash
  $ sudo mkdir -p /var/lib/rancher/k3s/agent/images/
  $ sudo cp ./k3s-airgap-images-$ARCH.tar /var/lib/rancher/k3s/agent/images/
  $ sudo cp ./k3s-arm64 /usr/local/bin/k3s
  $ sudo chmod +x /usr/local/bin/k3s
  $ sudo chmod +x ./install.sh
  # 安装节点，注意myserver的ip和token
  $ INSTALL_K3S_SKIP_DOWNLOAD=true K3S_URL=https://<myserver>:6443 K3S_TOKEN=<mynodetoken> ./install.sh 
  # 检查agent是否运行
  $ sudo systemctl status k3s-agent
  ```
* 切换server查看节点状态
* ```bash
  $ sudo kubectl get nodes -o wide
  ~
  NAME   STATUS   ROLES    AGE    VERSION        INTERNAL-IP       
  ea1    Ready    <none>   2m8s   v1.18.9+k3s1   192.168.137.111   
  es0    Ready    master   200d   v1.18.9+k3s1   192.168.137.10
EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION     CONTAINER-RUNTIME
<none>        Ubuntu 20.04 LTS   5.4.0-1019-raspi   containerd://1.3.3-k3s2
  <none>        Ubuntu 20.04 LTS   5.4.0-1021-raspi   containerd://1.3.3-k3s2
  ```

# 卸载k3s
```bash
#agent
$ /usr/local/bin/k3s-agent-uninstall.sh
#server
$ /usr/local/bin/k3s-uninstall.sh
```

# 使用docker作为容器运行时

- 安装对应架构的docker安装包

- https://download.docker.com/linux/ubuntu/dists/

- ```bash
  $ sudo apt-get remove docker docker-engine docker.io containerd runc #卸载旧版
  $ sudo dpkg -i </path/to/package.deb> #离线安装docker，共三个deb包：ce，cli和containerd（先安装运行时）
  #更换阿里源image加速
$ sudo vim /etc/docker/daemon.json
  ~
  {
    "registry-mirrors": ["https://6***o.mirror.aliyuncs.com"]
  }
  ~
  $ sudo systemctl daemon-reload
  $ sudo systemctl restart docker
  ```
  
- 定制安装变量

- https://rancher.com/docs/k3s/latest/en/installation/install-options/

- ```bash
  $ export INSTALL_K3S_SKIP_DOWNLOAD=true # 采用离线安装
  $ export INSTALL_K3S_EXEC="--docker --write-kubeconfig ~/.kube/config --write-kubeconfig-mode 666"
  #--docker 使用docker为容器运行时
  #/etc/rancher/k3s/k3s.yaml的默认权限为-rw-------所有者root root，--write-kubeconfig-mode 666添加非所有者权限 
  #--write-kubeconfig ~/.kube/config 修改配置为k8s默认位置。
  #注：k3s配置默认的位置/etc/rancher/k3s/k3s.yaml，可能会导致istio、helm需要额外设置或无法运行。
  ```

- 执行安装脚本

- ```bash
  # tar包以及执行文件，前略
  #server
  $ ./install.sh
#agent
  $ export K3S_URL=https://<myserver>:6443
  $ export K3S_TOKEN=<mynodetoken>
  $ ./install.sh
  ```
  

