Notes:

```
2023/11/18 13:41:01 CMD: UID=1002  PID=26147  | /usr/bin/python3.8 /home/morpheus/restore.py

-rw-rw-r-- 1 root death 51474 Aug  7 23:52 /usr/lib/python3.8/shutil.py

```

```
www-data@dreaming:/tmp$ find / -user lucien 2>/dev/null
/opt/test.py
/home/lucien
/home/lucien/.ssh
/home/lucien/.local
/home/lucien/.local/share
/home/lucien/.local/lib
/home/lucien/.local/lib/python3.8
/home/lucien/.local/lib/python3.8/site-packages
/home/lucien/.sudo_as_admin_successful
/home/lucien/.mysql_history
/home/lucien/.bash_history
/home/lucien/.profile
/home/lucien/.bash_logout
/home/lucien/.cache
/home/lucien/.bashrc
/home/lucien/lucien_flag.txt
www-data@dreaming:/tmp$ cd /opt
www-data@dreaming:/opt$ ls
getDreams.py  test.py
www-data@dreaming:/opt$ cat test.py 
import requests

#Todo add myself as a user
url = "http://127.0.0.1/app/pluck-4.7.13/login.php"
password = "HeyLucien#@1999!"

data = {
        "cont1":password,
        "bogus":"",
        "submit":"Log+in"
        }

req = requests.post(url,data=data)

if "Password correct." in req.text:
    print("Everything is in proper order. Status Code: " + str(req.status_code))
else:
    print("Something is wrong. Status Code: " + str(req.status_code))
    print("Results:\n" + req.text)

```
Found creds: `lucien:HeyLucien#@1999!
```
lucien@dreaming:/opt$ sudo -l
Matching Defaults entries for lucien on dreaming:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lucien may run the following commands on dreaming:
    (death) NOPASSWD: /usr/bin/python3 /home/death/getDreams.py
```

There is a copy of `getDreams.py` in `/opt`:
```
lucien@dreaming:/opt$ ls -la
total 16
drwxr-xr-x  2 root   root   4096 Aug 15 12:45 .
drwxr-xr-x 20 root   root   4096 Jul 28 22:35 ..
-rwxrw-r--  1 death  death  1574 Aug 15 12:45 getDreams.py
-rwxr-xr-x  1 lucien lucien  483 Aug  7 23:36 test.py

```

Found in LinPEAS output:
```
╔══════════╣ Searching passwords in history files
su root
su root
mysql -u lucien -plucien42DBPASSWORD
cat .mysql_history 
rm .mysql_history 
su root
su root
mysql -u lucien -p
sudo -l
sudo -u death /usr/bin/python3 /home/death/getDreams.py
mysql -u lucien -p
mysql -u root -p
su root
```

getDreams:
```
lucien@dreaming:/opt$ cat getDreams.py
import mysql.connector
import subprocess

# MySQL credentials
DB_USER = "death"
DB_PASS = "#redacted"
DB_NAME = "library"

import mysql.connector
import subprocess

def getDreams():
    try:
        # Connect to the MySQL database
        connection = mysql.connector.connect(
            host="localhost",
            user=DB_USER,
            password=DB_PASS,
            database=DB_NAME
        )

        # Create a cursor object to execute SQL queries
        cursor = connection.cursor()

        # Construct the MySQL query to fetch dreamer and dream columns from dreams table
        query = "SELECT dreamer, dream FROM dreams;"

        # Execute the query
        cursor.execute(query)

        # Fetch all the dreamer and dream information
        dreams_info = cursor.fetchall()

        if not dreams_info:
            print("No dreams found in the database.")
        else:
            # Loop through the results and echo the information using subprocess
            for dream_info in dreams_info:
                dreamer, dream = dream_info
                command = f"echo {dreamer} + {dream}"
                shell = subprocess.check_output(command, text=True, shell=True)
                print(shell)

    except mysql.connector.Error as error:
        # Handle any errors that might occur during the database connection or query execution
        print(f"Error: {error}")

    finally:
        # Close the cursor and connection
        cursor.close()
        connection.close()

# Call the function to echo the dreamer and dream information
getDreams()

```
Vulnerable:
```
command = f"echo {dreamer} + {dream}"
shell = subprocess.check_output(command, text=True, shell=True)
```