---
title: Check the MariaDB Operator status
description: Ensure operator have been created successfully without any error
---


### Check the MariaDB Operator's status


- watch your operator come up using below command :

```execute
kubectl get csv -n my-mariadb-operator-app
```

output:

```
NAME                      DISPLAY            VERSION   REPLACES                  PHASE
mariadb-operator.v0.0.4   Mariadb Operator   0.0.4     mariadb-operator.v0.0.3   Installing
```


- Get the associated Pods using below command:

```execute
kubectl get pods -n my-mariadb-operator-app
```

output:

```
NAME                               READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-d5vgr   1/1     Running   0          100s
```

