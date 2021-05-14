# Security Contexts

Security Contexts define access control and privileges within a POD or a CONTAINER.

By default, containers run with <code>root</code> privileges. As best practice, <code>Dockerfile</code> should user <code>USER</code> option to declare user ID which then can be used in container/pod declaration.
<ul>
<li> The user ID should be used to run the pod and/or container (<code>runAsUser</code>, <code>runAsGroup</code>)
<li>The group ID should be used to run the filesystem (<code>fsGroup</code>)

</ul>

<hr>

## Security Context within a POD
```
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
```
<hr>

## Security Context within a CONTAINER
Security Context applied to individual container will override settings made at pod level, but will not affect pod volumes.

```
spec:
  securityContext:
    runAsUser: 1000
  containers:
  - name: secured-container
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      runAsUser: 2000
      allowPrivilegeEscalation: false
```
```
$ kubectl create -f context_pod.yaml
pod/security-context-demo created

$ kubectl exec -it security-context-demo -c container1 -- sh
/ $ ps
PID   USER     TIME  COMMAND
    1 1000      0:00 sleep 3600
    7 1000      0:00 sh
   13 1000      0:00 ps
/ $ echo $(date) > /data/demo/date.log 
/ $ ls -la /data/demo/
total 16
drwxrwsrwx    2 root     2000          4096 May 14 10:32 .
drwxr-xr-x    3 root     root          4096 May 14 10:27 ..
-rw-r--r--    1 1000     2000            29 May 14 10:31 date.log
/ $ exit

$ kubectl exec -it security-context-demo -c container2 -- sh
/ $ ps
PID   USER     TIME  COMMAND
    1 4000      0:00 sleep 3600
    7 4000      0:00 sh
   16 4000      0:00 ps
/ $ echo $(date) > /data/demo/date.txt
/ $ ls -la /data/demo/
total 12
drwxrwsrwx    2 root     2000          4096 May 14 10:31 .
drwxr-xr-x    3 root     root          4096 May 14 10:27 ..
-rw-r--r--    1 1000     2000            29 May 14 10:31 date.log
-rw-r--r--    1 4000     2000            29 May 14 10:32 date.txt
```