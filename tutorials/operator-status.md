---
title: Check the MariaDB Operator status
description: Ensure operator have been created successfully without any error
---


### Check the status of MariaDB Operator


- watch your operator come up using below command :

```execute
kubectl get csv -n my-mariadb-operator-app
```

output:

```
NAME                      DISPLAY            VERSION   REPLACES                  PHASE
mariadb-operator.v0.0.4   Mariadb Operator   0.0.4     mariadb-operator.v0.0.3   Succeeded
```

Note: Output PHASE should come as "Succeeded".

- Get the associated Pods using below command:

```execute
kubectl get pods -n my-mariadb-operator-app
```

output:

```
NAME                               READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-d5vgr   1/1     Running   0          100s
```

Note: In above output, STATUS as "Running" shows the pods are up and running.
