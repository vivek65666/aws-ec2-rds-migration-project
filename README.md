# AWS Cloud Migration Project (EC2 + RDS MySQL)

## 🏗️ Architecture Overview
- **Before (Legacy):** Single Amazon Linux 2023 EC2 instance hosting both the web application environment and a local MySQL database server.
- **After (Target):** Production-grade architecture featuring an AWS EC2 instance cleanly separated and securely connected to a managed **Amazon RDS MySQL** database instance running inside the default VPC.

---

## 🛠️ Step-by-Step Implementation

### Phase 1: Legacy Setup & Data Backup
1. Deployed an Amazon Linux 2023 EC2 instance (`t3.micro`) acting as the legacy source environment.
2. Installed and configured MySQL locally on the instance and populated sample test records in the `app_db` database (`users` table).
3. Exported a complete database backup dump:
   ```bash
   mysqldump -u root -p app_db > backup.sql

 ###  Phase 2: Target RDS Provisioning
Created an Amazon RDS MySQL instance (target-rds-mysql) configured with standard credentials (admin) in the default VPC.

Configured security groups to allow inbound MySQL traffic (Port 3306) from the EC2 instance.

 ### Phase 3: Data Restoration & Troubleshooting
Imported the legacy SQL backup into the remote RDS database endpoint:

### Bash
mysql -h target-rds-mysql.cn0k80sqg9pp.ap-south-1.rds.amazonaws.com -P 3306 -u admin -p app_db < backup.sql
Troubleshooting Log:

Error Encountered: ERROR 1049 (42000): Unknown database 'app_db'

Resolution: Logged into the remote RDS instance via the MySQL client, executed CREATE DATABASE app_db;, and then successfully re-ran the import command.

### ✅ Verification & Operations
1. Row Count Validation
Executed row count verification queries on both environments to ensure data integrity. The matching output confirmed zero data loss:

Bash
mysql -h target-rds-mysql.cn0k80sqg9pp.ap-south-1.rds.amazonaws.com -P 3306 -u admin -p app_db -e "SELECT COUNT(*) FROM users;"
Result: 3 rows verified successfully.

2. Backup & Rollback Plan
Manual Snapshot: Created a manual RDS snapshot named migration-final-snapshot as an immutable recovery point.

Rollback Strategy: In the event of a migration failure or connection drop, the database can be instantly restored from the manual snapshot or pointed back to the local legacy backup dump.

3. Monitoring & CloudWatch Alarms
Configured baseline metric alarms for proactive operational visibility:

ec2-cpu-high: Triggers when EC2 instance CPU utilization exceeds 70%.

rds-high-connections: Triggers when RDS active database connections exceed 10.
