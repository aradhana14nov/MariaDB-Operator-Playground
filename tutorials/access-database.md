---
title: MariaDB Operator Tutorial
description: This tutorial explains how to access MariaDB Database
---

### Access MariaDB Database 

Execute below commands to connect with server and check for the newly created custom database (test-db)

Step1: 

Get MariaDB Server Instance podname by using command :


```execute
kubectl get pods -n my-mariadb-operator-app
```execute


Output:

```
NAME                                         READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-zk56k             1/1     Running   0          2d18h
mariadb-server-5dccfb7b59-rhxkm              1/1     Running   0          2d18h
```


Copy below command and execute by adding MariaDB Server Instance podname.

```copycommand
kubectl exec -it <podname> bash -n my-mariadb-operator-app
```

Step2:

```execute
mysql -h ##DNS.ip## -P 30685 -u db-user -pdb-user
```
```
Note: IP is kubernetes cluster IP here.
```
  
Step3:
```execute
show databases;
```
```
Note: In order to create database table, login with root user creds using below command.
```

Step4:
```execute
exit
```

Step5:
```execute
mysql -h ##DNS.ip## -P 30685 -u root -ppassword
```

step6:
```execute
create database testdb;
```

step7:
```execute
use testdb;
```

step8:
```execute
create table names (name VARCHAR(100));
```

step9:
```execute
insert into names values('Abhijit');
```

step9:
```execute
select * from names;
```
