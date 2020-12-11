---
title: Prometheus Operator Tutorial
description: This tutorial explains how to create Prometheus Operator
---


## Prometheus Operator and Deploy Prometheus Server and InstanceServiceMonitor


### Install Prometheus Operator and Deploy Prometheus Instance and ServiceMonitor :

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


```
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

