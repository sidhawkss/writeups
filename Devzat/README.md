# Devzat

![Devzat.png](Devzat/Devzat.png)

### 10/20/2021

Nmap scan:

```python
bracketz:~ $ nmap -sV 10.10.11.118
Starting Nmap 7.92 ( https://nmap.org ) at 2021-10-20 21:52 -03
Nmap scan report for 10.10.11.118
Host is up (0.41s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.41
8000/tcp open  ssh     (protocol 2.0)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8000-TCP:V=7.92%I=7%D=10/20%Time=6170BA0C%P=x86_64-pc-linux-gnu%r(N
SF:ULL,C,"SSH-2\.0-Go\r\n");
Service Info: Host: devzat.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 44.58 seconds
```

Domains:

```python
/.hta                 (Status: 403) [Size: 275]
/.htaccess            (Status: 403) [Size: 275]
/.htpasswd            (Status: 403) [Size: 275]
/assets               (Status: 301) [Size: 309] [--> http://devzat.htb/assets/]
/images               (Status: 301) [Size: 309] [--> http://devzat.htb/images/]
/index.html           (Status: 200) [Size: 6527]                               
/javascript           (Status: 301) [Size: 313] [--> http://devzat.htb/javascript/]
```

Subdomains:

![Untitled](Devzat/Untitled.png)

Connection in port 8000

![Untitled](Devzat/Untitled%201.png)

Pets subdomain has a .git vulnerability:

![Untitled](Devzat/Untitled%202.png)

Commits history:

![Untitled](Devzat/Untitled%203.png)

![Untitled](Devzat/Untitled%204.png)

Analyzing all the commits history we found a vulnerable function that calls system bash, we can parse a payload with this command and try get a shell:

![Untitled](Devzat/Untitled%205.png)

With the request set in burpsuite we send a command injection in this camp of input:

![Untitled](Devzat/Untitled%206.png)

And get a shell !

![Untitled](Devzat/Untitled%207.png)

ID_RSA key.

```python
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```

SSH forwarding advanced technique:

[https://lonesysadmin.net/2011/11/08/ssh-escape-sequences-aka-kill-dead-ssh-sessions/](https://lonesysadmin.net/2011/11/08/ssh-escape-sequences-aka-kill-dead-ssh-sessions/)

![Untitled](Devzat/Untitled%208.png)

Backup Catherine items: can be interesting.

![Untitled](Devzat/Untitled%209.png)

![Untitled](Devzat/Untitled%2010.png)

![Untitled](Devzat/Untitled%2011.png)

![Untitled](Devzat/Untitled%2012.png)

```python
influxdb 1.7.5
```

```python
e3cb20bd368ec4d78dae0760dbf33de7c7c7c4969501a25fabd55fc339dbad72

how to connect influxd

/usr/share/bash-completion/completions/passwd

/home/catherine/.profile
/usr/bin/gettext.sh
/home/catherine/.bash_history

2021-06-22 18:03:06,894 DEBUG root:39 start: subiquity/Identity/POST: {"realname": "patrick", "username": "patrick", "crypted_password": "$6$7ni9PM...
2021-06-22 18:08:26,296 - util.py[DEBUG]: Writing to /var/lib/cloud/instances/iid-datasource-none/sem/config_set_passwords - wb: [644] 24 bytes
2021-06-22 18:08:26,297 - ssh_util.py[DEBUG]: line 124: option PasswordAuthentication added with yes
2021-06-22 18:08:26,325 - cc_set_passwords.py[DEBUG]: Restarted the SSH daemon.
```

![Untitled](Devzat/Untitled%2013.png)

File password:

![Untitled](Devzat/Untitled%2014.png)

Ok, with the password we can try use the file command: The error returned say that the file doesn't exists, let's try a scape sequence.

![Untitled](Devzat/Untitled%2015.png)

With scape sequence I can read the passwd file:

![Untitled](Devzat/Untitled%2016.png)

Reading flags and finishing the machine:

![Untitled](Devzat/Untitled%2017.png)
