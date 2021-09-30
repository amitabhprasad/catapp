###Check Agro CD deployment
- oc get all -n argocd
```
NAME                                     READY   STATUS    RESTARTS   AGE
pod/argocd-application-controller-0      1/1     Running   0          7d15h
pod/argocd-dex-server-56599558bd-fmj97   1/1     Running   2          7d15h
pod/argocd-repo-server-688795869-x9qwz   1/1     Running   0          7d15h
pod/argocd-server-5d665fb5d8-c7q2v       1/1     Running   0          7d15h

NAME                            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/argocd-dex-server       ClusterIP   172.21.224.178   <none>        5556/TCP,5557/TCP,5558/TCP   7d15h
service/argocd-metrics          ClusterIP   172.21.37.73     <none>        8082/TCP                     7d15h
service/argocd-redis            ClusterIP   172.21.87.253    <none>        6379/TCP                     7d15h
service/argocd-repo-server      ClusterIP   172.21.215.202   <none>        8081/TCP,8084/TCP            7d15h
service/argocd-server           ClusterIP   172.21.147.7     <none>        80/TCP,443/TCP               7d15h
service/argocd-server-metrics   ClusterIP   172.21.101.248   <none>        8083/TCP                     7d15h

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-dex-server    1/1     1            1           7d15h
deployment.apps/argocd-redis         0/1     0            0           7d15h
deployment.apps/argocd-repo-server   1/1     1            1           7d15h
deployment.apps/argocd-server        1/1     1            1           7d15h

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-dex-server-56599558bd   1         1         1       7d15h
replicaset.apps/argocd-redis-74d8c6db65        1         0         0       7d15h
replicaset.apps/argocd-repo-server-688795869   1         1         1       7d15h
replicaset.apps/argocd-server-5d665fb5d8       1         1         1       7d15h

NAME                                             READY   AGE
statefulset.apps/argocd-application-controller   1/1     7d15h

NAME                                    HOST/PORT                                                                                                            PATH   SERVICES        PORT    TERMINATION            WILDCARD
route.route.openshift.io/argocd-route   argocd-route-argocd.amitabh-spp-snapshot-fae6c235d7a3aed6346697c0e75f4896-0000.us-south.containers.appdomain.cloud          argocd-server   https   passthrough/Redirect   None
```

### Commands
- login
```
argocd login argocd-route-argocd.amitabh-spp-snapshot-fae6c235d7a3aed6346697c0e75f4896-0000.us-south.containers.appdomain.cloud
```
- argocd cluster|repo|app list
- argocd account get-user-info
- argocd account update-password
- argocd app create --help
