---
title: MariaDB Operator Tutorial
description: This tutorial explains how to access MariaDB Database
---

### Access MariaDB Database 

Execute below commands to connect with server and check for the newly created custom database (test-db)

Step1:

```execute
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
mysql -h <IP> -P 30685 -u root -ppassword
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
