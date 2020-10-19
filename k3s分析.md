ubuntu@ES0:~/k8s-dashboard$ sudo kubectl get all --all-namespaces
NAMESPACE     NAME                                         READY   STATUS              RESTARTS   AGE
kube-system   pod/helm-install-traefik-qqggd               0/1     Completed           0          200d
kube-system   pod/local-path-provisioner-6d59f47c7-lk7jp   1/1     Running             7          200d
kube-system   pod/metrics-server-7566d596c8-kz5n5          1/1     Running             1          200d
kube-system   pod/svclb-traefik-mzjtl                      2/2     Running             2          3h45m
kube-system   pod/traefik-758cd5fc85-7lwf4                 1/1     Running             1          3h45m
kube-system   pod/coredns-7944c66d8d-zrxdt                 1/1     Running             1          200d
kube-system   pod/svclb-traefik-4hjmw                      0/2     ContainerCreating   0          163m

NAMESPACE     NAME                         TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
default       service/kubernetes           ClusterIP      10.43.0.1      <none>           443/TCP                      200d
kube-system   service/kube-dns             ClusterIP      10.43.0.10     <none>           53/UDP,53/TCP,9153/TCP       200d
kube-system   service/metrics-server       ClusterIP      10.43.19.194   <none>           443/TCP                      200d
kube-system   service/traefik-prometheus   ClusterIP      10.43.69.233   <none>           9100/TCP                     3h45m
kube-system   service/traefik              LoadBalancer   10.43.116.36   192.168.137.10   80:31188/TCP,443:31009/TCP   3h45m

NAMESPACE     NAME                           DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
kube-system   daemonset.apps/svclb-traefik   2         2         1       2            1           <none>          3h45m

NAMESPACE     NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/metrics-server           1/1     1            1           200d
kube-system   deployment.apps/local-path-provisioner   1/1     1            1           200d
kube-system   deployment.apps/traefik                  1/1     1            1           3h45m
kube-system   deployment.apps/coredns                  1/1     1            1           200d

NAMESPACE     NAME                                               DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/local-path-provisioner-6d59f47c7   1         1         1       200d
kube-system   replicaset.apps/metrics-server-7566d596c8          1         1         1       200d
kube-system   replicaset.apps/traefik-758cd5fc85                 1         1         1       3h45m
kube-system   replicaset.apps/coredns-7944c66d8d                 1         1         1       200d

NAMESPACE     NAME                             COMPLETIONS   DURATION   AGE
kube-system   job.batch/helm-install-traefik   1/1           200d       200d