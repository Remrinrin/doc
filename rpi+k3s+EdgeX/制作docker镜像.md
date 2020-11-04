## 制作镜像

1. 准备Dockerfile内容。

   1. 新建并编辑Dockerfile文件。

      ```bash
      $ vim Dockerfile
      ```

   2. 按i进入编辑模式，添加以下内容。

      ```bash
      FROM apachephp:v1  #声明基础镜像来源
      MAINTAINER DTSTACK #声明镜像拥有者
      RUN mkdir /dtstact #RUN后面接容器运行前需要执行的命令，由于Dockerfile文件不能超过127行，因此当命令较多时建议写到脚本中执行
      ENTRYPOINT ping www.aliyun.com #开机启动命令，此处最后一个命令需要是可在前台持续执行的命令，否则容器后台运行时会因为命令执行完而退出。
      ```

   3. 按下键盘esc键，输入`:wq`并按下enter键，保存并退出Dockerfile文件。

2. 构建镜像。

   ```bash
   docker build -t webaliyunlinux:v1 .   # . 是Dockerfile文件的路径，不能忽略
   docker images                    #查看是否创建成功
   ```

3. 运行容器并查看。

   ```bash
   docker run -d webaliyunlinux:v1       #后台运行容器
   docker ps                        #查看当前运行中的容器
   docker ps -a                     #查看所有容器，包括未运行中的
   docker logs CONTAINER ID/IMAGE   #如未查看到刚才运行的容器，则用容器id或者名字查看启动日志排错
   ```

4. 制作镜像。

   ```bash
   docker commit fb2844b6**** dtstackweb:v1 #commit参数后添加容器ID和构建新镜像的名称和版本号。
   docker images                    #列出本地（已下载的和本地创建的）镜像
   ```

5. 将镜像推送至远程仓库。

   默认推送到Docker Hub。您需要先登录Docker，为镜像绑定标签，将镜像命名为`Docker用户名/镜像名:标签`的格式。最终完成推送。

   ```bash
   docker login --username=dtstack_plus registry.cn-shanghai.aliyuncs.com #执行后输入镜像仓库密码
   docker tag [ImageId] registry.cn-shanghai.aliyuncs.com/dtstack123/test:[标签]
   docker push registry.cn-shanghai.aliyuncs.com/dtstack123/test:[标签]
   ```