ubuntu@ES0:~$ sudo kubectl get all --all-namespaces
NAMESPACE     NAME                                         READY   STATUS      RESTARTS   AGE
kube-system   pod/helm-install-traefik-cs7nn               0/1     Completed   0          9m15s
kube-system   pod/metrics-server-7566d596c8-4pkmz          1/1     Running     1          9m14s
kube-system   pod/local-path-provisioner-6d59f47c7-65cgw   1/1     Running     1          9m14s
kube-system   pod/svclb-traefik-5btq7                      2/2     Running     2          7m49s
kube-system   pod/traefik-758cd5fc85-m7qlz                 1/1     Running     1          7m50s
kube-system   pod/coredns-7944c66d8d-mnskg                 1/1     Running     2          9m14s

NAMESPACE     NAME                         TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                      AGE
default       service/kubernetes           ClusterIP      10.43.0.1       <none>           443/TCP                      9m31s
kube-system   service/kube-dns             ClusterIP      10.43.0.10      <none>           53/UDP,53/TCP,9153/TCP       9m29s
kube-system   service/metrics-server       ClusterIP      10.43.125.134   <none>           443/TCP                      9m27s
kube-system   service/traefik-prometheus   ClusterIP      10.43.244.92    <none>           9100/TCP                     7m51s
kube-system   service/traefik              LoadBalancer   10.43.147.215   192.168.137.10   80:31344/TCP,443:31840/TCP   7m51s

NAMESPACE     NAME                           DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
kube-system   daemonset.apps/svclb-traefik   1         1         1       1            1           <none>          7m51s

NAMESPACE     NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/local-path-provisioner   1/1     1            1           9m29s
kube-system   deployment.apps/metrics-server           1/1     1            1           9m28s
kube-system   deployment.apps/traefik                  1/1     1            1           7m51s
kube-system   deployment.apps/coredns                  1/1     1            1           9m29s

NAMESPACE     NAME                                               DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/local-path-provisioner-6d59f47c7   1         1         1       9m16s
kube-system   replicaset.apps/metrics-server-7566d596c8          1         1         1       9m16s
kube-system   replicaset.apps/traefik-758cd5fc85                 1         1         1       7m51s
kube-system   replicaset.apps/coredns-7944c66d8d                 1         1         1       9m16s

NAMESPACE     NAME                             COMPLETIONS   DURATION   AGE
kube-system   job.batch/helm-install-traefik   1/1           88s        9m27s