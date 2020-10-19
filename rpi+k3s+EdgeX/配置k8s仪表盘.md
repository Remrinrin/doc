# k8s DashBoard use for k3s

---

2020.10.19

---

- 下载dashboard配置yaml，并安装

- https://raw.githubusercontent.com/kubernetes/dashboard/<VERSION>/aio/deploy/recommended.yaml

- ```bash
  sudo kubectl create -f recommended.yaml
  ```
  
- 配置仪表盘**RBAC**，Role-Based Access Control（角色访问控制）
  
  > **重要：** 本指南中创建的 `admin-user` 将在仪表板中拥有管理权限。
  
- 创建以下资源清单文件：

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

  

