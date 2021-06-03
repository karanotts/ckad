```$ kubectl describe deploy kubernetes-bootcamp ```
```
Name:                   kubernetes-bootcamp
Namespace:              default
CreationTimestamp:      Thu, 03 Jun 2021 10:29:00 +0000
Labels:                 app=kubernetes-bootcamp
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=kubernetes-bootcamp
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
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
NewReplicaSet:   kubernetes-bootcamp-fb5c67579 (4/4 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  4m59s  deployment-controller  Scaled up replica set kubernetes-bootcamp-fb5c67579 to 4
```
```$ kubectl set image deploy kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v2 --record```
```
deployment.apps/kubernetes-bootcamp image updated
```
```$ kubectl rollout status deploy kubernetes-bootcamp ```
```
Waiting for deployment "kubernetes-bootcamp" rollout to finish: 2 out of 4 new replicas have been updated...
```
```$ kubectl rollout history deploy kubernetes-bootcamp```
```
deployment.apps/kubernetes-bootcamp 
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl set image deploy kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v2 --record=true
```
```$ kubectl rollout undo deploy kubernetes-bootcamp --to-revision=1```
```
deployment.apps/kubernetes-bootcamp rolled back
```
```$ kubectl rollout status deploy kubernetes-bootcamp```
```
deployment "kubernetes-bootcamp" successfully rolled out
```
```$ kubectl rollout history deploy kubernetes-bootcamp```
```
deployment.apps/kubernetes-bootcamp 
REVISION  CHANGE-CAUSE
2         kubectl set image deploy kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v2 --record=true
3         <none>
```