# ConfigMaps 
## short name: ```cm``` 

ConfigMaps allow to decouple configuration artifacts from image content.<br>
Config maps store a set of key-value pairs, compeletely decoupled from a POD lifecycle.

<br><hr>

## Creating ConfigMaps imperatively

```
$ kubectl create configmap db-config --from-literal=environment=staging

$ kubectl get cm db-config -o yaml
apiVersion: v1
data:
  environment: staging
kind: ConfigMap
metadata:
  name: db-config
  namespace: default
```

```
$ kubectl create configmap game-config --from-file=sample_configs/

$ kubectl describe configmap game-config
Name:         game-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
game.properties:
----
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
secret.code.passphrase=UUDDLRLRBABAS
secret.code.allowed=true
secret.code.lives=30
ui.properties:
----
color.good=purple
color.bad=yellow
allow.textmode=true
how.nice.to.look=fairlyNice
Events:  <none>

$ kubectl get configmaps game-config -o yaml
apiVersion: v1
data:
  game.properties: |-
    enemies=aliens
    lives=3
    enemies.cheat=true
    enemies.cheat.level=noGoodRotten
    secret.code.passphrase=UUDDLRLRBABAS
    secret.code.allowed=true
    secret.code.lives=30
  ui.properties: |-
    color.good=purple
    color.bad=yellow
    allow.textmode=true
    how.nice.to.look=fairlyNice
kind: ConfigMap
metadata:
  name: game-config
  namespace: default
```
<hr>
<br>

## Creating ConfigMaps declaratively

``` 
$ kubectl create -f pod_with_cm.yaml
configmap/db-config created
pod/pod-with-cm created

$ kubectl logs pod-with-cm
postgresuser
```

``` 
$ kubectl create -f pod_with_cm_volumes.yaml
configmap/cm-volumes created
pod/pod-with-cm-vols created

$ kubectl exec pod-with-cm-vols -- cat /etc/config/db.config
DB_URL: postgresql://mydb:5432
DB_USER: postgresuser
DB_ENV: staging
```