```$ kubectl get deploy```
```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           9m19s
```
```$ kubectl get rs```
```
NAME                            DESIRED   CURRENT   READY   AGE
kubernetes-bootcamp-fb5c67579   1         1         1       9m30s
```
```$ kubectl scale deploy kubernetes-bootcamp --replicas=4```
```
deployment.apps/kubernetes-bootcamp scaled
```
```$ kubectl get deploy```
```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   4/4     4            4           10m
```
```$ kubectl get rs```
```
NAME                            DESIRED   CURRENT   READY   AGE
kubernetes-bootcamp-fb5c67579   4         4         4       10m
```
```$ kubectl get pods -o wide```
```
NAME                                  READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
kubernetes-bootcamp-fb5c67579-9c959   1/1     Running   0          12m     172.18.0.3   minikube   <none>           <none>
kubernetes-bootcamp-fb5c67579-9xcwl   1/1     Running   0          2m59s   172.18.0.7   minikube   <none>           <none>
kubernetes-bootcamp-fb5c67579-mt8nd   1/1     Running   0          2m59s   172.18.0.9   minikube   <none>           <none>
kubernetes-bootcamp-fb5c67579-tdfbr   1/1     Running   0          2m59s   172.18.0.8   minikube   <none>           <none>
```