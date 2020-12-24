---
title: MariaDB Create Instance  Tutorial
description: This tutorial explains how to create Instance of MariaDB Operator
---

### Create Instance of MariaDB 



***Create below yaml which will create a Custom Resource for MariaDB Server Instance and also create database called test-db, along with user credentials:***

```execute
cat <<'EOF' > MariaDBserver.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: MariaDB
metadata:
  name: mariadb
spec:
  # Keep this parameter value unchanged.
  size: 1
  
  # Root user password
  rootpwd: password

  # New Database name
  database: test-db
  # Database additional user details (base64 encoded)
  username: db-user 
  password: db-user 

  # Image name with version
  image: "mariadb/server:10.3"

  # Database storage Path
  dataStoragePath: "/mnt/data" 

  # Database storage Size (Ex. 1Gi, 100Mi)
  dataStorageSize: "1Gi"

  # Port number exposed for Database service
  port: 30685
EOF
```

***Execute below command to create MariaDBserver instance :***


```execute
kubectl create -f MariaDBserver.yaml -n my-mariadb-operator-app 
```


This CR will create a database called `test-db`, along with user credentials. The Server image name is mentioned in "image" parameter. MariaDB Database uses external location on host to store all Database files. This location is default set to `/mnt/data` from MariaDB CR file. As Database will be stored at location: '/mnt/data'.These location should exists before applying the CR. 



### Verify MariaDB Deployment



***Verify pods status:*** 


```execute
kubectl get pods -n my-mariadb-operator-app 
```

You should see a similar output as below:

```
NAME                              READY   STATUS    RESTARTS   AGE
mariadb-operator-78c95468-m824g   1/1     Running   0          118s
mariadb-server-778b9b7cb5-nt6n5   1/1     Running   0          109s
```

***Verify services:***



```execute
kubectl get svc -n my-mariadb-operator-app 
```

You should see a similar output as below:


```
NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
mariadb-operator-metrics   ClusterIP   10.98.179.105    <none>        8383/TCP,8686/TCP   101s
mariadb-service            NodePort    10.105.103.156   <none>        80:30685/TCP        73s
```

Service "mariadb-service" is a NodePort that exposes mariadb on port 3306

```execute
kubectl describe service/mariadb-service -n my-mariadb-operator-app
```

You should see a similar output as below:

```
Name:                     mariadb-service
Namespace:                mariadb
Labels:                   MariaDB_cr=mariadb
                          app=MariaDB
                          tier=mariadb
Annotations:              <none>
Selector:                 MariaDB_cr=mariadb,app=MariaDB,tier=mariadb
Type:                     NodePort
IP:                       10.100.40.161
Port:                     <unset>  80/TCP
TargetPort:               3306/TCP
NodePort:                 <unset>  30685/TCP
Endpoints:                172.17.0.5:3306
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

