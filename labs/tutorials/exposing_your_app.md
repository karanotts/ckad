```$ kubectl get pods```
```
NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-fb5c67579-2ndm4   1/1     Running   0          3m55s
```
```$ kubectl get services```
```
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   4m22s
```
```$ kubectl expose deployment kubernetes-bootcamp --type="NodePort" --port 90```
```
service/kubernetes-bootcamp exposed
```
```$ kubectl get services```
```
NAME                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes            ClusterIP   10.96.0.1        <none>        443/TCP        5m12s
kubernetes-bootcamp   NodePort    10.107.241.254   <none>        90:32278/TCP   16s
```
```$ kubectl describe service kubernetes-bootcamp```
```
Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   app=kubernetes-bootcamp
Annotations:              <none>
Selector:                 app=kubernetes-bootcamp
Type:                     NodePort
IP Families:              <none>
IP:                       10.107.241.254
IPs:                      10.107.241.254
Port:                     <unset>  90/TCP
TargetPort:               90/TCP
NodePort:                 <unset>  32278/TCP
Endpoints:                172.18.0.6:90
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```
```$ kubectl get service kubernetes-bootcamp -o yaml | grep nodePort```
```
  - nodePort: 32278
```
```$ kubectl describe deployments.apps```
```
Name:                   kubernetes-bootcamp
Namespace:              default
CreationTimestamp:      Thu, 03 Jun 2021 09:56:08 +0000
Labels:                 app=kubernetes-bootcamp
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=kubernetes-bootcamp
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=kubernetes-bootcamp
  Containers:
   kubernetes-bootcamp:
    Image:        gcr.io/google-samples/kubernetes-bootcamp:v1
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   kubernetes-bootcamp-fb5c67579 (1/1 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  6m47s  deployment-controller  Scaled up replica set kubernetes-bootcamp-fb5c67579 to 1
```
```$ kubectl describe deployments kubernetes-bootcamp | grep Labels:```
```
Labels:                 app=kubernetes-bootcamp
  Labels:  app=kubernetes-bootcamp
```
```$ kubectl get svc -l app=kubernetes-bootcamp```
```
NAME                  TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes-bootcamp   NodePort   10.107.241.254   <none>        90:32278/TCP   4m51s
```
```$ kubectl get pods -l app=kubernetes-bootcamp```
```
NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-fb5c67579-2ndm4   1/1     Running   0          9m3s
```
```$ kubectl label pod kubernetes-bootcamp-fb5c67579-2ndm4 version=v1```
```
pod/kubernetes-bootcamp-fb5c67579-2ndm4 labeled
```
```$ kubectl get pod kubernetes-bootcamp-fb5c67579-2ndm4 --show-labels```
```
NAME                                  READY   STATUS    RESTARTS   AGE   LABELS
kubernetes-bootcamp-fb5c67579-2ndm4   1/1     Running   0          11m   app=kubernetes-bootcamp,pod-template-hash=fb5c67579,version=v1
```