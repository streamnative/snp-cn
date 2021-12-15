---
title: 使用元数据工具备份和还原
id: backup-restore-metadata-tool
category: operator-guides
---

StreamNative Platform 是具有多层架构的分布式消息系统，使用 Apache ZooKeeper 作为元数据服务。StreamNative Platform 存储层和 ZooKeeper 提供了多个数据副本。为了进一步提高数据容错性和防止数据丢失，可以用元数据工具进行备份和还原。目前该工具可以将 Pulsar 集群的元数据备份到云存储或从云存储中还原元数据。

# 备份 ZooKeeper 元数据

默认情况下，备份服务是禁用的。要启用备份服务，需要将 `backup` 设置为 `true`，并在需要部署集群的 `values.yaml` 文件中对存储桶和 secret 进行相应的设置。

要备份集群的 ZooKeeper 元数据，请完成如下步骤：

1. 在 AWS S3 中创建一个存储桶以保存备份文件。
2. 创建访问 AWS S3 的凭证。
3. 用以下命令创建一个 secret 来保存 AWS 凭证：

	```bash
	kubectl create secret generic aws-secret          --from-literal=AWS_ACCESS_KEY_ID=<YOUR_ACCESS_KEY_ID>    --from-literal=AWS_SECRET_ACCESS_KEY=<YOUR_AWS_SECRET_ACCESS_KEY>
	```

4. 在 `values.yaml` 文件的 ZooKeeper customTools 部分启用备份服务。

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

5. 用 `values.yaml` 文件创建集群。

	```bash
	helm install  -f values.yaml snpe $PULSAR_CHART/  --set initialize=true --set namespace=snpe
	```

6. 检查备份服务是否正在运行。 

	```bash
	kubectl get pods snpe-restore-pulsar-backup-0
  ```

	如果状态是 `running`，则备份服务已经启用。你也可以检查存储桶中的备份文件。

# 从旧集群中还原元数据 

只有在使用旧的备份元数据来启动新的集群时，还原服务才发挥作用，而且它仅从分层储存中还原数据。如果集群正在运行，则就不能还原该集群。

默认情况下，还原服务是禁用的。要将集群还原到备份点，请完成以下步骤：

1. 通过从存储桶中查找还原文件来确定还原点，例如：`s3a://my-backuprestore/pulsar-backup-1623721488825.tar.gz`。

2. 在用于部署集群的 `values.yaml` 文件中的 ZooKeeper customTools 部分启用还原服务。

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

3. 使用  `values.yaml`  还原集群。

	```bash
	helm install  -f values.yaml snpe $PULSAR_CHART/  --set initialize=true --set namespace=snpe
	```

4. 检查还原服务并确保 ZooKeeper pod 已启动。

	```bash
	kubectl get pods | grep zookeeper
	```
	还可以检查在新集群中创建的主题和命名空间。

# 配置

下表列出了备份和还原服务的属性、相关描述和默认值。

| 属性 | 描述 | 默认值                |
| -------- | ----------- | ------------- |
| webServerPort | 用于指标和管理的备份 HTTP 服务端口。 | 8088 |
| backupInterval | 备份服务的运行时间间隔。时间单位为秒。                       | 600 |
| bucket | 保存备份文件的存储桶。                             | s3a://metadata-backup |
| backupPrefix | 备份文件名的前缀。                                           | metadata-backup |
| managedLedgerPath | ZooKeeper 中 Pulsar 管理的 ledger 的路径。         | /managed-ledgers |
| restorePoint | 还原集群的具体时间点。可以将 `restorePoint` 设置为一个云存储文件路径，以便用指定的还原点还原集群。 | |
| restoreVersion | 用于还原的唯一编号。如果想用同一个还原点多次还原 ZooKeeper，则需要为每次还原指定一个不同的还原版本号。 | 1 |

# 管理

备份服务提供了一些用于管理的 REST API。

| 端点 | 描述 | 示例 |
| -------- | ----------- | ------- |
| /backup/latest | 获得最新的备份文件 | curl localhost:8088/backup/latest |
| /backup/list | 获得所有备份文件   | curl localhost:8088/backup/list |

# 监控

备份服务提供指标端点，以监测以下指标。 

| 名称 | 类型 | 描述 |
| ---- | ---- | ----------- |
| backup_count | 计数器 | 正在运行的备份的数量。   |
| backup_failure_count | 计数器 | 失败的备份数量。         |
| backup_size_non_compressed | 标尺 | 备份文件的大小。         |
| backup_size_compressed | 标尺 | 备份压缩文件的大小。     |
| backup_size_total | 标尺 | 所有备份压缩文件的大小。 |
| backup_execution_time_total | 标尺 | 备份执行过程的时间。     |
| backup_execution_time_prepare | 标尺 | 备份准备过程的时间。     |
| backup_execution_time_compression | 标尺 | 压缩备份文件的时间。     |
| backup_execution_time_upload | 标尺 | 上传备份文件的时间。 |
