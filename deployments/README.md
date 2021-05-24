# Deployments
## short name: ```deploy````

Deployments allow to manage groups of pods using replicaSets. RepicaSets guarantee to replicate desired number of pods with the same configuration.


```
    kubectl get deployments

    kubectl get deployment <deployment name>

    kubectl describe deployment <deployment name>

    kubectl edit deployment <deployment name>

    kubectl delete deployment <deployment name>
```
<hr>

## Labels and Selectors
``` kubectl create deployment webserver --image=nginx:1.19.10 --replicas=3 ```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: webserver
  name: webserver
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webserver
  template:
    metadata:
      labels:
        app: webserver
    spec:
(...)
```

```metadata.labels``` - these labels belong to deployment/replicaSet itself - these are important if other kubernetes object needs to discover deployment/replicaSet

```spec.selector.matchLabels``` - match the labels declared in template section

```spec.template.metadata.labels``` - declare labels for deployment pods

<hr>

```
    kubectl get deployments.apps 
    NAME     READY   UP-TO-DATE   AVAILABLE   AGE
    webapp   3/3     3            3           64m

    kubectl get replicasets.apps 
    NAME               DESIRED   CURRENT   READY   AGE
    webapp-5654c984c   3         3         3       64m

    kubectl get pods
    NAME                     READY   STATUS    RESTARTS   AGE
    webapp-5654c984c-hf48q   1/1     Running   0          64m
    webapp-5654c984c-hxmmw   1/1     Running   0          64m
    webapp-5654c984c-t5gwz   1/1     Running   0          64m

    kubectl describe deployment webapp
    Name:                   webapp
    Labels:                 app=webapp
    Annotations:            deployment.kubernetes.io/revision: 1
    Selector:               app=webapp
    Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
    (...)

    kubectl describe replicasets.apps webapp-5654c984c
    Name:           webapp-5654c984c
    Namespace:      default
    Selector:       app=webapp,pod-template-hash=5654c984c
    Labels:         app=webapp
                    pod-template-hash=5654c984c
    Annotations:    deployment.kubernetes.io/desired-replicas: 3
                    deployment.kubernetes.io/max-replicas: 4
                    deployment.kubernetes.io/revision: 1
    Controlled By:  Deployment/webapp
    Replicas:       3 current / 3 desired
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed
```

<hr>

## Rollouts

When a deployment is first created it triggers a <strong>rollout</strong>.
```
    kubectl rollout history deployment webapp
    deployment.apps/webapp 
    REVISION  CHANGE-CAUSE
    1         <none>
```

<strong>Rolling updates</strong> provide a way to update a deployment by gradually updating replicas so there is no downtime.

Create a deployment:

```kubectl create deployment webserver --image=nginx:1.19.10 --replicas=3```

Check rollout status:

```kubectl rollout status deployment <deployment name>```

Update a deployment:

```kubectl set image deployment <deployment name> <container name>=<image> --record``` (use ```--record=true``` to record changes done, else it shows as CHANGE-CAUSE: <none>)

```
    kubectl set image deployment webserver nginx=1.20.0 --record=true
```

Check rollout status:
```
    kubectl describe deployment webserver | grep Image:
        Image:        nginx:1.20.0

    kubectl rollout status deployment webserver
    deployment "webserver" successfully rolled out

    kubectl rollout history deployment webserver
    deployment.apps/webserver 
    REVISION  CHANGE-CAUSE
    1         <none>
    2         kubectl set image deployment webserver nginx=nginx:1.20.0 --record=true
```

Check particular revision:
```
    kubectl rollout history deployment webserver --revision=1
    deployment.apps/webserver with revision #1
    Pod Template:
    Labels:       app=webserver
            pod-template-hash=55df995c99
    Containers:
    nginx:
        Image:      nginx:1.19.10

    kubectl rollout history deployment webserver --revision=2
    deployment.apps/webserver with revision #2
    Pod Template:
    Labels:       app=webserver
            pod-template-hash=56f648d5d4
    Containers:
    nginx:
        Image:      nginx:1.20.0
```

## Rollbacks
Rollouts can be undone if necessary with <strong>rollbacks</strong> - ```rollout undo```

```kubectl rollout undo deployment <deployment name>``` - rollback to previous (last) revision

```kubectl rollout undo deployment <deployment name> --to-revision=<revision number>``` - rollback to any previous revision using revision number as a selector

```
    kubectl rollout undo deployment webserver --to-revision=1
    deployment.apps/webserver rolled back

    kubectl rollout history deployment webserver
    deployment.apps/webserver 
    REVISION  CHANGE-CAUSE
    2         kubectl set image deployment webserver nginx=nginx:1.20.0 --record=true
    3         <none>

    kubectl rollout history deployment webserver --revision=3
    deployment.apps/webserver with revision #3
    Pod Template:
    Labels:       app=webserver
            pod-template-hash=55df995c99
    Containers:
    nginx:
        Image:      nginx:1.19.10
```

### Deployment spec
```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
        name: rolling-deployment
    spec:
    strategy:
        rollingUpdate:
            maxSurge: 3
            maxUnavailable: 1
        replicas: 3
```
```strategy``` - specified strategy used to replace pods. It can be:
- ```Recreate```: all pods are killed before new ones are created, <u>causes downtime</u>
- ```RollingUpdate```: pod are being replaced in turns to avoid downtime

```strategy.rollingUpdate.maxUnavailable``` (optional) - specifies how many pods can become unavailable during rolling update

```strategy.rollingUpdate.maxSurge``` (optional) - specifies how many pods can be created over the number of desired replicas

Both ```maxUnavailable``` and ```maxSurge``` can be set as a numeric value (ie. 3) or percentage (ie. 25%)

<hr>

```
    kubectl describe deployment webserver
    (...)
    Events:
      Type    Reason             Age                    From                   Message
      ----    ------             ----                   ----                   -------
      Normal  ScalingReplicaSet  29m (x2 over 3h27m)    deployment-controller  Scaled down replica set webserver-55df995c99 to 1
      Normal  ScalingReplicaSet  29m (x2 over 3h27m)    deployment-controller  Scaled up replica set webserver-56f648d5d4 to 3
      Normal  ScalingReplicaSet  29m (x2 over 3h27m)    deployment-controller  Scaled down replica set webserver-55df995c99 to 0
      Normal  ScalingReplicaSet  4m25s (x2 over 3h20m)  deployment-controller  Scaled up replica set webserver-55df995c99 to 1
      Normal  ScalingReplicaSet  4m23s                  deployment-controller  Scaled up replica set webserver-55df995c99 to 2
      Normal  ScalingReplicaSet  4m23s (x2 over 3h20m)  deployment-controller  Scaled down replica set webserver-56f648d5d4 to 2
      Normal  ScalingReplicaSet  4m20s                  deployment-controller  Scaled down replica set webserver-56f648d5d4 to 1
      Normal  ScalingReplicaSet  4m20s (x2 over 3h28m)  deployment-controller  Scaled up replica set webserver-55df995c99 to 3
      Normal  ScalingReplicaSet  4m17s                  deployment-controller  Scaled down replica set webserver-56f648d5d4 to 0
      Normal  ScalingReplicaSet  3m57s (x3 over 3h27m)  deployment-controller  Scaled up replica set webserver-56f648d5d4 to 1
      Normal  ScalingReplicaSet  3m54s (x3 over 3h27m)  deployment-controller  Scaled up replica set webserver-56f648d5d4 to 2
      Normal  ScalingReplicaSet  3m54s (x3 over 3h27m)  deployment-controller  Scaled down replica set webserver-55df995c99 to 2
      Normal  ScalingReplicaSet  3m50s (x7 over 3h20m)  deployment-controller  (combined from similar events): Scaled down replica set webserver-55df995c99 to 0
```k