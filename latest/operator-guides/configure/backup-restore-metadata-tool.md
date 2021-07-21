---
title: Backup and restore with metadata tool
id: backup-restore-metadata-tool
category: operator-guides
---

StreamNative Platform is a distributed messaging system with multi-layer architecture and uses Apache ZooKeeper as the metadata service. Both StreamNative Platform storage layer and ZooKeeper provide multiple data replicas. To further enhance data fault tolerance and prevent data loss, you can backup and restore with the metadata tool. Currently, the tool enables you to backup and restore Pulsar cluster’s metadata to or from the cloud storage.

# Backup ZooKeeper metadata

By default, the backup service is disabled. To enable the backup service, you need to set the `backup` to `true`, set the bucket and secrets accordingly in the `values.yaml` file that you want to deploy your cluster. 

To backup ZooKeeper metadata of your cluster, complete the following steps:

1. Create a bucket in AWS S3 to save the backup files.
2. Create the credential to access AWS S3.
3. Create a secret to save your AWS credentials with the following command.

	```bash
	kubectl create secret generic aws-secret          --from-literal=AWS_ACCESS_KEY_ID=<YOUR_ACCESS_KEY_ID>    --from-literal=AWS_SECRET_ACCESS_KEY=<YOUR_AWS_SECRET_ACCESS_KEY>
	```

4. Enable backup service in the ZooKeeper customTools section in the `values.yaml` file.

	```yaml
 	backup:
   	  component: "backup"
      enable: true
   	  webServerPort: "8088"
	  backupInterval: "600"
      bucket: "s3a://<your-bucket-name>"
      backupPrefix: "pulsar-backup"
      managedLedgerPath: "/managed-ledgers"
      configData:
      secrets:
       	use: true
         aws:
           secretName: "aws-secret"
	```

5. Create your cluster with the `values.yaml` file.

	```bash
	helm install  -f values.yaml snpe $PULSAR_CHART/  --set initialize=true --set namespace=snpe
	```

6. Check whether the backup service is running.

	```bash
	kubectl get pods snpe-restore-pulsar-backup-0
	```
	
	If the status is `running`, the backup service starts. You can also check the backup files in your bucket.

# Restore metadata from an old cluster

The restore service works only when you start a new cluster by using the old backup metadata, and it only restores data from tiered storage. If a cluster is running, you cannot restore it.

By default, the restore service is disabled. To restore your cluster to a backup point, complete the following steps:

1. Decide the restore point by finding a restore file from your bucket, for example: `s3a://my-backuprestore/pulsar-backup-1623721488825.tar.gz`.

2. Enable the restore service in the ZooKeeper customTools section in the `values.yaml` file that you use to deploy the cluster.

	```yaml
 	restore:
  	   component: "restore"
  	   enable: true
  	   restorePoint: "s3a://<your-bucket-name>/<backup-file>"
   	   restoreVersion: "1"
   	   bucket: "s3a://<your-bucket-name>"
       configData:
       secrets:
       	use: true
         aws:
           secretName: "aws-secret"
	```

3. Restore your cluster with the `values.yaml`.

	```bash
	helm install  -f values.yaml snpe $PULSAR_CHART/  --set initialize=true --set namespace=snpe
	```

4. Check the restore service and make sure that ZooKeeper pods start.

	```bash
	kubectl get pods | grep zookeeper
	```
	You can also check the topics and namespaces created in the new cluster.

# Configuration

The following table lists the properties, description and default values of the backup and restore services.

| Property | Description | Default value |
| -------- | ----------- | ------------- |
| webServerPort | The backup HTTP service port for metrics and administration. | 8088 |
| backupInterval | The backup service running interval. The time unit is measured in seconds. | 600 |
| bucket | The bucket for saving backup files. | s3a://metadata-backup |
| backupPrefix | The prefix of the backup file name. | metadata-backup |
| managedLedgerPath | The Pulsar managed ledger path in ZooKeeper.  | /managed-ledgers |
| restorePoint | The specific time point that you want to restore your cluster. You can set the `restorePoint` to a cloud storage file path to restore the cluster with a specified restore point. | |
| restoreVersion | A unique number for restoration. If you want to restore the ZooKeeper multiple times with the same restore point, you need to specify a different restore version number for each restore. | 1 |

# Administration

The backup service provides some REST APIs for administration.

| Endpoint | Description | Example |
| -------- | ----------- | ------- |
| /backup/latest | Get the latest backup file | curl localhost:8088/backup/latest |
| /backup/list | Get all the backup files | curl localhost:8088/backup/list |

# Monitor

The backup service provides the metrics endpoint to monitor the following metrics. 

| Name | Type | Description |
| ---- | ---- | ----------- |
| backup_count | Counter | The count of running backups. |
| backup_failure_count | Counter | The count of failed backups. |
| backup_size_non_compressed | Gauge | The size of the backup files. |
| backup_size_compressed | Gauge | The size of the backup compressed file. |
| backup_size_total | Gauge | The size of all the backup compressed files. |
| backup_execution_time_total | Gauge | The time of the backup execution process. |
| backup_execution_time_prepare | Gauge | The time of the backup preparation process. |
| backup_execution_time_compression | Gauge | The time of compressing the backup files. |
| backup_execution_time_upload | Gauge | The time of uploading the backup files.|
