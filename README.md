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
