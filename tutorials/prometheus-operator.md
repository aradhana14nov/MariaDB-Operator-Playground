---
title: Prometheus Operator Tutorial
description: This tutorial explains how to create Prometheus Operator
---


### Install Prometheus Operator and Deploy Prometheus Server and ServiceMonitor


***Install Prometheus operator from :*** 

Operator Hub link: https://operatorhub.io/operator/prometheus


Step 1 : Install the Prometheus operator by running the following command:

```execute
kubectl create -f https://operatorhub.io/install/prometheus.yaml
```

Output:

```
subscription.operators.coreos.com/my-prometheus created
```

This Operator will be installed in the "operators" namespace and will be usable from all namespaces in the cluster.


- After Operator installation, verify that your operator got successfully installed by executing the below command.:

```execute
kubectl get csv -n operators
```

Output:

```
NAME                        DISPLAY               VERSION   REPLACES                    PHASE
prometheusoperator.0.37.0   Prometheus Operator   0.37.0    prometheusoperator.0.32.0   Succeeded
```

From above output, once operator is successfully installed, **PHASE** will be as "Succeeded" 

- Check the Pods status using below command::

```execute
kubectl get pods -n operators
```

Output:

```
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-6f7589ff7f-wq9zd   1/1     Running   0          8m35s
```

In above output, STATUS as "Running" shows the pods are up and running.


If you are installing from operatorhub, then by default it installs the operator in operators namespace.
Below steps assumes that its deployed in operators namespace.



Step 2: Create below yaml definition of the Custom Resource to create a Prometheus Instance along with a ServiceAccount and a Service of Type NodePort :


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


Step 3: Execute below command to create Prometheus instance



```execute
kubectl create -f prometheusInstance.yaml -n operators
```

Output:

```
prometheus.monitoring.coreos.com/server created
```


- Check the pods status using below commands:



```execute
kubectl get pods -n operators
```

Output:

```
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-6f7589ff7f-wq9zd   1/1     Running   0          14m
prometheus-server-0                    3/3     Running   1          40s
```

Step 4: Create below yaml definition of the Custom Resource to create the service to access prometheus server


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
EOF
```

- Execute below command to create Prometheus Service:

```execute
kubectl create -f prometheus_service.yaml -n operators
```

Output:

```
service/prometheus created
```

- Access the service :


Access the Prometheus dashboard using below link:

http://##DNS.ip##:30100



Step 5: Create below yaml definition of the Custom Resource to create Instance of ServiceMonitor:


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


Step 6: Execute below command to create ServiceMonitor instance :


```execute
kubectl create -f ServiceMonitor.yaml -n operators
```


- Get the associated Pods:



```execute
kubectl get pods -n operators
```

