```kubectl get pods```
```
NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-fb5c67579-s4h2p   1/1     Running   0          43s
```
```kubectl get services```
```
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   98s
```
```kubectl get deployments```
```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           2m1s
```
```kubectl expose deployment kubernetes-bootcamp --type=NodePort --port=8080```
```
service/kubernetes-bootcamp exposed
```
```kubectl get services```
```
NAME                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
kubernetes            ClusterIP   10.96.0.1        <none>        443/TCP          3m9s
kubernetes-bootcamp   NodePort    10.107.163.113   <none>        8080:31769/TCP   35s
```
```kubectl describe service kubernetes-bootcamp```
```
Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   app=kubernetes-bootcamp
Annotations:              <none>
Selector:                 app=kubernetes-bootcamp
Type:                     NodePort
IP Families:              <none>
IP:                       10.107.163.113
IPs:                      10.107.163.113
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  31769/TCP
Endpoints:                172.18.0.3:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```
```export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')```

```echo NODE_PORT=$NODE_PORT```
```
NODE_PORT=31769
```
```curl $(minikube ip):$NODE_PORT```
```
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-fb5c67579-s4h2p | v=1
```