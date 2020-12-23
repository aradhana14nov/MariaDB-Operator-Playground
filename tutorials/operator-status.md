---
title: Check the MariaDB Operator status
description: This tutorial is to verify operator have been created successfully without any error
---


### Check the status of MariaDB Operator


- After MariaDB Operator installation, verify that your operator got successfully installed by executing the below command.

```execute
kubectl get csv -n my-mariadb-operator-app
```

output:

```
NAME                      DISPLAY            VERSION   REPLACES                  PHASE
mariadb-operator.v0.0.4   Mariadb Operator   0.0.4     mariadb-operator.v0.0.3   Succeeded
```

Note: Once operator is successfully installed, Output PHASE should be as "Succeeded".


- Check the Pods status using below command:

```execute
kubectl get pods -n my-mariadb-operator-app
```

output:

```
NAME                               READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-d5vgr   1/1     Running   0          100s
```

Note: In above output, STATUS as "Running" shows the pods are up and running.
