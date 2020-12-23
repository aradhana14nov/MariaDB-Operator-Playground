---
title: Enable Monitoring to MariaDB
description: This tutorial explains how to Enable Monitoring service to MariaDB
---

### Enable Monitoring service on MariaDB 

Step1: Create below yaml to create CR for MariaDB Monitoring services.

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
  dataSourceName: "root:password@(##DNS.ip##:30685)/test-db"
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

1. Install Prometheus Operator and Deploy Prometheus Instance and ServiceMonitor 

2. Install Grafana Operator and Deploy Grafana, Grafana Data Source and Grafana Dashboard

If above prerequisites are already fullfilled, you can proceed with Step2 and Step4.

Else proceed as follows: 


### Step 1: Install Prometheus operator and Deploy Prometheus and InstanceServiceMonitor :
 
 Please go to Prometheus operator tutorial.


### Step 2: Verify prometheus monitoring deployment :


Verify metrics are present at [http://##DNS.ip##:30100](http://##DNS.ip##:30100/)



### Step 3: Install Grafana Operator and Deploy Grafana ServerGr and Gafana Data Source: 


Please go to Grafana Operator tutorial 



### Step 4: Verify Grafana dashboard is accessible


Verify Grafana Dashboard is accessible at [http://##DNS.ip##:30101](http://##DNS.ip##:30101/)





