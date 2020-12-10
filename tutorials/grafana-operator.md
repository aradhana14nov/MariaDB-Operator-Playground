---
title: Grafana Operator Tutorial
description: This tutorial explains how to create Grafana Operator
---



## Grafana Operator and Deploy Grafana, Grafana Data Source and Grafana Dashboard

### Install Grafana Operator and Deploy Grafana, Grafana Data Source and Grafana Dashboard: 

Install Grafana Operator from operatorhub.

OperatorHub link: https://operatorhub.io/operator/grafana-operator

Step 1: Install the Grafana operator by running the following command:

```execute
$ kubectl create -f https://operatorhub.io/install/grafana-operator.yaml
```

This Operator will be installed in the "my-grafana-operator" namespace and will be usable from this namespace only.


Step 2: watch your operator come up using next command.

```execute
$ kubectl get csv -n my-grafana-operator
```

Note: If you are installing from operatorhub, then by default it installs the operator in my-grafana-operator namespace.
Below steps assumes that its deployed in my-grafana-operator namespace. However you may do the changes.

#### Deploy Grafana, Grafana Data Source and Grafana Dashboard:

Step 3:Create below CR which will create Instance of Grafana:

```execute
cat <<'EOF' > GrafanaInstance.yaml
apiVersion: integreatly.org/v1alpha1
kind: Grafana
metadata:
  name: example-grafana
spec:
  ingress:
    enabled: true
  config:
    auth:
      disable_signout_menu: true
    auth.anonymous:
      enabled: true
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
kubectl create -f GrafanaInstance.yaml -n my-grafana-operator
```


Step 5: Get the associated Pods:

```execute
kubectl get pods -n my-grafana-operator
```



Step 6: Create below CR which will create Instance for Grafana Dashboard:

```execute
cat <<'EOF' > simple-dashboard.json
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDashboard
metadata:
  labels:
    app: grafana
  name: simple-dashboard
spec:
  json: |
    {
      "id": null,
      "title": "Simple Dashboard",
      "tags": [],
      "style": "dark",
      "timezone": "browser",
      "editable": true,
      "hideControls": false,
      "graphTooltip": 1,
      "panels": [],
      "time": {
        "from": "now-6h",
        "to": "now"
      },
      "timepicker": {
        "time_options": [],
        "refresh_intervals": []
      },
      "templating": {
        "list": []
      },
      "annotations": {
        "list": []
      },
      "refresh": "5s",
      "schemaVersion": 17,
      "version": 0,
      "links": []
    }
 
EOF
```

Step 7: Execute below command to create Grafana dashboard instance:

```execute
kubectl create -f simple-dashboard.json -n my-grafana-operator
```

Step 8: Get the associated Pods:

```execute
kubectl get pods -n my-grafana-operator
```


Step 9:Create below CR which will create Instance of Grafana Datasources :

```execute
cat <<'EOF' > example-datasources.yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDataSource
metadata:
  name: example-grafanadatasource
spec:
  datasources:
    - access: proxy
      editable: true
      isDefault: true
      jsonData:
        timeInterval: 5s
      name: Prometheus
      type: prometheus
      url: 'http://prometheus-service:9090'
      version: 1
  
EOF
```

Step 10: Execute below command to create Grafana datasources instance:


```execute
kubectl create -f example-datasources.yaml -n my-grafana-operator
```

Step 11: Get the associated Pods:


```execute
kubectl get pods -n my-grafana-operator
```

