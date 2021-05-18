# Pods 

In Kubernetes, a ```pod``` is a smaller deployable unit of computing. A ```pod``` can store one or multiple ```containers```, with shared context, storage and network resources.

<hr>

## Creating Pods

### Imperatively:
``` 
    kubectl run hazelcast --image=hazelcast/hazelcast --restart=Never \
    --port=5701 --env="DNS_DOMAIN=cluster" --labels="app=hazelcast,env=stg"
```

```
    kubectl run tmp --image=busybox -it --rm -- /bin/sh
```

``` 
    kubectl run mypod --image=busybox --restart=Never \
    --env="ENVIRONMENT=dev" --labels="environment=dev" --dry-run=client \
     -o yaml > mypod.yaml -- /bin/sh -c 'echo "Welcome to $ENVIRONMENT!"'

    kubectl create -f mypod.yaml

    kubectl logs mypod
    # Welcome to dev!
```

### Declaratively:
```
    apiVersion: v1
    kind: Pod
    metadata:
      name: hazelcast
      labels:
        app: hazelcast
        env: stg
    spec:
      containers:
      - name: hazelcast
        image: hazelcast/hazelcast
        ports:
        - containerPort: 5701
      restartPolicy: Never
```
<hr>

## Pod lifecycle: 

``` Pending ``` - pod has been accepted by kubernetes system but has not yet been created

``` Running ``` - at least one container in a pod is running, starting or restarting

``` Terminating ``` - pod is currently being terminated 

``` Succeeded ``` - all containers in a pod have been successfully terminated

``` Failed ``` - all containers in a pod have been terminated, at least one thrown an error

``` Unknown ``` - the state of a pod could not be obtainer by kubernetes system

<hr>

## Pod commands:

<strong>Listing pods:</strong>

``` kubectl get pods --all-namespaces ```

``` kubectl get pod <pod-name> -n <namespace> ```

``` kubectl describe pod <pod-name> -n <namespace> ```

<strong>Accessing pod logs:</strong>

``` kubectl logs <pod-name> ```

<strong>Executing command on a pod:</strong>

``` kubectl exec <pod-name> -- echo "Hello World" ```

``` kubectl exec -it <pod-name> -- /bin/sh ```

<strong>Deleting a pod</strong>

``` kubectl delete pod <pod-name> ```

``` kubectl delete -f pod-file.yaml ```

``` kubectl delete pod <pod-name> --grace-period=0 --force ```