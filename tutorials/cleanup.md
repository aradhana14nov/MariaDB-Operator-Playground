---
title: MariaDB Operator cleanup Tutorial
description: This tutorial explains how to cleanup Operator---


### Cleaning Up Operator


***Delete the CRs of the operator's by kubectl delete commands :

kubectl delete -f <yaml file to create instance of the operator> -n <namespace>
 
Example:
 
 ```
 kubectl delete -f prometheusInstance.yaml -n operators
 ```

 
- Delete the operator by following cmds:
 
kubectl delete -f <operator's yaml file>
 
 Example:
 
 ```
 kubectl delete -f https://operatorhub.io/install/prometheus.yaml
 ```
 
- Delete the PVC:
 
 kubectl delete pvc <name-of-pvc> -n <namespace>
 
 
 Example:
 
 ```
 kubectl delete pvc mariadb-pv-claim -n my-mariadb-operator-app
 ```
 
- Delete the yaml files as well:
 
 Example:
 
  ```
  rm prometheusInstance.yaml
  ```


***Deleting the CSV resource and subscription***

- Find the csv in the same namespace:

Example:

```
kubectl get csv -n operators
```

- Delete the csv :

Example:

```
kubectl delete csv/mariadb-operator.v0.0.4 -n my-mariadb-operator-app
```

- Get the Subscription in the same namespace :

Example:

```
kubectl get subscription -n operators
```

- Delete the subscription :

Example:

```
kubectl delete subscription/my-mariadb-operator-app -n my-mariadb-operator-app
```

