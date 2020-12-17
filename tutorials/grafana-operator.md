---
title: Grafana Operator Tutorial
description: This tutorial explains how to create Grafana Operator
---


### Install Grafana Operator and Deploy Grafana, Grafana Data Source and Grafana Dashboard: 


Install Grafana Operator from operatorhub.

OperatorHub link: https://operatorhub.io/operator/grafana-operator


Step 1: Install the Grafana operator by running the following command:


```execute
kubectl create -f https://operatorhub.io/install/grafana-operator.yaml
```


This Operator will be installed in the "my-grafana-operator" namespace and will be usable from this namespace only.



Step 2: watch your operator come up using next command.


```execute
kubectl get csv -n my-grafana-operator
```


Note: If you are installing from operatorhub, then by default it installs the operator in my-grafana-operator namespace.
Below steps assumes that its deployed in my-grafana-operator namespace. 


### Deploy Grafana, Grafana Data Source and Grafana Dashboard:


Step 3:Create below CR which will create Grafana Server Instance:


```execute
cat <<'EOF' > grafana-server.yaml
apiVersion: integreatly.org/v1alpha1
kind: Grafana
metadata:
  name: mariadb-grafana
spec:
  ingress:
    enabled: true
  config:
    auth:
      disable_signout_menu: false 
    auth.anonymous:
      enabled: false
    log:
      level: warn
      mode: console
    security:
      admin_password: secret
      admin_user: root
  dashboardLabelSelector:
    - matchExpressions:
        - key: app
          operator: In
          values:
            - grafana
EOF
```


Step 4: Execute below command to create Grafana instance:


```execute
kubectl create -f grafana-server.yaml -n my-grafana-operator
```


Step 5: Check the associated Pods:


```execute
kubectl get pods -n my-grafana-operator
```

Step 6: Create below CR which will create services to Grafana :


```execute
cat <<'EOF' > grafana_service.yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana
spec:
  type: NodePort
  ports:
  - name: web
    nodePort: 30101
    port: 9090
    protocol: TCP
    targetPort: 3000
  selector:
    app: grafana
EOF
```

Step 7:Execute below command to create grafana Service:

```execute
kubectl create -f grafana_service.yaml -n my-grafana-operator
```


Step 8:Create below CR which will create Instance of Grafana Datasources :

```execute
cat <<'EOF' > prometheus-datasources.yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDataSource
metadata:
  name: prometheus-grafanadatasource
spec:
  datasources:
    - access: proxy
      editable: true
      isDefault: true
      jsonData:
        timeInterval: 5s
      name: Prometheus
      type: prometheus
      url: 'http://prometheus-operated.operators:9090'
      version: 1
  name: prometheus-datasources.yaml  
EOF
```


Step 9: Execute below command to create Grafana datasources instance:


```execute
kubectl create -f prometheus-datasources.yaml -n my-grafana-operator
```


Step 10: Check the associated Pods:



```execute
kubectl get pods -n my-grafana-operator
```

