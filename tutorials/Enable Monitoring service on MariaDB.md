### Create below CR to Enable Monitoring service to MariaDB :

### MariaDB Monitor CR

```
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
```

This CR will start Prometheus exporter pod and service. 

Note: The database host and port should be correct for metrics to work.

### Create monitoring resources :

To enable monitoring services, you need to have external Prometheus and Grafana servers deployed.

## Install Prometheus operator :

Install Prometheus operator from operator hub.

Operator Hub link: https://operatorhub.io/operator/prometheus

- ##### Execute below command to create directory for this operator

```
cd && mkdir Prometheus-Operator
cd Prometheus-Operator
```

- Install the Prometheus operator by running the following command:

```
$ kubectl create -f https://operatorhub.io/install/prometheus.yaml
```

This Operator will be installed in the "operators" namespace and will be usable from all namespaces in the cluster.

- After install, watch your operator come up using next command:

```
$ kubectl get csv -n operators
```

- Get the associated Pods:

```
$ kubectl get pods -n operators
```



Note: If you are installing from operatorhub, then by default it installs the operator in operators namespace.

Below steps assumes that its deployed in operators namespace.



#### Create below CR which will create a Prometheus Instance along with a ServiceAccount and a Service of Type NodePort :

```
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

Execute below command to create Prometheus instance

```
kubectl create -f prometheusInstance.yaml -n operators
```

Get the associated Pods:

```
kubectl get pods -n operators
```

Access the service :

```
http://<IPaddress>:30100
```



#### Create below CR which will create Instance of ServiceMonitor:

```
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

Execute below command to create ServiceMonitor instance

```
kubectl create -f ServiceMonitor.yaml -n operators
```

Get the associated Pods:

```
kubectl get pods -n operators
```

#### Verify prometheus monitoring deployment

You can do forwarding to open prometheus UI locally.

```
#kubectl --namespace operators port-forward svc/prometheus-operated 9090
```

Verify metrics are present at [http://localhost:9090](http://localhost:9090/)

## Install Grafana Operator :

Install Grafana Operator from operatorhub.

OperatorHub link: https://operatorhub.io/operator/grafana-operator

- ##### Execute below command to create directory for this operator

```
cd && mkdir my-grafana-operator
cd my-grafana-operator
```

- Install the Grafana operator by running the following command:

```
$ kubectl create -f https://operatorhub.io/install/grafana-operator.yaml
```

This Operator will be installed in the "my-grafana-operator" namespace and will be usable from this namespace only.

- After install, watch your operator come up using next command.

```
$ kubectl get csv -n my-grafana-operator
```



Note: If you are installing from operatorhub, then by default it installs the operator in my-grafana-operator namespace.

Below steps assumes that its deployed in my-grafana-operator namespace. However you may do the changes.

## Deploy Grafana, Grafana Data Source and Grafana Dashboard:

#### Create below CR which will create Instance of Grafana.

```
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

Execute below command to create Grafana instance:

```
kubectl create -f GrafanaInstance.yaml -n my-grafana-operator
```

Get the associated Pods:

```
kubectl get pods -n my-grafana-operator
```



#### Create below CR which will create Instance of Grafana Dashboard:

```
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

Execute below command to create Grafana dashboard instance:

```
kubectl create -f simple-dashboard.json -n my-grafana-operator
```

Get the associated Pods:

```
kubectl get pods -n my-grafana-operator
```



#### Create below CR which will create Instance of Grafana Datasources :

```
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

Execute below command to create Grafana datasources instance:

```
kubectl create -f example-datasources.yaml -n my-grafana-operator
```

Get the associated Pods:

```
kubectl get pods -n my-grafana-operator
```

#### Verify Grafana monitoring deployment

You can do forwarding to open grafana UI locally.

```
#kubectl --namespace my-grafana-operator port-forward svc/grafana-service 3000
```

Verify Grafana Datasource is created at [http://localhost:3000](http://localhost:3000/)

Import Grafana dashboard via UI. 

You can use below sample dashboard:

examples/monitoring/simple-dashboard.json



