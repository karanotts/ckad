# Resource Requirements

Kubernetes allows specifying how much of a resource a container needs in form of <code>limits</code> (minimum) and <code>requests</code> (maximum).

<ul>
<li>CPU - measured in cpu units - one cpuis equivalent to 1 vCPU/Core for cloud providers and 1 hyperthread on bare-metal Intel processors.
<li>Memory - measured in bytes - expressed as a plain integer or as a fixed-point number using suffixes: E, P, T, G, M, K or the power-of-two equivalents: Ei, Pi, Ti, Gi, Mi, Ki. 
</ul>
<br>

## Namespace ResourceQuota
```
$ kubectl create -f namespace_resource_quota.yaml
resourcequota/web-quota created

$ kubectl describe namespace web
Name:         web
Status:       Active

Resource Quotas
 Name:            web-quota
 Resource         Used  Hard
 --------         ---   ---
 limits.cpu       0     4
 limits.memory    0     4096m
 pods             0     2
 requests.cpu     0     1
 requests.memory  0     1024m

No LimitRange resource.
```

If resource quota is declared on <code>namescape</code> it then <code>resources</code> requirement have to be also declared in <code>pod</code> manifest.

```
$ kubectl create -f pod_resource_requirements.yaml 
pod/webserver created

$ $ kubectl describe quota -n web
Name:            web-quota
Namespace:       web
Resource         Used   Hard
--------         ----   ----
limits.cpu       1      4
limits.memory    1024m  4096m
pods             1      2
requests.cpu     500m   1
requests.memory  512m   1024m
```
