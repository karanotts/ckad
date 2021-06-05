# State Persistence

<strong>Volume</strong> is a directory that persists beyond pod restart and is sharable between containers in a pod.

<strong>Persistent Volume</strong> persists data to underlying physical storage.

A pod can <i>claim</i> Persistent Volume using <strong>Persistent Volume Claim</strong>.

<hr>

## kubectl 

```kubectl get pv --sort-by=.spec.capacity.storage```

## Volumes

Pods running in a container can use temporary filesystem to read and write files. If container restarts, this data is lost. This temporary filesystem can be used to ie. share log files between main app pod and sidecar pod.

<hr>

## Volume Types

```emptyDir``` - empty dir in a pod with RW access. Persists lifetime of a pod, but is lost if container is restarted.

```hostPath``` - file or dir on a host running kubernetes.

```configMap```, ```secret``` - provides a way to inject data (variables, passwords etc) into containers

```nfs`` - NFS (Network File System) share, preserves data after pod restart.

```persistentVolume```, ```persistentVolumeClaim``` - persists data after container is restarted/terminated.

<hr>

## Persistent Volumes

PersistentVolume is a storage device in k8s cluster and is decoupled from container lifecycle

PersistentVolumeClaim bids a PersistentVolume to a Pod.

### Creating PersistentVolumes
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-1
spec:
  storageClassName: local-storage
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 1Gi
  hostPath:
    path: /tmp/data
```

### Creating PersistentVolumeClaims
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-1
spec:
  storageClassName: local-storage
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
```
### Mounting PersistentVolumeClaims in a Pod
```
apiVersion: v1
kind: Pod
metadata:
  name: pod
spec:
  containers:
  - volumeMounts:
    - name: pvc-mount
      mountPath: /logs
  volumes:
  - name: pvc-mount
    persistentVolumeClaim:
      claimName: pvc-1
```