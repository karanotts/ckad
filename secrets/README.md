# Secrets

Secrets allow storing sensitive information.
<br>


### Opaque - defautl type of Secret (can be ommitted in file declaration)

<br><hr>

## Creating Secrets imperatively
``` 
$ kubectl create secret generic db-credentials --from-literal=db_password=Pa$$w0rd

$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
db-credentials        Opaque                                1      18s
```

``` 
$ create secret generic ssh-key --from-file=id_rsa=/home/cloud_user/.ssh/id_rsa

$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
ssh-key               Opaque                                1      28s
```

<hr>
<br>

## Creating Secrets declaratively

To create opaque secrets, first the data needs to be base64 encoded, then encoded value used in yaml declaration

```
$ echo -n 'Pa$$w0rd' | base64
UGEkJHcwcmQ=
```

```
$ kubectl create -f secret.yaml
secret/db-password created

$ kubectl describe secret db-password
Name:         db-password
Namespace:    default
Type:  Opaque
Data
====
db_password:  8 bytes
```

! After creating the object, file `secret.yaml` should be deleted, as it contains sensitive data!

<hr>
<br>

## Consuming Secrets
```
$ kubectl create -f pod_with_secret_keyref.yaml
$ kubectl get pod pod-with-secret -o=custom-columns="ENVS:.spec.containers[*].env"
ENVS
[map[name:DB_PASSWORD valueFrom:map[secretKeyRef:map[key:db_password name:db-secrets]]] map[name:DB_ADMIN valueFrom:map[secretKeyRef:map[key:db_admin_user name:db-secrets]]]]
```

```
$ kubectl create -f pod_with_secret_volume.yaml
pod/pod-with-secret-vol created
$ kubectl exec pod-with-secret-vol -- cat /etc/config/passwords/db_password
Pa$$w0rd
```

```
$ kubectl create -f ppod_with_secret_envvars.yaml
pod/pod-with-secrets-from-env-vars created
$ kubectl exec pod-with-secrets-from-env-vars -- env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=pod-with-secrets-from-env-vars
db_admin_user=secret_admin
db_password=Pa$$w0rd
```
<hr>
<br>

## Editing Secrets

``` 
echo -n "n3wPa$$w0rd" | base64
$ kubectl edit secrets db-password
secret/db-password edited
```