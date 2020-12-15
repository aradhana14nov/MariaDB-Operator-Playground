### Create below CR to Enable Monitoring service to MariaDB

Step1: Create CR for MariaDB Monitoring services.

```execute
cat <<'EOF'> MariaDBmonitoring.yaml
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
  dataSourceName: "root:password@(67.228.231.203:30685)/testdb"
  # Image name with version
  # Refer https://registry.hub.docker.com/r/prom/mysqld-exporter for more details
  image: "prom/mysqld-exporter"
EOF
```

Note: The database host and port should be correct for metrics to work.


Step2: Execute below command to Create Instance of Monitoring 

```execute
kubectl create -f MariaDBmonitoring.yaml -n my-mariadb-operator-app
```

This CR will start Prometheus exporter pod and service. 



## Create monitoring resources :

To enable monitoring services, you need to :
1. Install Prometheus Operator and Deploy Prometheus and InstanceServiceMonitor 
2. Install Grafana Operator and Deploy Grafana, Grafana Data Source and Grafana Dashboard

If you have already with above prerequisite then you can proceed with Step2 and Step4.
Else proceed as follows: 
1. Go to Prometheus Operator Tile of this tutorial.
2. Follow Step 2.
3. Go to Grafana Operator Tile of this tutorial.
4. Follow Step 4.


### Step 1: Install Prometheus operator and Deploy Prometheus and InstanceServiceMonitor :
 
 Please go to Prometheus operator tutorial.


### Step 2: Verify prometheus monitoring deployment :

Step 2.1: You can do forwarding to open prometheus UI locally.

```execute
kubectl --namespace operators port-forward svc/prometheus-operated 9090
```

Step 2.2: Verify metrics are present at [http://##DNS.ip##:9090](http://##DNS.ip##:9090/)



### Step 3: Install Grafana Operator and Deploy Grafana, Grafana Data Source and Grafana Dashboard: 

Please go to Grafana Operator tutorial 



### Step 4: Verify Grafana monitoring deployment

Step 4.1:

You can do forwarding to open grafana UI locally.

```execute
kubectl --namespace my-grafana-operator port-forward svc/grafana-service 3000
```

Step 4.2:

Verify Grafana Datasource is created at [http://##DNS.ip##:3000](http://##DNS.ip##:3000/)

Import Grafana dashboard via UI. 

You can use below sample dashboard:

examples/monitoring/simple-dashboard.json




