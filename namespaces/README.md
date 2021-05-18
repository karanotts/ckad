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

``` kubectl get ns ```

``` kubectl create ns <ns-name> ```

``` kubectl get ns <ns-name> ```

``` kubectl get pods --all-namespaces ``` 

``` kubectl get pods ns <ns-name> ```