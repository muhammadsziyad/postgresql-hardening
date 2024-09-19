
# PostgreSQL Server Hardening 

## Overview

This guide provides step-by-step instructions for each method to enhance the security of your PostgreSQL 
installation.

## Hardening Techniques

### 1. Update PostgreSQL Regularly
- **Step 1:** Check for the latest PostgreSQL version from the [official PostgreSQL 
website](https://www.postgresql.org/download/).
- **Step 2:** Follow the instructions for your operating system to upgrade PostgreSQL.

### 2. Use Strong Passwords
- **Step 1:** Log in to PostgreSQL: `psql -U postgres`.
- **Step 2:** Change the postgres password: `ALTER USER postgres WITH PASSWORD 'NewStrongPassword!';`.
- **Step 3:** Update passwords for other user accounts if needed.

### 3. Remove Unused Accounts
- **Step 1:** List all PostgreSQL roles: `\du` in `psql`.
- **Step 2:** Drop unused roles: `DROP ROLE role_name;`.

### 4. Restrict Remote Access
- **Step 1:** Edit `postgresql.conf` file to listen on specific addresses:
  ```ini
  listen_addresses = 'localhost'
  ```

-   **Step 2:** Modify `pg_hba.conf` to restrict access:
    
    
    ```
    host    all             all             127.0.0.1/32            md5
    ``` 
    
-   **Step 3:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 5. Disable Remote Superuser Access

-   **Step 1:** Ensure the `pg_hba.conf` file does not allow superuser access from remote locations:
    
    
    ```
    host    all             postgres        127.0.0.1/32            md5
    ``` 
    
-   **Step 2:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 6. Use SSL/TLS for Connections

-   **Step 1:** Generate SSL certificates and configure them in `postgresql.conf`:
    
    
    ```
    ssl = on
    ssl_cert_file = 'server.crt'
    ssl_key_file = 'server.key'
    ``` 
    
-   **Step 2:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 7. Configure PostgreSQL User Privileges

-   **Step 1:** Log in to PostgreSQL: `psql -U postgres`.
-   **Step 2:** Grant specific privileges:
    
    
    ```
    GRANT SELECT, INSERT, UPDATE ON database.* TO username;
    ``` 
    
-   **Step 3:** Revoke unnecessary privileges:
    
    
    ```
    REVOKE ALL PRIVILEGES ON database.* FROM username;
    ``` 
    

### 8. Use the `pgAudit` Extension

-   **Step 1:** Install `pgAudit` extension: `CREATE EXTENSION pgaudit;`.
-   **Step 2:** Configure logging in `postgresql.conf`:
    
    
    ```
    pgaudit.log = 'all'
    ``` 
    
-   **Step 3:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 9. Enable Logging

-   **Step 1:** Edit `postgresql.conf` to enable logging:
    
    
    ```
    logging_collector = on
    log_directory = 'pg_log'
    log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
    ``` 
    
-   **Step 2:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 10. Monitor Logs

-   **Step 1:** Regularly review log files located in the `pg_log` directory.
-   **Step 2:** Use log monitoring tools to detect suspicious activities.

### 11. Limit Resource Usage

-   **Step 1:** Edit `postgresql.conf` to set resource limits:
    
    
    ```
    max_connections = 100
    work_mem = 4MB
    ``` 
    
-   **Step 2:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 12. Encrypt Data at Rest

-   **Step 1:** Enable data encryption features, if supported by your PostgreSQL version or use 
third-party solutions for encryption.

### 13. Secure PostgreSQL Configuration Files

-   **Step 1:** Set proper file permissions:
    
    
    ```
    sudo chmod 600 /etc/postgresql/*/main/postgresql.conf
    sudo chown postgres:postgres /etc/postgresql/*/main/postgresql.conf
    ``` 
    

### 14. Use Application-Level Encryption

-   **Step 1:** Implement encryption in your application code.
-   **Step 2:** Use libraries or services for encryption (e.g., AWS KMS, Azure Key Vault).

### 15. Backup Regularly

-   **Step 1:** Use `pg_dump` to create backups:
    
    
    ```
    pg_dump -U postgres dbname > backup.sql
    ``` 
    
-   **Step 2:** Schedule regular backups using `cron` or task scheduler.

### 16. Enable Point-in-Time Recovery (PITR)

-   **Step 1:** Configure continuous archiving in `postgresql.conf`:
    
    
    ```
    archive_mode = on
    archive_command = 'cp %p /path/to/archive/%f'
    ``` 
    
-   **Step 2:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 17. Use `full_page_writes`

-   **Step 1:** Ensure `full_page_writes` is enabled in `postgresql.conf`:
   
    
    ```
    full_page_writes = on
    ``` 
    
-   **Step 2:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 18. Disable `pg_stat_statements` If Not Needed

-   **Step 1:** Comment out `pg_stat_statements` in `postgresql.conf` if not needed:
   
    
    ```
    #shared_preload_libraries = 'pg_stat_statements'
    ``` 
    
-   **Step 2:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 19. Secure the PostgreSQL Data Directory

-   **Step 1:** Set directory permissions:
    
    
    ```
    sudo chmod 700 /var/lib/postgresql/your_version/main
    sudo chown -R postgres:postgres /var/lib/postgresql/your_version/main
    ``` 
    

### 20. Implement IP Whitelisting

-   **Step 1:** Configure firewall rules to allow access from specific IPs only.
-   **Step 2:** Use tools like `iptables` or cloud security groups.

### 21. Use `pgbouncer` for Connection Pooling

-   **Step 1:** Install and configure `pgbouncer` for connection pooling.
-   **Step 2:** Update your application to connect through `pgbouncer`.

### 22. Limit Connection and Query Timeouts

-   **Step 1:** Set connection and query timeouts in `postgresql.conf`:
   
    
    ```
    statement_timeout = 5min
    idle_in_transaction_session_timeout = 5min
    ``` 
    
-   **Step 2:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 23. Use PostgreSQL's Role-Based Access Control

-   **Step 1:** Create roles with specific privileges:
    
    
    ```
    CREATE ROLE readonly;
    GRANT CONNECT ON DATABASE dbname TO readonly;
    ``` 
    

### 24. Avoid Using the `postgres` User for Application Access

-   **Step 1:** Create specific roles for application access:
    
    
    ```
    CREATE ROLE appuser WITH LOGIN PASSWORD 'AppPassword!';
    GRANT CONNECT ON DATABASE dbname TO appuser;
    ``` 
    

### 25. Limit `max_connections`

-   **Step 1:** Edit `postgresql.conf` to set the maximum connections:
    
    
    ```
    max_connections = 100
    ``` 
    
-   **Step 2:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 26. Disable Unnecessary Extensions

-   **Step 1:** List installed extensions: `\dx` in `psql`.
-   **Step 2:** Drop unnecessary extensions:
    
    
    ```
    DROP EXTENSION extension_name;
    ``` 
    

### 27. Use `pg_hba.conf` for Access Control

-   **Step 1:** Configure `pg_hba.conf` to enforce authentication methods:
    
    
    ```
    host    all             all             127.0.0.1/32            md5
    ``` 
    

### 28. Change the Default Port

-   **Step 1:** Edit `postgresql.conf` to use a non-default port:
    
    
    ```
    port = 5433
    ``` 
    
-   **Step 2:** Restart PostgreSQL: `sudo systemctl restart postgresql`.

### 29. Implement Network Security Groups (NSGs)

-   **Step 1:** Configure NSGs in your cloud provider’s console to restrict traffic.

### 30. Deploy PostgreSQL in a Private Network

-   **Step 1:** Place PostgreSQL server in a private subnet within your cloud infrastructure.

### 31. Enable `log_line_prefix`

-   **Step 1:** Configure `log_line_prefix` in `postgresql.conf` for detailed logs:
    
    
    ```
    log_line_prefix = '%t [%p] %u@%d '
    ``` 
    

### 32. Avoid Storing Sensitive Data in Plaintext

-   **Step 1:** Use encryption or hashing for sensitive data.

### 33. Use Read-Only Replicas

-   **Step 1:** Set up streaming replication for read-only replicas.
-   **Step 2:** Configure replicas to be read-only by setting `default_transaction_read_only` to `on` in 
`postgresql.conf`.

### 34. Limit Administrative Privileges

-   **Step 1:** Grant administrative privileges sparingly and to trusted users only.

### 35. Monitor and Patch Vulnerabilities

-   **Step 1:** Regularly check for security patches and updates.
-   **Step 2:** Apply patches promptly to mitigate vulnerabilities.

### 36. Secure Backup Files

-   **Step 1:** Encrypt backup files and ensure they are stored securely.
-   **Step 2:** Use tools or services for backup encryption.

### 37. Configure `pg_stat_statements` Carefully

-   **Step 1:** If using `pg_stat_statements`, configure it to log only necessary queries.

### 38. Use `pg_crypto` for Cryptographic Functions

-   **Step 1:** Install and configure `pg_crypto` for encryption and hashing functions.

### 39. Regularly Review and Update Security Policies

-   **Step 1:** Periodically review and update security policies and practices.

### 40. Implement Database-Level Firewalls

-   **Step 1:** Use additional firewall layers to protect the PostgreSQL server.

### 41. Secure PostgreSQL with Host-Based Firewall

-   **Step 1:** Configure host-based firewall rules to restrict access.

### 42. Disable `pgpool` If Not Used

-   **Step 1:** Ensure `pgpool` or similar tools are disabled if not in use.

### 43. Avoid Using Deprecated Features

-   **Step 1:** Disable and avoid deprecated PostgreSQL features.

### 44. Implement Connection Limits and Timeouts

-   **Step 1:** Configure connection limits and timeouts in `postgresql.conf`:
    
    
    ```
    max_connections = 100
    idle_in_transaction_session_timeout = 5min
    ``` 
    

### 45. Use Connection Encryption

-   **Step 1:** Ensure connections are encrypted with SSL/TLS.

### 46. Avoid Using the `trust` Authentication Method

-   **Step 1:** Configure `pg_hba.conf` to use `md5` or `scram-sha-256` instead of `trust`.

### 47. Educate and Train Administrators

-   **Step 1:** Provide security training and best practices to PostgreSQL administrators.

### 48. Use Role Separation

-   **Step 1:** Implement role separation to limit access to different parts of the system.

### 49. Implement Connection Pooling

-   **Step 1:** Use tools like `pgbouncer` to manage and pool database connections.

### 50. Perform Regular Security Audits

-   **Step 1:** Conduct regular security audits and vulnerability assessments.
