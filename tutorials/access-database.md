### Access MariaDB.

Execute the below commands to connect to the server and check for the newly created custom database (test-db)
Step1:
```execute
kubectl exec -it <podname> bash -n my-mariadb-operator-app
```
Step2:
```execute
mysql -h <IP> -P 30685 -u db-user -pdb-user
```
```
Note: <IP> is kubernetes cluster IP here.
```
  
Step3:
```execute
show databases;
```
```
Note: In order to create database and table login with root user using below command.
```
Step4:
```execute
mysql -h <IP> -P 30685 -u root -ppassword
```

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
