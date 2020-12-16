---
title: Check the MariaDB Operator status
description: Ensure operator have been created successfully without any error
---


### Check the MariaDB Operator's status


- watch your operator come up using below command :

```execute
kubectl get csv -n my-mariadb-operator-app
```



- Get the associated Pods using below command:

```execute
kubectl get pods -n my-mariadb-operator-app
```


