# Labels

Labels are key-value pairs that can be attached to kubernetes objects and can be used in search queries. Labels are limited to 63 chracters.


## Declaring labels
### Imperatively
```
    kubectl run my-pod --image=busybox --labels="env=development,tier=backend,version=v1.2.12"
```
### Declaratively
```
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-pod
      labels:
        env: development
        tier: backend
        version: v1.2.12
    spec:
        containers:
        - name: my-pod
          image: busybox
```
## Inspecting labels
```
    kubectl get pods --show-labels
```
## Modifying labels
```
    kubectl get pods --show-labels
    NAME     READY   STATUS      RESTARTS   AGE    LABELS
    my-pod   1/1     Running     2          124m   environment=dev

    kubectl label pod my-pod environment=development --overwrite
    pod/my-pod labeled

    kubectl get pods --show-labels
    NAME     READY   STATUS      RESTARTS   AGE    LABELS
    my-pod   1/1     Running     2          126m   environment=development

    kubectl label pod my-pod revision=1
    pod/my-pod labeled
    kubectl get pods --show-labels
    NAME     READY   STATUS      RESTARTS   AGE    LABELS
    my-pod   1/1     Running     2          127m   environment=development,revision=1

    kubectl label pod my-pod revision=1 environment=dev region=uk_west --overwrite
    pod/my-pod labeled
    kubectl get pods --show-labels
    NAME     READY   STATUS      RESTARTS   AGE    LABELS
    my-pod   1/1     Running     2          129m   environment=dev,region=uk_west,revision=1
```
## Querying by labels
``` 
    kubectl get pods -l environment=dev --show-labels
    NAME     READY   STATUS      RESTARTS   AGE    LABELS
    my-pod   1/1     Running     2          132m   environment=dev,region=uk_west,revision=1
    mypod    0/1     Running     0          78m    environment=dev


    ubectl get pods -l 'region in (uk_west,uk_south)' --show-labels
    NAME     READY   STATUS    RESTARTS   AGE    LABELS
    my-pod   1/1     Running   2          143m   environment=dev,region=uk_west,revision=1
    nginx    1/1     Running   0          104m   region=uk_south,revision=1

    kubectl get pods -l region --show-labels
    NAME     READY   STATUS             RESTARTS   AGE     LABELS
    my-pod   1/1     Running            2          143m    environment=dev,region=uk_west,revision=1
    nginx    1/1     Running            0          104m    region=uk_south,run=nginx
    webapp   1/1     Running            0          3m52s   environment=stg,region=fr_south

    kubectl get pods -l environment!=dev --show-labels
    NAME     READY   STATUS             RESTARTS   AGE     LABELS
    nginx    1/1     Running            0          107m    region=uk_south,run=nginx
    webapp   1/1     Running            0          6m22s   environment=stg,region=fr_south

    kubectl get pods -l env=prod,bu=finance,tier=frontend
    NAME          READY   STATUS    RESTARTS   AGE
    app-1-zzxdf   1/1     Running   0          6m16s   bu=finance,env=prod,tier=frontend
```

## Selectors
```
    kubectl get all --selector env=dev
```

## Node selector

```
    kubectl get nodes
    NAME                           STATUS   ROLES                  AGE   VERSION
    131c.mylabserver.com   Ready    control-plane,master   24d   v1.20.1
    361c.mylabserver.com   Ready    <none>                 24d   v1.20.1
    881c.mylabserver.com   Ready    <none>                 24d   v1.20.1

    kubectl label 361c.mylabserver.com size=large
    node/361c.mylabserver.com labeled
```
```
    apiVersion: v1
    kind: Pod
    metadata:
      name: webapp-pod
    spec:
      nodeSelector:
        size: large
      containers:
      - name: webapp-pod
        image: nginx
```
```
    kubectl get pod webapp-pod -o wide
    NAME         READY   STATUS    RESTARTS   AGE   IP                NODE                           NOMINATED NODE   READINESS GATES
    webapp-pod   1/1     Running   0          33s   192.168.231.166   361c.mylabserver.com   <none>           <none>

    kubectl get pod webapp-pod -o yaml | grep -C 2 nodeSelector:
    ---
    enableServiceLinks: true
    nodeName: d04862c2361c.mylabserver.com
    nodeSelector:
        size: large
    preemptionPolicy: PreemptLowerPriority
```

## kubectl 

### label resource:
```kubectl label pod <pod-name> ver=1```

### show by label and key: 
```kubectl get pod -L ver```

### show by label and key=value:
```kubectl get pod -L ver=v1```

### remove label:
```kubectl label pod <pod-name> ver-```

### annotate resource:
```kubectl annotate pod <pod-name> support="Call helpdesk on 12345..."```

### remove annotation:
```kubectl annotate pod <pod-name> support-```