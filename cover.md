<h1 align="center">Mariadb Operator</h1>

![Logo](_images/logo.PNG)



### Overview:

Mariadb Operator enables you to create MariaDB server and database easily by defining simple Custom Resource.The MariaDB Operator enables customers to deploy MariaDB Platform in a variety of database configurations in a Kubernetes environment and is a key technology for MariaDB’s cloud strategy.

### Operator's features are as follows:

- Setup a MariaDB server with configured version

- Create a custom database along with a user credential set for the custom database

- Operator uses Persistent Volume where MariaDB can write its data files

- Seamless upgrades of MariaDB is possible without loosing existing data

- Take full backup of Database at user defined location.

- Schedule backup at regular intervals.

- Monitor metrics for mariadb node.


### Objective of tutorial

In this tutorial,we are going to cover following topics:

1. How to Install MariaDB Operator and verify its successful installation.
2. Create Instance Of MariaDB Operator and verify status of pods and services.
3. How to access DB Instance and create database tables on MariaDB
4. How to Schedule backup of MariaDB at regular intervals
5. Enable monitoring services on MariaDB Server using Prometheus and Grafana Operator.
6. Cleanup Operators
  
