# Trick

![Trick.png](Trick/Trick.png)

**Jun 19, 2022.**

Doing a network port scanning to look the open ports on the machine:

![Untitled](Trick/Untitled.png)

Zone transfer using the trick.htb domain we can discover other subdomain of the machine, the “prepod-payroll.trick.htb”

![Untitled](Trick/Untitled%201.png)

Bypass authentication using SQL Injection.

![Untitled](Trick/Untitled%202.png)

LFI using the PHP wrapper to explore the machine:

![Untitled](Trick/Untitled%203.png)

Reading the database password from the “db_connect” file.

![Untitled](Trick/Untitled%204.png)

Identifying the number of rows using the SQL injection

![Untitled](Trick/Untitled%205.png)

Writing a SQL Injection query to write the output in a file:

```python
php://filter/convert.base64-encode/resource=
```

![Untitled](Trick/Untitled%206.png)

The web-shell worked writing the file on TMP folder.

![Untitled](Trick/Untitled%207.png)

Spawning a reverse shell using the curl command getting the file from the our machine.

![Untitled](Trick/Untitled%208.png)

With the past credentials obtained, we will try to authenticate on michael user.

![Untitled](Trick/Untitled%209.png)

```python

SuperGucciRainbowCake
TrulyImpossiblePasswordLmao123
```

There are some interesting VHOST on the web-server configuration.

![Untitled](Trick/Untitled%2010.png)

Using the VHOST obtained we can access other server and explore a LFI vulnerability .

![Untitled](Trick/Untitled%2011.png)

In this LFI we can use the web-shell written in last exploitation.

![Untitled](Trick/Untitled%2012.png)

Creating a reverse shell to call.

![Untitled](Trick/Untitled%2013.png)

After the execution the reverse shell was obtained.

![Untitled](Trick/Untitled%2014.png)

The user michael can run fail2ban as root.

![Untitled](Trick/Untitled%2015.png)

What is fail2ban?

![Untitled](Trick/Untitled%2016.png)

We can write files on this folder of actions.

![Untitled](Trick/Untitled%2017.png)

Loop to append “multiport.conf” file with +s permission in /bin/bash

![Untitled](Trick/Untitled%2018.png)

Restart fail2ban

![Untitled](Trick/Untitled%2019.png)

Brute some passwords with hydra

![Untitled](Trick/Untitled%2020.png)

After the execution of fail2ban the permissions of “/bin/bash” was changed. 

![Untitled](Trick/Untitled%2021.png)

Root session

![Untitled](Trick/Untitled%2022.png)