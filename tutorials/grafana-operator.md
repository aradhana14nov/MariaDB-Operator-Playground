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



Step 2: After installation, verify that your operator got successfully installed by executing the below command.

```execute
kubectl get csv -n my-grafana-operator
```

You should see a similar output as below:

```output
NAME                      DISPLAY            VERSION   REPLACES                  PHASE
grafana-operator.v3.2.0   Grafana Operator   3.2.0     grafana-operator.v3.0.2   Succeeded
```

**Please wait till `PHASE` status will be `Succeeded` and then proceed further.**

After the installation is successful , you can check your operator pods by executing the below command.

```execute
kubectl get pods -n my-grafana-operator
```

You should see a pod starting with 'grafana-operator' with Ready value '1/1' and Status 'Running' like the output below.

```output
NAME                                READY   STATUS    RESTARTS   AGE
grafana-operator-7574bbdbc9-skdk8   1/1     Running   0          45s
```


Note: If you are installing from operatorhub, then by default it installs the operator in my-grafana-operator namespace.
Below steps assumes that its deployed in my-grafana-operator namespace. 


### Deploy Grafana Server Instance and Grafana Data-Source:


Step 3: Create below yaml definition of the Custom Resource to create Grafana Instance


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

You will see the following resources created:

```output
grafana.integreatly.org/example-grafana created
```




Step 5: Check pods status:

```execute
kubectl get pods -n my-grafana-operator
```

You should see a similar output as below:

```
NAME                                  READY   STATUS    RESTARTS   AGE
grafana-deployment-549c685ddc-b6dq7   1/1     Running   0          83s
grafana-operator-7574bbdbc9-skdk8     1/1     Running   0          6m4s
```



Step 6: Create below yaml definition of the Custom Resource to create Grafana Service of type NodePort:


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

Output:
```
service/grafana created
```


Step 8: Create below yaml definition of the Custom Resource to create Instance of Grafana Datasource :

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

Here we are choosing Prometheus as our datasourse.

Step 9:Execute below command to create instance of Grafana datasource:


```execute
kubectl create -f prometheus-datasources.yaml -n my-grafana-operator
```

Output:

```
grafanadatasource.integreatly.org/prometheus-grafanadatasource created
```


Step 10: Check the associated Pods:



```execute
kubectl get pods -n my-grafana-operator
```


You should see a similar output as below:

```
NAME                                  READY   STATUS    RESTARTS   AGE
grafana-deployment-549c685ddc-b6dq7   1/1     Running   0          83s
grafana-operator-7574bbdbc9-skdk8     1/1     Running   0          6m4s
```

