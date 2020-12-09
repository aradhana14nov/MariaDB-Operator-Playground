### Access MariaDB.

Execute the below commands to connect to the server and check for the newly created custom database (test-db)
Step1:
```execute
kubectl exec -it mariadb-server-<podname> bash -n my-mariadb-operator-app
```
Step2:
```execute
mysql -h <IP> -P 30685 -u db-user -pdb-user
```
Step3:
```execute
show databases;
```
Step4:
```execute
mysql -h <IP> -P 30685 -u root -ppassword
```

Example:
```
# mysql -h 192.168.29.217 -P 30685 -u db-user -pdb-user
```
where 192.168.29.217 is the minikube IP and 30685 is the configured NodePort.

step5:
```execute
create database testdb;
```
step6:
```execute
use testdb;
```

step7:
```execute
create table names (name VARCHAR(100));
```

step8:
```execute
insert into names values('Abhijit');
```

step9:
```execute
select * from names;
```
