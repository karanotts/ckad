```kubectl create secret generic mysql-secret --from-literal=mysql-pass=p4ssw0rd --dry-run=client -o yaml > secret.yaml```

(note: do not keep secret files on the server! this is just an example)

kubectl create -f mysql-secret.yaml
secret/mysql-secret created

kubectl create -f mysql-pvc.yaml 
persistentvolumeclaim/mysql-pvc created

kubectl create -f mysql-deployment.yaml 
deployment.apps/wordpress-mysql created

kubectl expose deployment wordpress-mysql --port=3306 --cluster-ip=None --dry-run=client -o yaml > mysql-service.yaml
kubectl create -f mysql-service.yaml 
service/wordpress-mysql created

kubectl create -f wordpress-pvc.yaml 
persistentvolumeclaim/wordpress-pvc created

kubectl create -f wordpress-deployment.yaml 
deployment.apps/wordpress created

kubectl get secrets
NAME                  TYPE                                  DATA   AGE
mysql-secret          Opaque                                1      19m