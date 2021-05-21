# Multi-container pods
Multi-container pods are pods with more than one container, working together as a single unit, which consists of the app proper and a "helper" container that is not involved in app logic, but performs helper functions.

Multi-container pods can be created by listing multiple containers in the pod definition:
```
kind: Pod
spec:
  containers:
  - name: container1
    image: busybox
  - name: container2
    image: nginx
```

When sharing a pod, containers can interact:
- by sharing a namespace - if enabled, containers within a namespaces can interact with each other's processes
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  shareProcessNamespace: true
  containers:
  - name: container1
    image: nginx
  - name: container2
    image: busybox
```

- by sharing a network 
    (ie. if ```container1``` is listening on port 1234, ```container 2``` in the same pod can access it by simply calling ```localhost:1234```, even if the port is not exposed outside of the pod)

- by sharing storage volume - same volume can be mounted to any container within a pod

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:  
  containers:
  - name: container1
    volumeMounts:
    - name: shared-volume
      mountPath: /usr/shared
  - name: container2
    volumeMounts:
    - name: shared-volume
      mountPath: /usr/shared
  volumes:
  - name: shared-volume
    emptyDir: {}
```

## Sidecar
Sidecar containers are not part of the app logic, but enchance or add app functionality in some way (file synchronisation, logging, watcher capabilities etc.)
`main app <-> sidecar`

```
    kubectl create -f sidecar.yaml
    pod/webserver created

    kubectl logs webserver -c sidecar
    (empty)

    kubectl exec webserver -c sidecar -- wget -O- localhost?unknown
    Connecting to localhost (127.0.0.1:80)
    wget: server returned error: HTTP/1.1 404 Not Found
    command terminated with exit code 1

    kubectl logs webserver -c sidecar
    Error discovered
```

## Adapter 
Adapter containers change the output from the main app container, ie. can be used to normalise logs.
`app -> output log -> adapter -> normalised output log -> log analytics app`


## Abmassador
Ambassador containers capture and/or translate network traffic. Ie. if an app has hardcoded port "1234", ambassador container can be used to (proxy) expose a different port.
`network traffic -> ambassador:80 -> translated traffic -> app:1234`

## Init containers

Init containers are ran before the app containers are started and must be run to completion before a pod can be ready. They can be used to configure containers with necessary data before app proper is deployed. 

```
    kubectl create -f init_container.yaml 
    pod/business-app created

    kubectl get pod business-app
    NAME           READY   STATUS     RESTARTS   AGE
    business-app   0/1     Init:0/1   0          3s

    kubectl get pod business-app
    NAME           READY   STATUS    RESTARTS   AGE
    business-app   1/1     Running   0          7s

    kubectl get pod business-app -o yaml
    spec:
    containers:
    - image: nginx
        name: webapp
        (...)
    initContainers:
        image: busybox
        name: configurer

    (...)

    status:
    containerStatuses:
    - containerID: docker://75f...9e0
        image: nginx:latest
        name: webapp
        state:
        running:

    initContainerStatuses:
    - containerID: docker://a66...a09
        image: busybox:latest
        name: configurer
        state:
        terminated:
            reason: Completed
```