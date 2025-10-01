Absolutely! We can structure a **practical lab series** for MySQL based on your training content. I’ll provide **step-by-step practical exercises** for each topic along with commands, configuration tips, and explanation.

I’ll break this into **practical exercises with clear steps** so you can follow along. Let’s start.

---

## **1. Architecture of MySQL**

**Objective:** Understand MySQL architecture and its components.

**Steps:**

1. Learn about the MySQL server structure:

   * **Client Layer**: MySQL client programs (mysql, mysqldump)
   * **Server Layer**: Handles connections, SQL parsing, optimization, storage engines.
   * **Storage Engine Layer**: InnoDB, MyISAM, etc.
2. **Exercise:** Identify storage engines and server version.

   ```bash
   mysql -u root -p
   SHOW VARIABLES LIKE 'version';
   SHOW ENGINES;
   ```
3. **Observation:** Note which engines are default, supported, and their characteristics.

---

## **2. Installation and Configuration**

**Objective:** Install MySQL on Linux/Windows and perform basic configuration.

**Steps:**

1. **Install MySQL (Linux Example):**

   ```bash
   sudo apt update
   sudo apt install mysql-server
   sudo systemctl start mysql
   sudo systemctl enable mysql
   ```
2. **Secure MySQL Installation:**

   ```bash
   sudo mysql_secure_installation
   ```
3. **Test Connection:**

   ```bash
   mysql -u root -p
   ```
4. **Basic Configuration Check:**

   ```sql
   SHOW VARIABLES LIKE 'datadir';
   SHOW VARIABLES LIKE 'max_connections';
   ```

---

## **3. Configuration**

**Objective:** Configure MySQL settings to optimize performance.

**Steps:**

1. Edit `my.cnf` (Linux) or `my.ini` (Windows)

   ```ini
   [mysqld]
   max_connections = 200
   innodb_buffer_pool_size = 1G
   ```
2. Restart MySQL:

   ```bash
   sudo systemctl restart mysql
   ```
3. **Verify Configuration:**

   ```sql
   SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
   SHOW VARIABLES LIKE 'max_connections';
   ```

---

## **4. The InnoDB Engine**

**Objective:** Understand InnoDB features like transactions, foreign keys, and row-level locking.

**Steps:**

1. Create an InnoDB table:

   ```sql
   CREATE DATABASE company;
   USE company;

   CREATE TABLE employees (
     emp_id INT AUTO_INCREMENT PRIMARY KEY,
     name VARCHAR(50),
     department VARCHAR(50)
   ) ENGINE=InnoDB;
   ```
2. Insert sample data and check transactions:

   ```sql
   START TRANSACTION;
   INSERT INTO employees (name, department) VALUES ('Alice', 'HR');
   ROLLBACK;
   SELECT * FROM employees;  -- Should be empty
   ```

---

## **5. Client Programs**

**Objective:** Learn MySQL client tools.

**Steps:**

1. `mysql` — Interactive command-line client:

   ```bash
   mysql -u root -p
   ```
2. `mysqldump` — Backup/restore tables:

   ```bash
   mysqldump -u root -p company > company_backup.sql
   ```
3. `mysqladmin` — Admin tasks:

   ```bash
   mysqladmin -u root -p status
   mysqladmin -u root -p processlist
   ```

---

## **6. Table Maintenance Operations**

**Objective:** Maintain tables for performance and health.

**Steps:**

1. Check table status:

   ```sql
   SHOW TABLE STATUS FROM company;
   ```
2. Optimize a table:

   ```sql
   OPTIMIZE TABLE employees;
   ```
3. Repair table (for non-InnoDB/MyISAM tables):

   ```sql
   REPAIR TABLE employees;
   ```

---

## **7. MySQL Users and Security Management**

**Objective:** Manage users and privileges.

**Steps:**

1. Create user and grant privileges:

   ```sql
   CREATE USER 'john'@'localhost' IDENTIFIED BY 'Password123!';
   GRANT SELECT, INSERT ON company.* TO 'john'@'localhost';
   FLUSH PRIVILEGES;
   ```
2. Check user privileges:

   ```sql
   SHOW GRANTS FOR 'john'@'localhost';
   ```

---

## **8. Backup and Recovery**

**Objective:** Practice backup and restore strategies.

**Steps:**

1. Backup using `mysqldump`:

   ```bash
   mysqldump -u root -p company > company_backup.sql
   ```
2. Restore backup:

   ```bash
   mysql -u root -p company < company_backup.sql
   ```

---

## **9. Replication**

**Objective:** Set up MySQL master-slave replication.

**Steps:**

1. Configure Master:

   ```ini
   [mysqld]
   server-id = 1
   log_bin = mysql-bin
   ```
2. Create replication user:

   ```sql
   CREATE USER 'repl'@'%' IDENTIFIED BY 'replpass';
   GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
   ```
3. Configure Slave:

   ```ini
   [mysqld]
   server-id = 2
   replicate-do-db = company
   ```
4. Start replication:

   ```sql
   CHANGE MASTER TO
       MASTER_HOST='master_ip',
       MASTER_USER='repl',
       MASTER_PASSWORD='replpass',
       MASTER_LOG_FILE='mysql-bin.000001',
       MASTER_LOG_POS=  107;
   START SLAVE;
   ```

---

## **10. Amazon Aurora MySQL with AWS Services**

**Objective:** Connect and interact with Aurora MySQL.

**Steps:**

1. Launch an **Aurora MySQL cluster** in AWS console.
2. Connect using MySQL client:

   ```bash
   mysql -h aurora-endpoint -u admin -p
   ```
3. Integrate with **AWS services** like:

   * S3 (LOAD DATA FROM S3)
   * Lambda (triggering events)
   * CloudWatch (monitoring)

---

## **11. Monitoring and Security Practices**

**Objective:** Monitor database and secure it.

**Steps:**

1. Enable slow query log:

   ```ini
   [mysqld]
   slow_query_log = 1
   slow_query_log_file = /var/log/mysql/slow.log
   long_query_time = 2
   ```
2. View active connections:

   ```sql
   SHOW PROCESSLIST;
   ```
3. Use `mysqladmin` to monitor status:

   ```bash
   mysqladmin -u root -p extended-status
   ```

---

## **12. Troubleshooting and Performance Tuning**

**Objective:** Optimize queries for large databases.

**Steps:**

1. Use `EXPLAIN` to analyze queries:

   ```sql
   EXPLAIN SELECT * FROM employees WHERE department='HR';
   ```
2. Indexing for performance:

   ```sql
   CREATE INDEX idx_department ON employees(department);
   ```
3. Check server status variables for tuning:

   ```sql
   SHOW GLOBAL STATUS LIKE 'Threads_connected';
   SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool%';
   ```

---

## **13. Partitioned Tables Concepts**

**Objective:** Implement partitioning for large tables.

**Steps:**

1. Create a partitioned table:

   ```sql
   CREATE TABLE sales (
     id INT,
     sale_date DATE
   )
   PARTITION BY RANGE (YEAR(sale_date)) (
     PARTITION p2022 VALUES LESS THAN (2023),
     PARTITION p2023 VALUES LESS THAN (2024)
   );
   ```
2. Insert data and query:

   ```sql
   INSERT INTO sales VALUES (1, '2023-08-01');
   SELECT * FROM sales WHERE YEAR(sale_date)=2023;
   ```

---

## **14. Upgradation and Migration**

**Objective:** Upgrade MySQL version safely.

**Steps:**

1. Backup all databases:

   ```bash
   mysqldump -u root -p --all-databases > all_backup.sql
   ```
2. Stop MySQL service, install new version, restore databases.

---

## **15. High Availability with MySQL InnoDB Cluster**

**Objective:** Setup HA using InnoDB Cluster.

**Steps:**

1. Install MySQL Shell:

   ```bash
   sudo apt install mysql-shell
   ```
2. Configure InnoDB cluster:

   ```javascript
   dba.createCluster('myCluster')
   cluster.addInstance('root@host2:3306')
   cluster.addInstance('root@host3:3306')
   cluster.status()
   ```

---

✅ These exercises cover **practical hands-on steps** for all the topics.

If you want, I can create a **full lab guide document with scripts, commands, and screenshots for each step**, so you can follow it like a workbook.

Do you want me to do that next?
