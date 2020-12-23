---
title: MariaDB Operator Tutorial
description: This tutorial explains how to access MariaDB Database
---

### Access MariaDB Database 

Execute below commands to connect with server and check for the newly created custom database (test-db)


- Get MariaDB Server Instance podname by using command :


```execute
kubectl get pods -n my-mariadb-operator-app
```

Output:

```
NAME                                         READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-zk56k             1/1     Running   0          2d18h
mariadb-server-5dccfb7b59-rhxkm              1/1     Running   0          2d18h
```


- Connect to MariaDB Server pod.

  Copy below command to the terminal,add the podname of MariaDB Server Instance.
    
```copycommand
 kubectl exec -it <podname> bash -n my-mariadb-operator-app
 ```


- Connect to the database using username **db-user** and password **db-user**


 ```execute
 mysql -h ##DNS.ip## -P 30685 -u db-user -pdb-user
 ```


- list database

```execute
show databases;
```


- exit the database.


```execute
exit
```


- To login through root user, use below command:


```execute
mysql -h ##DNS.ip## -P 30685 -u root -ppassword
```

- Create database testdb

```execute
create database testdb;
```


- Use the testdb to create some table 

```execute
use testdb;
```


- Create table 

```execute
create table Population(year numeric,population numeric);
```

- Insert datas into the table 

```execute
insert into Population values(2017,1380004385 );
```

```execute
insert into Population values(2018,1366417754 );
```

```execute
insert into Population values(2019,1352642280 );
```

```execute
insert into Population values(2020,1338676785 );
```


- Retrieve the data from table

```execute
select * from Population;
```

- Exit from testdb :

```execute
exit
```

- Exit from pod :

```execute
exit
```
