# Namespaces
## short name: ```ns``` 

Kubernetes uses ```Namespace``` to organise objects in the cluster. 
```Namespace``` implements strict resource separation, allowing to isolate objects into distinct groups. ```ResourceQuota``` can be applied to ```Namespace``` to limit resource usage. If no namespace is selected, kubernetes will interact with ```default``` namespace. Resources names need to be unique within ```namespace```. 

## Kubernetes inbuilt namespaces:
<ul>
<li><strong> default </strong> - where all resources are created by default
<li><strong> kube-system </strong> - where all kubernetes infrastructure is stored
<li><strong> kube-public </strong> - where generic information is stored
</ul>

## Creating namespaces
### Imperatively
``` 
    kubectl create ns web 
```

### Declaratively
```
    apiVersion: v1
    kind: Namespace
    metadata:
      name: web
    ---
    apiVerion: v1
    kind: Pod
    metadata:
      name: web-pod
      namespace: web
    (...)
```


## Namespaces commands:

``` 
kubectl get ns
```
```
kubectl create ns <ns-name>
```
```
kubectl get ns <ns-name>
```
```
kubectl get pods --all-namespaces
```
```
kubectl get pods -n <ns-name> 
```

Set the working namespace:
```
kubectl config set-context $(kubectl config current-context) --namespace=<ns-name>
```
```
kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   

kubectl config set-context $(kubectl config current-context) --namespace=ckad
Context "kubernetes-admin@kubernetes" modified.

kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ckad
```


## Connecting to namespaces:

Services within same namespace:
```
  mysql.connect("<service-name>")
```

Services in different namespace:
```
  mysql.connect("<service-name>.<namespace-name>.svc.cluster.local")
```

## Namespace resource quotas
```
kubectl describe ns marketing
Name:         marketing
Labels:       <none>
Annotations:  <none>
Status:       Active

Resource Quotas
 Name:            marketing-quota
 Resource         Used  Hard
 --------         ---   ---
 limits.cpu       0     4
 limits.memory    0     2Gi
 pods             0     10
 requests.cpu     0     2
 requests.memory  0     1Gi

No LimitRange resource.
```