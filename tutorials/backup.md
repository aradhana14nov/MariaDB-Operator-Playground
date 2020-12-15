---
title: MariaDB Backup Tutorial
description: This tutorial explains how to schedule backup of MariaDB
---

### MariaDB Backup 

### Create the below CR which will schedule backup of MariaDB at defined schedule : 

#### MariaDBBackup.yaml

```execute
cat <<'EOF' > MariaDBBackup.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: Backup
metadata:
  name: mariadb-backup
spec:
  # Backup Path
  backupPath: "/mnt/backup"

  # Backup Size (Ex. 1Gi, 100Mi)
  backupSize: "1Gi" 

  # Schedule period for the CronJob.
  # This spec allow you setup the backup frequency
  # Default: "0 0 * * *" # daily at 00:00
  schedule: "0 0 * * *"
EOF
```

Execute below command to create MariaDBBackup instance:

```execute
kubectl create -f MariaDBBackup.yaml -n my-mariadb-operator-app
```

This CR will schedule backup of MariaDB at defined schedule. The Database backup files will be stored at location: '/mnt/backup'. 
As Backup Files will be stored at location: '/mnt/backup'. This location should be created before applying the CR. 

To Ensure that cronjob is configured correctly, run below command:

```execute
# kubectl get cronjob -n my-mariadb-operator-app
```
Output:
```
NAME             SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
mariadb-backup   0 0 * * *   False     0        <none>          17m
```

At scheduled interval, a new Job will start to take database backup and create a backup file at defined location (default: /mnt/backup)

### Setup Instructions

Database backup files are stored at default location "/mnt/backup" and can be configured in Backup CR file. Ensure that this paths exist and have all necessary permissions.

Check if there are no existing Persistent Volumes defined for same locations. If so, delete those PVs before applying CRs



