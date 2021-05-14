# Service Accounts
## short name: ```sa``` 

Service accounts are meant for processes that run in pods, and allow to be created for specific tasks following the principle of least privilege.
kubect
Service accounts are namespaced.

If not assigned explicitly, a pod will use ```default``` service account.
```
$ kubectl get sa
NAME      SECRETS   AGE
default   1         18d
```

<br><hr>

## Creating Service Accounts imperatively
```
$ kubectl create serviceaccount custom
serviceaccount/custom created

$ kubectl run webserver --image=nginx --restart=Never --serviceaccount=custom
pod/webserver created

$ kubectl get pod webserver -o yaml
apiVersion: v1
kind: Pod
metadata:
  name: webserver
  namespace: default
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: webserver
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: custom-token-md4gd
      readOnly: true
  serviceAccount: custom
  serviceAccountName: custom
```
Authentication token for the service account is created automatically
```
$ kubectl describe sa custom
Name:                custom
Namespace:           default
Mountable secrets:   custom-token-md4gd
Tokens:              custom-token-md4gd

$ kubectl get secret custom-token-md4gd -o yaml
apiVersion: v1
data:
  ca.crt: LS0...LS1CRUdJT==
  namespace: ZGVmYXVsdA==
  token: ZXl...KaGJHY2Pa3c=
kind: Secret
metadata:
  annotations:
    kubernetes.io/service-account.name: custom
    kubernetes.io/service-account.uid: bda1f76e-678c-4dcd-8657-b33268242c23
  name: custom-token-md4gd
  namespace: default
type: kubernetes.io/service-account-token
```
<br><hr>

## Creating Service Accounts declaratively
```
$ kubectl create -f service_account.yaml
serviceaccount/web-service created

$ kubectl create -f pod_with_service_account.yaml
pod/busybox created
```

## Docker Registry and adding imagePullSecret to existing service account
```
$ kubectl create secret docker-registry myregistry --docker-server='https://index.docker.io/v2' \
        --docker-username=DUMMY_USERNAME --docker-password=DUMMY_DOCKER_PASSWORD \
        --docker-email=DUMMY_DOCKER_EMAIL
secret/myregistry created

$ kubectl get secrets myregistry
NAME         TYPE                             DATA   AGE
myregistry   kubernetes.io/dockerconfigjson   1      22s

$ kubectl patch sa custom -p '{"imagePullSecrets": [{"name": "myregistry"}]}'
serviceaccount/custom patched

$ $ kubectl describe sa custom
Name:                custom
Namespace:           default
Image pull secrets:  myregistry
Mountable secrets:   custom-token-md4gd
Tokens:              custom-token-md4gd
```