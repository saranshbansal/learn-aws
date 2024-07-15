# Usecases of all AWS database solutions

| Data Store         | When to Use                                                                                                                                  |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Database on EC2    | Ultimate control over database <br> Preferred DB not available under RDS                                                                     |
| Amazon RDS         | Need traditional relational database for OLTP <br> Your data is well formed and structured <br> Existing apps requiring RDBMS                |
| Amazon DynamoDB    | Name/value pair data or unpredictable data structure <br> In-memory performance with persistence <br>  High I/O needs <br> Scale dynamically |
| Amazon RedShift    | Massive amounts of data <br> Primarily OLAP workloads                                                                                        |
| Amazon Neptune     | Relationships between objects a major portion of data value                                                                                  |
| Amazon Elasticache | Fast temporary storage for small amounts of data <br> Highly volatile data                                                                   |
| Amazon S3          | BLOBs <br> Static Websites                                                                                                                   |

# Amazon RDS
Amazon Relational Database Service (Amazon RDS) is a managed service that you can use to launch and manage relational databases on AWS.

Amazon RDS is an Online Transaction Processing (OLTP) type of database.

It is best suited to structured, relational data store requirements.

**Managed Service:** Amazon RDS is a fully managed service that automates database management tasks such as provisioning, patching, backup, and recovery, allowing users to focus more on their applications.

Database Engines Supported: Amazon RDS supports various popular relational database engines:

```
MySQL
PostgreSQL
MariaDB
Oracle Database
Microsoft SQL Server
Amazon Aurora (MySQL and PostgreSQL compatible)
```

**Scalability:** Amazon RDS makes it easy to scale your database deployment both vertically (by changing instance types) and horizontally (by adding read replicas for read scalability).

**High Availability:** RDS provides high availability through automated backups, automated failover, and Multi-AZ deployments for supported database engines (such as MySQL, PostgreSQL, and SQL Server).

**Security:** Amazon RDS offers built-in security features to help control access to your databases:

- Encryption at rest using AWS Key Management Service (KMS)
- Network isolation using Amazon VPC (Virtual Private Cloud)
- SSL/TLS encrypted connections
- IAM database authentication

**Monitoring and Metrics:** Amazon RDS provides monitoring and metrics through Amazon CloudWatch, which allows you to monitor database performance metrics such as CPU utilization, storage capacity, and more.

**Backup and Restore:** Automated backups are enabled by default with a retention period of up to 35 days. You can also take manual snapshots for backup and restore purposes.

**Performance:**

Amazon RDS uses EBS volumes (never uses instance store) for DB and log storage.

There are three storage types available: 
- General Purpose (gp2) - Moderate I/O workloads upto 3000 IOPS
- Provisioned IOPS (SSD) - I/O intensite workloads upto 80k IOPS
- Magnetic - Old and not recommended max 1000 IOPS

## DB Instance
A DB instance is a virtual database environment in the cloud with the compute and storage resources you specify.

DB instances are available in different instance types that vary in compute, memory, and storage capacity. Examples include `db.t3.micro`, `db.r5.large`, etc. Instance types can be chosen based on the workload and performance requirements of your application.

DB instances can be deployed in `Multi-AZ (Availability Zone)` configurations. This provides high availability and automatic failover capability in case of infrastructure failures.

DB instances support vertical scaling by changing to a larger instance type with more compute power and memory. Horizontal scaling is achieved through the use of `read replicas` for read scalability.

## Multi-AZ Deployments:
**Purpose:** Multi-AZ (Availability Zone) deployments in Amazon RDS are designed to enhance the availability and durability of database instances by automatically replicating data to a standby instance in a different Availability Zone within the same AWS region.

**High Availability:** In the event of a planned maintenance event, DB instance failure, or an Availability Zone outage, Amazon RDS can automatically failover to the standby instance, minimizing downtime.

**Synchronous Replication:** Multi-AZ deployments use synchronous replication to keep data consistent between the primary and standby instances. This ensures that data is durable and available with minimal latency impact.

**Supported Database Engines:** Multi-AZ deployments are available for certain database engines such as MySQL, PostgreSQL, and SQL Server.

**Automatic Failover:** Amazon RDS handles automatic failover from the primary to the standby instance in case of failure or maintenance, reducing the need for manual intervention and improving application resilience.

**Cost:** Multi-AZ deployments typically incur higher costs compared to single-AZ deployments due to the additional infrastructure and data replication overhead.

## Read Replicas:
**Purpose:** Read Replicas in Amazon RDS allow you to create one or more copies of your database instance within the same AWS region or even across different regions.

**Scaling Read Workloads:** Read Replicas are used primarily to offload read-only queries from the primary database instance, improving overall performance and scalability for read-heavy workloads.

**Asynchronous Replication: **Data replication to Read Replicas is asynchronous, meaning there might be slight replication lag compared to the primary instance. However, this allows for greater scalability without impacting the primary workload.

**Use Cases:** Read Replicas are beneficial for applications that require frequent read operations such as reporting, analytics, and scaling read-heavy workloads like web applications.

**Supported Database Engines:** Read Replicas are supported for `MySQL`, `PostgreSQL`, `MariaDB`, and `Aurora` (`MySQL` and `PostgreSQL` compatible).

**Promotion to Standalone Instance:** In case of a primary instance failure, a Read Replica can be promoted to become a standalone instance, but it loses its replication relationship and becomes independent.

## DB Snapshots:
**Purpose:** DB Snapshots in Amazon RDS are `point-in-time` backups of your database instance stored in `Amazon S3`. They capture the entire state of your database at the moment the snapshot is taken.

**Manual Creation:** DB Snapshots can be manually created using the AWS Management Console, CLI, or API. They are user-initiated and provide a way to create backups before making significant changes to your database.

**Retention:** DB Snapshots are retained until explicitly deleted by the user. You can keep snapshots for as long as needed to comply with regulatory requirements or for disaster recovery purposes.

**Storage Cost:** Storing DB Snapshots incurs storage costs based on the size of the database at the time the snapshot was taken.

**Restore Operations:** You can restore a database instance from a DB Snapshot, which recreates the database using the snapshot's data. This is useful for recovering from accidental data loss or corruption.

## DB Backups:
**Automated Backups:** Amazon RDS enables automated backups by default, which are automatically taken daily during a user-defined backup window.

**Retention Period:** Automated backups are retained for a specified retention period, which can be set between 1 to 35 days. These backups are incremental, capturing changes since the last full backup.

**Point-in-Time Recovery: **With automated backups, you can perform point-in-time recovery to restore your database to any second during the retention period. This helps in recovering to specific moments before data loss or corruption occurred.

**Storage Cost:** Storage for automated backups is managed by Amazon RDS and does not count towards your provisioned storage limit. However, it incurs storage costs based on the volume of data stored.

**Manual Snapshots:** In addition to automated backups, you can manually create DB Snapshots for additional backups or to retain backups for longer periods beyond the automated retention period.