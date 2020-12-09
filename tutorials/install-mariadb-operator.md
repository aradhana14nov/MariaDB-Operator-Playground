## Installation

### MariaDB Operator Installation

MariaDB Database uses external location on host to store all Database files. This location is default set to `/mnt/data` 
Ensure that the path exists for Database files and has all necessary permissions.


#### Execute below command to install the MariaDB operator from operatorhub:

```execute
kubectl create -f https://operatorhub.io/install/mariadb-operator-app.yaml
```

This Operator will be installed in the "my-mariadb-operator-app" namespace and will be usable from this namespace only.

Below is the yaml file used to install the operator.

**mariadb-operator-app.yaml**

```
apiVersion: v1 
kind: Namespace 
metadata: 
  name: my-mariadb-operator-app 
---
apiVersion: operators.coreos.com/v1 
kind: OperatorGroup 
metadata: 
  name: operatorgroup 
  namespace: my-mariadb-operator-app 
spec:
  targetNamespaces:
  - my-mariadb-operator-app
---
apiVersion: operators.coreos.com/v1alpha1 
kind: Subscription 
metadata: 
  name: my-mariadb-operator-app 
  namespace: my-mariadb-operator-app 
spec: 
  channel: alpha 
  name: mariadb-operator-app 
  source: operatorhubio-catalog 
  sourceNamespace: olm
```

#### After installation, watch your operator come up using next command.

```execute
kubectl get csv -n my-mariadb-operator-app
```

**Note - Please wait till `PHASE` status will be `Succeeded` and then proceed further.**
