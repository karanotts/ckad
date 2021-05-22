# Logging, Monitoring and Debugging

## Loging

Get container logs in a single container pod:
```
    kubectl logs <pod-name>
```

Get container logs in a multi-container pod:
```
    kubectl logs <pod-name> -c <container-name>
```
<br><hr>

## Debugging

Pod status errors:

```ImagePullBackOff``` or ```ErrImagePull``` - image can't be retrieved from registry (incorrect image name, registry authentication error etc)

```CrashLoopBackOff``` - app or command crashed 

```CreateContainerConfigError``` - likely a ```configMap``` or ```secret``` can't be found

### Events

```
    kubectl get events
    LAST SEEN   TYPE      REASON          OBJECT                                  MESSAGE
    29m         Normal    Pulling         pod/my-pod                              Pulling image "busybox"
    29m         Normal    Created         pod/my-pod                              Created container my-container-1
    29m         Normal    Started         pod/my-pod                              Started container my-container-1
    29m         Normal    Pulled          pod/my-pod                              Successfully pulled image "busybox" in 1.545233793s
    4m48s       Normal    FailedBinding   persistentvolumeclaim/nginx-claim0      no persistent volumes available for this claim and no storage class is set
    4m48s       Normal    FailedBinding   persistentvolumeclaim/registry-claim0   no persistent volumes available for this claim and no storage class is set
    25m         Normal    Pulling         pod/webapp                              Pulling image "busybox"
    35m         Normal    Started         pod/webapp                              Started container webapp
    8s          Warning   BackOff         pod/webapp                              Back-off restarting failed container
```

### Using ephemeral container (alpha)
Minimalistic (no shell) containers can be troubleshooted using <strong>ephemeral containers</strong> <i>(experimental feature, needs to be enabled, due to alpha features nature not suitable for prod environments!)</i>
```
    kubectl create -f minimal-pod.yaml 
    pod/noshell created

    kubectl exec noshell -it -- /bin/sh
    OCI runtime exec failed: exec failed: container_linux.go:367: starting container process caused: exec: "/bin/sh": stat /bin/sh: no such file or directory: unknown
    command terminated with exit code 126

    kubectl debug -it noshell --image=busybox
```

### Useful debugging commands:

If pod doesn't connect to service, check selectors and labels for mismatch

``` kubectl describe service <service-name> ```

``` kubectl get pods --show-labels ```

``` kubectl get endpoints <service-name> ```

<br><hr>

## Monitoring

For monitoring ```metrics-server``` has to be enabled on the cluster. 

``` kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml ```
```
    kubectl get --raw /apis/metrics.k8s.io/
    {
    "kind": "APIGroup",
    "apiVersion": "v1",
    "name": "metrics.k8s.io",
    "versions": [
        {
        "groupVersion": "metrics.k8s.io/v1beta1",
        "version": "v1beta1"
        }
    ],
    "preferredVersion": {
        "groupVersion": "metrics.k8s.io/v1beta1",
        "version": "v1beta1"
    }
    }
```
``` kubectl top node ```

``` kubectl top pod <pod-name> ```
