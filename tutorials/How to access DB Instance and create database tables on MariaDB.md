### Access MariaDB.

Execute the below commands to connect to the server and check for the newly created custom database (test-db)

```
kubectl exec -it mariadb-server-<podname> bash -n my-mariadb-operator-app
mysql -h <IP> -P 30685 -u db-user -pdb-user
show databases;
mysql -h <IP> -P 30685 -u root -ppassword
```

```
# mysql -h 192.168.29.217 -P 30685 -u db-user -pdb-user
```

where 192.168.29.217 is the minikube IP and 30685 is the configured NodePort.



