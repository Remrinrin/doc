2020年5月：Geneva版本docker-compose-geneva-redis.yml 

- consul: 服务注册及发现

- vault: secret存储

- security-secrets-setup: 产生secret设置

- vault-worker: secret存储设置

- 反向代理

  \- kong-db: postgres数据库

  \- kong-migrations: 迁移

  \- kong: API网关

  \- edgex-proxy: 安全设置

- redis: 数据库

- system: 系统管理代理

- notifications: 支持通知

- metadata: 元数据

- data: 核心数据

- command: 命令

- scheduler: 定时任务

- app-service-rules: 可配置应用服务

- rulesengine: 规则引擎

- device-virtual: 虚拟设备

- device-rest: Restful API设备

根据上面的分析结果，如果要将EdgeX Foundry进行分布式部署，需要实现：

- 设备服务Daemon化，以在特定设备上保持南向连接

  \- 比如device-modbus

- 核心数据服务有状态，须连接持久化存储/卷

  \- 比如redis、kong-db

- 响应性服务无状态，可以考虑多实例部署

  \- 比如command

- 应用服务Daemon化，以在特定设备上保持北向连接（可选）

  \- 比如app-service-rules

**与Kubernetes对象适配**

Kubernetes通过Pod和Controller来管理部署于其上的应用。Pod包含一个或多个容器，它是Kubernetes对象模型中的最小可部署对象，也是应用的最小可执行单元。Kubernetes通过Controller来部署应用，这些应用可由运行特性不同的ReplicaSet、StatefulSet、DameonSet等类型的Pod组成。

如果将EdgeX Foundry的当前模块与Kubernetes的不同Pod类型来对应，可以得到如下粗略列表：

- Deployment/ReplicaSet: consul, command, rulesengine, …
- DaemonSet: device services, app-service-rules, …
- StatefulSet: redis, data, metadata, …
- Job/CronJob: scheduler

需要注意的是这些对应只是从功能角度大致匹配，但实际执行方式可能会有较大差别。有些是长时运行，而其对应的模块可能是平台服务、或由平台服务触发的短时任务，比如定时。

如果将EdgeX Foundry在Docker上的原生部署服务和Kubernetes生态系统比较，可以得到下表：

| 微服务/模块  | EdgeX Foundry on Docker | Kubernetes生态             |
| ------------ | ----------------------- | -------------------------- |
| 定时服务     | 自制scheduler           | Job/CronJob                |
| API网关      | Kong                    | Envoy                      |
| 服务发现     | Consul                  | CoreDNS                    |
| 部署         | Docker  Compose         | Helm Chart                 |
| 网络         |                         | Flannel/Calico             |
| 存储         | Volume                  | PV/CSI                     |
| 生命周期管理 |                         | Operator                   |
| Service Mesh |                         | Istio/Network Service Mesh |

可以看出，对于像定时、API代理、服务发现、部署这样的基础功能，是比较容易从Kubernetes生态里找到对应的轻量级服务的。

另一个选项是保留在Docker中采用的原始服务，但这样就不能像Kubernetes原生应用那样运维了。而这并非本篇讨论的初心。

**Helm Chart**

更进一步地，可以将部署方式从Docker Compose转换为**Helm Chart**(https://helm.sh/)。同时，充分考虑分布式部署EdgeX Foundry在网络和存储方面的需求和设置，利用比如**Flannel**(https://github.com/coreos/flannel)和**PersistentVolume**(https://kubernetes.io/docs/concepts/storage/persistent-volumes/)之类服务实现。

**Operator**

在支持Helm Chart之上，可以考虑利用Kubernetes应用管理平台上的Operator，实现云边协同的统一管理。