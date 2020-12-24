---
title: MariaDB Operator cleanup Tutorial
description: This tutorial explains how to cleanup Operator
---


### Cleaning Up Operator




***Delete the operator's Custom Resources  by kubectl delete commands :***

 

Example:
 
 ```
 kubectl delete -f MariaDBserver.yaml -n my-mariadb-operator-app 
 ```

Note: Here MariaDBserver.yaml is the Custom Resource  of the MariaDB Server Instance.
Similarly,delete all the Custom Resource s.
 

***Delete the operator by kubectl delete command:***
 
 
 Example:
 
 ```
 kubectl delete -f https://operatorhub.io/install/mariadb-operator-app.yaml
 ```
 

***Deleting the CSV resource and subscription***

- Find the CSV in the namespace

Example:

```
kubectl get csv -n operators
```

- Delete that CSV :

Example:

```
kubectl delete csv/mariadb-operator.v0.0.4 -n my-mariadb-operator-app
```

- Get the Subscription in the same namespace 

Example:

```
kubectl get subscription -n my-mariadb-operator-app
```

- Delete the subscription :

Example:

```
kubectl delete subscription/my-mariadb-operator-app -n my-mariadb-operator-app
```


 
***Delete all the yaml files:***
 
 Example:
 
  ```
  rm -rf MariaDBserver.yaml
  ```
  


