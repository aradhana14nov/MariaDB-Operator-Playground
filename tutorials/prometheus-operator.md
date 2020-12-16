---
title: Prometheus Operator Tutorial
description: This tutorial explains how to create Prometheus Operator
---


### Install Prometheus Operator and Deploy Prometheus Server and ServiceMonitor


***Install Prometheus operator from :*** 

Operator Hub link: https://operatorhub.io/operator/prometheus


Step 1.1: Install the Prometheus operator by running the following command:

```execute
kubectl create -f https://operatorhub.io/install/prometheus.yaml
```

This Operator will be installed in the "operators" namespace and will be usable from all namespaces in the cluster.


Step 1.2: watch your operator come up using next command:

```execute
kubectl get csv -n operators
```

Step 1.3: Get the associated Pods:

```execute
kubectl get pods -n operators
```


Note: If you are installing from operatorhub, then by default it installs the operator in operators namespace.
Below steps assumes that its deployed in operators namespace.



Step 1.4: Create below CR which will create a Prometheus Instance along with a ServiceAccount and a Service of Type NodePort :


```execute
cat <<'EOF' > prometheusInstance.yaml
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: server
  labels:
    prometheus: k8s
  namespace: operators
spec:
  replicas: 1
  serviceAccountName: prometheus-k8s
  securityContext: {}
  serviceMonitorSelector:
    matchLabels:
      app: playground  
  alerting:
    alertmanagers:
      - namespace: operators
        name: alertmanager-main
        port: web  
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


Step 1.7: Create the service to access prometheus server


```execute
cat <<'EOF' > prometheus_service.yaml
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
    app: prometheus
```


Access the service :


```
http://##DNS.ip##:30100
```



Step 1.8: Create below CR which will create Instance of ServiceMonitor:


```execute
cat <<'EOF' > ServiceMonitor.yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: mariadb-monitor 
  labels:
    app: playground
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


Step 1.10: Get the associated Pods:



```execute
kubectl get pods -n operators
```

