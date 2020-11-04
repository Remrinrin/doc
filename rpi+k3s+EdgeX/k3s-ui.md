# k8s DashBoard

---

2020.10.19

> dashboard社区维护较少，存在较多问题

---

- 下载dashboard配置yaml，并安装

- https://raw.githubusercontent.com/kubernetes/dashboard/<VERSION>/aio/deploy/recommended.yaml

- ```bash
  $ sudo kubectl create -f recommended.yaml
  #查看运行状况
  $ kubectl get pods -n kubernetes-dashboard
  NAME                                         READY   STATUS    RESTARTS   AGE
  dashboard-metrics-scraper-6b4884c9d5-tvqwz   1/1     Running   0          2m21s
  kubernetes-dashboard-7d8574ffd9-sslkr        1/1     Running   0          2m21s
  #卸载dashboard
  $ kubectl delete -f recommended.yaml
  ```
  
- 配置仪表盘**RBAC**，Role-Based Access Control（角色访问控制）
  
  > **重要：** 本指南中创建的 `admin-user` 将在仪表板中拥有管理权限。
  
- 创建以下资源清单文件，并通过kubectl apply：

- **1、dashboard.admin-user.yml**

- ```bash
apiVersion: v1 
kind: ServiceAccount
metadata:
  name: admin-user # 用户名
  namespace: kubernetes-dashboard
  ```

- **2、dashboard.admin-user-role.yml**

- ```bash
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
    name: admin-user # 用户名
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: ClusterRole
    name: cluster-admin
  subjects:
    - kind: ServiceAccount
      name: admin-user # 用户名
      namespace: kubernetes-dashboard
  ```

  

# octopus-ui

---

rancher 定制的k3s-ui

octopus是基于k8s/k3s的原生设备管理系统

---

```bash
#安装
$ kubectl apply -f https://raw.githubusercontent.com/cnrancher/octopus-api-server/master/deploy/e2e/all_in_one.yaml
```

```bash
$ sudo kubectl apply -f all_in_one.yaml 
serviceaccount/octopus-ui created #权限管理相关
clusterrolebinding.rbac.authorization.k8s.io/octopus-ui-rolebinding created #权限管理相关
service/octopus-ui created #访问
deployment.apps/octopus-ui created
$ kubectl get -n kube-system svc -l app.kubernetes.io/name=octopus-ui #查看service是否启动
# 由于RBAC原因，octopus-ui默认部署在kube-system的namespace，采用k3s的负载均衡
# 可以直接更改octopus-ui的配置
NAME                 TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                      AGE
octopus-ui           LoadBalancer   10.43.21.85     192.168.137.10   8443:31100/TCP               7m58s
```

通过浏览器，使用**EXTERNAL-IP**访问ui

> https://<EXTERNAL-IP>:8443
>
> 用户名及密码位于kubeconfig文件