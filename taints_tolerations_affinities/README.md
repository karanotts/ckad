# Taints, Tolerations, Affinities

## Taints 
Taints allow a node to repel a set of pods

Add a taint:

``` kubectl taint node <node-name> key1=value1:<taint-effect> ```
```
    kubectl taint nodes node1 app=nginx:NoSchedule
```

Remove a taint:

``` kubectl taint node <node-name> key1=value1:<taint-effect>- ```
```
    kubectl taint nodes nginx app=nginx:NoSchedule-
```

<br>

### Taint effects:

```NoSchedule``` - pods will not be scheduled on the node

```PreferNoSchedule``` - the system will try to avoid placing a pod on the node, but without guarantee

```NoExecute``` - no pod will be scheduled on the node and pods that are already running on the pod, and will immediatelly evict all pods without taint tolerations


Example error from a pod that can't be scheduled due to taint:
```
    kubectl taint node node01 spray=mortein:NoSchedule

    kubectl get pod mosquito -o wide
    NAME       READY   STATUS    RESTARTS   AGE     IP       NODE     NOMINATED NODE   READINESS GATES
    mosquito   0/1     Pending   0          7m50s   <none>   <none>   <none>           <none>

    kubectl get pod mosquito -o yaml
    ...
    status:
    conditions:
    - lastProbeTime: null
        lastTransitionTime: "2021-05-20T14:31:33Z"
        message: '0/2 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master:
        }, that the pod didn''t tolerate, 1 node(s) had taint {spray: mortein}, that
        the pod didn''t tolerate.'
        reason: Unschedulable
        status: "False"
        type: PodScheduled
    phase: Pending
  ```

## Tolerations
Tolerations are applied to pods and allow the pods to schedule onto nodes with matching taints

By default, pods have no tolerations, so can't tolerate any taints (can't be placed on a node with a taint).

Creating a pod with a toleration:
```
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
  tolerations:
  - effect: "NoSchedule"
    key: "spray"
    value: "mortein"
    operator: "Equal"
```
```
    kubectl create -f toleration.yaml
    pod/bee created

    kubectl get pods -o wide
    NAME       READY   STATUS              RESTARTS   AGE   IP       NODE     NOMINATED NODE   READINESS GATES
    bee        0/1     ContainerCreating   0          29s   <none>   node01   <none>           <none>
    mosquito   0/1     Pending             0          12m   <none>   <none>   <none>           <none>

    kubectl get pods -o wide
    NAME       READY   STATUS    RESTARTS   AGE   IP           NODE     NOMINATED NODE   READINESS GATES
    bee        1/1     Running   0          57s   10.244.1.2   node01   <none>           <none>
    mosquito   0/1     Pending   0          12m   <none>       <none>   <none>           <none>
```

## Node Affinity
Node affinities attract pods to a set of nodes.

```requiredDuringSchedulingIgnoredDuringExecution``` - "hard" affinity, rules <strong>must</strong> be met for a pod to be scheduled on the node. If the matching label doesn't exist on the node, pod will <strong>not</strong> be scheduled.

```preferredDuringSchedulingIgnoredDuringExecution``` - "soft" affinity, pod is preffered to be scheduled on the node, but not guaranteed. If the matching label doesn't exist on the node, affinity will be ignored and pod will be randomly scheduled on any available node.

In both "hard" and "soft" affinity, ```IgnoredDuringExecution``` means that already running pods will ignore any changed to node affinity. 

<br>

Code examples: 
```
  kubectl label node 361c.mylabserver.com size=large
```
```
  spec:
    affinity:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: size
              values:
              - large
              operator: In
```
```
    kubectl create -f affinity.yaml 
    pod/my-webapp created

    kubectl get pod my-webapp -o wide
    NAME        READY   STATUS    RESTARTS   AGE    IP                NODE                           NOMINATED NODE   READINESS GATES
    my-webapp   1/1     Running   0          3m4s   192.168.231.163   361c.mylabserver.com   <none>           <none>
```