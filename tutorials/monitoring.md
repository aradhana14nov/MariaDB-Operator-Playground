### Create below CR to Enable Monitoring service to MariaDB

Step1: Create CR for MariaDB Monitoring services.

```execute
cat <<'EOF' > MariaDBmonitoring.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: Monitor
metadata:
  name: mariadb-monitor
spec:
  # Add fields here
  size: 1
  # Database source to connect with for colleting metrics
  # Format: "<db-user>:<db-password>@(<dbhost>:<dbport>)/<dbname>">
  # Make approprite changes 
  dataSourceName: "root:password@(192.168.99.127:30685)/test-db"
  # Image name with version
  # Refer https://registry.hub.docker.com/r/prom/mysqld-exporter for more details
  image: "prom/mysqld-exporter"
  EOF
```

Note: The database host and port should be correct for metrics to work.


Step2: Execute below command to Create Instance of Monitoring 

```execute
kubectl create -f MariaDBserver.yaml -n my-mariadb-operator-app
```

This CR will start Prometheus exporter pod and service. 



## Create monitoring resources :

To enable monitoring services, you need to :
1. Install Prometheus Operator and Deploy Prometheus and InstanceServiceMonitor 
2. Install Grafana Operator and Deploy Grafana, Grafana Data Source and Grafana Dashboard

If you have already with above prerequisite then you can proceed with Step2 and Step4.
Else proceed as follows: 
1. Go to Prometheus Operator Tiles of this tutorial
2. Follow Step 2
3. Go to Grafana Operator Tile of this tutorial
4. Follow Step 4


### Step1: Install Prometheus operator and Deploy Prometheus and InstanceServiceMonitor :

Install Prometheus operator from : 

Operator Hub link: https://operatorhub.io/operator/prometheus


Step 1.1: Install the Prometheus operator by running the following command:

```execute
$ kubectl create -f https://operatorhub.io/install/prometheus.yaml
```

This Operator will be installed in the "operators" namespace and will be usable from all namespaces in the cluster.


Step 1.2: watch your operator come up using next command:

```execute
$ kubectl get csv -n operators
```

Step 1.3: Get the associated Pods:

```execute
$ kubectl get pods -n operators
```


Note: If you are installing from operatorhub, then by default it installs the operator in operators namespace.
Below steps assumes that its deployed in operators namespace.


Step 1.4: Create below CR which will create a Prometheus Instance along with a ServiceAccount and a Service of Type NodePort :

```execute
cat <<'EOF' > prometheusInstance.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: prometheus
---
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: prometheus
  labels:
    prometheus: prometheus
spec:
  replicas: 2
  serviceAccountName: prometheus
  securityContext: {}
  serviceMonitorSelector: {}
  ruleSelector: {}
  alerting:
    alertmanagers:
      - namespace: monitoring
        name: alertmanager-main
        port: web
---
apiVersion: v1
kind: Service
metadata:
  name: prometheus
spec:
  type: NodePort
  ports:
  - name: web
    nodePort: 30100
    port: 9090
    protocol: TCP
    targetPort: web
  selector:
    prometheus: prometheus
EOF
```

Step 1.5: Execute below command to create Prometheus instance


```execute
kubectl create -f prometheusInstance.yaml -n operators
```

Step 1.6: Get the associated Pods:


```execute
kubectl get pods -n operators
```

Step 1.7: Access the service :


```execute
http://<IPaddress>:30100
```


Step 1.8: Create below CR which will create Instance of ServiceMonitor:


```execute
cat <<'EOF' > ServiceMonitor.yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: backend-monitor
  labels:
    k8s-app: backend-monitor
  namespace: operators 
spec:
  namespaceSelector:
    any: true
  selector:
    matchLabels:
      tier: monitor-app
  endpoints:
    - interval: 20s
      port: monitor
EOF
```

Step 1.9: Execute below command to create ServiceMonitor instance :


```execute
kubectl create -f ServiceMonitor.yaml -n operators
```

Step10: Get the associated Pods:


```execute
kubectl get pods -n operators
```


### Step 2 Verify prometheus monitoring deployment :

You can do forwarding to open prometheus UI locally.

```execute
kubectl --namespace operators port-forward svc/prometheus-operated 9090
```

Verify metrics are present at [http://localhost:9090](http://localhost:9090/)



### Step 3 Install Grafana Operator and Deploy Grafana, Grafana Data Source and Grafana Dashboard: 

Install Grafana Operator from operatorhub.

OperatorHub link: https://operatorhub.io/operator/grafana-operator

Step 3.1:

Install the Grafana operator by running the following command:

```execute
$ kubectl create -f https://operatorhub.io/install/grafana-operator.yaml
```

This Operator will be installed in the "my-grafana-operator" namespace and will be usable from this namespace only.


Step 3.2: watch your operator come up using next command.

```execute
$ kubectl get csv -n my-grafana-operator
```

Note: If you are installing from operatorhub, then by default it installs the operator in my-grafana-operator namespace.
Below steps assumes that its deployed in my-grafana-operator namespace. However you may do the changes.

#### Deploy Grafana, Grafana Data Source and Grafana Dashboard:

Step 3.3:Create below CR which will create Instance of Grafana:

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

Step 3.4:

Execute below command to create Grafana instance:

```execute
kubectl create -f GrafanaInstance.yaml -n my-grafana-operator
```


Step 3.5:

Get the associated Pods:

```execute
kubectl get pods -n my-grafana-operator
```



Step 3.6: Create below CR which will create Instance for Grafana Dashboard:

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

Step 3.7: Execute below command to create Grafana dashboard instance:

```execute
kubectl create -f simple-dashboard.json -n my-grafana-operator
```

Step 3.8: Get the associated Pods:

```execute
kubectl get pods -n my-grafana-operator
```


Step 3.9:Create below CR which will create Instance of Grafana Datasources :

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

Step 3.10: Execute below command to create Grafana datasources instance:


```execute
kubectl create -f example-datasources.yaml -n my-grafana-operator
```

Step 3.11: Get the associated Pods:


```execute
kubectl get pods -n my-grafana-operator
```


### Step 4: Verify Grafana monitoring deployment

Step1:

You can do forwarding to open grafana UI locally.

```execute
kubectl --namespace my-grafana-operator port-forward svc/grafana-service 3000
```

Step2:

Verify Grafana Datasource is created at [http://localhost:3000](http://localhost:3000/)

Import Grafana dashboard via UI. 

You can use below sample dashboard:

examples/monitoring/simple-dashboard.json




