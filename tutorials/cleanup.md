---
title: MariaDB Operator Tutorial
description: This tutorial explains how to cleanup Operator---


### Cleaning Up Operator


Step1:

#### Deleting the CSV resource and subscription

A deployment resource acts as installation instructions for pods. 
If a pod is removed, either by user intervention or because of an error on the pod itself, Kubernetes detects the difference between the desired state of the deployment and the actual number of pods.
 
In much the same way, the CSV resource acts as the installation instructions for the Operator. 
Often, a CSV indicates that a deployment must exist to fulfill this plan. 
If that deployment ceases to exist, OLM takes the necessary steps to make the actual state of the system match the CSV’s desired state.
As such, it’s not sufficient to simply delete the Operator’s deployment resource.
Instead, an Operator deployed by OLM is deleted by deleting the CSV resource.

```execute
kubectl delete csv/mariadb-operator.v0.0.4 -n my-mariadb-operator-app
```

OLM takes care of deleting the resources that the CSV created when it was originally deployed, including the Operator’s deployment resource.
Additionally, you’ll need to delete the subscription to prevent OLM from installing new CSV versions in the future:

```execute
kubectl delete subscription/my-mariadb-operator-app -n my-mariadb-operator-app
```

