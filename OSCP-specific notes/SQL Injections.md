

#### Connecting to MySQL
```
mysql -h TARGET_IP -u USERNAME -p   // Remote authentication with username and password
```

```
SELECT @@VERSION;               // Print the SQL version
SELECT system_user();           // Print the current user. Note: this is the database-specific user, it may not be the user of the same name in the system.

SHOW databases;
USE DATABASE_NAME;
SHOW tables;
```


#### MSSQL basics
```
impacket-mssqlclient USER:PASSWORD@IP_ADDRESS -windows-auth
SELECT @@VERSION;

SELECT name FROM master.dbo.sysdatabases                        // Show databases



```


