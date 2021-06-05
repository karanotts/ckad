# Services and Networking

## Services

Services are abstractive layer that provide network access to resources.

The sets of pods targeted by the service are usually specified by pod ```selector```, whereas services connecting other kinds of backends (ie. a database or a different k8s cluster) don't require selectors. Services without a selector require manual mapping.

```network traffic -> service -> pods```


```
apiVersion: v1
kind: Service
metadata:
  name: webservice
spec:
  selector:
    tier: frontend
  type: ClusterIp
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 80
```
```spec.type``` - service type:
    <li>```ClusterIp``` - the service is exposed <strong>internally</strong> within the cluster using internal IP or cluster DNS
    <li>```NodePort``` - the service is exposed <strong>externally</strong> via node port ```<NodeIP>:<NodePort>```
    <li>```LoadBalancer``` - used specifically on cloud clusters, to expose service via cloud platform load balancer
    <li>```ExternalName``` - maps the service to extrernal address to allow resources to access other resources outside the cluster

```spec.selector``` - pod selector, specifies pods associated with this service

```spec.ports.port``` - service port, a port on which the service will listen

```spec.ports.targetPort``` - pod port, a port on the pod/s to which service will forward traffic; if not provided will be set as same as ```port```

```spec.ports.nodePort``` - port on the ```NodePort``` - if not provided will be set automagically in range 30000-32700

Create a service:

```kubectl create service <service-type> <service-name> --<protocol>=<port>:<targetPort>```
```
    kubectl create service clusterip my-service --tcp=80:8080 

    kubectl get services
    NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
    my-service   ClusterIP   10.110.206.68   <none>        80/TCP    14m
```

Create a pod with a service using ```--expose```:
```
    kubectl run webserver --image=nginx --labels=app=nginx,tier=frontend --port=80 --expose 
    service/webserver created
    pod/webserver created

    kubectl get services
    NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
    webserver    ClusterIP   10.102.92.26   <none>        80/TCP    16s

    kubectl get endpoints webserver
    NAME        ENDPOINTS            AGE
    webserver   192.168.231.137:80   24s
```

Create a deployment and then create a service using ```kubectl expose deployment```:
```
    kubectl create deployment webserver-2 --image=nginx --replicas=3
    deployment.apps/webserver-2 created

    kubectl expose deployment webserver-2 --port=8080 --target-port=8080 --type=NodePort
    service/webserver-2 exposed

    kubectl get services
    NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
    webserver-2   NodePort    10.104.62.175   <none>        8080:30885/TCP   10s

    kubectl get endpoints webserver-2
    NAME          ENDPOINTS                                                     AGE
    webserver-2   192.168.231.138:8080,192.168.97.124:8080,192.168.97.74:8080   19s
```

## Network Policies

Network policies allow controlling what traffic is allowed in and out of the pods.

```
  kubectl create -f network-policy.yaml

  kubectl describe netpol network-policy
  Name:         network-policy
  Namespace:    default
  Created on:   2021-05-28 13:17:10 +0000 UTC
  Labels:       <none>
  Annotations:  <none>
  Spec:
    PodSelector:     app=secure-app
    Allowing ingress traffic:
      To Port: 80/TCP
      From:
        PodSelector: allow-access=true
    Allowing egress traffic:
      To Port: 80/TCP
      To:
        PodSelector: allow-access=true
    Policy Types: Ingress, Egress
```

## Network Policy selectors

```podSelector``` - matches traffic to and from pods

```namespaceSelector``` - matches traffic to and from pods within namespace.

```ipBlock``` - specifies IP ranges to match the rule.
