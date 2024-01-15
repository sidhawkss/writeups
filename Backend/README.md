# Backend - HTB/UHC

Nmap scan to discover open ports and service versions.

![Untitled](Backend/Untitled.png)

Performing routes enumeration in web service.

![Untitled](Backend/Untitled%201.png)

API route:

![Untitled](Backend/Untitled%202.png)

Docs route:

![Untitled](Backend/Untitled%203.png)

Finding the signup method to register in the application, fuzz with post method.

![Untitled](Backend/Untitled%204.png)

In the body, we can send the parameters “email” and “password” to register a new user in the API.

![Untitled](Backend/Untitled%205.png)

To see the documentation we need to set the bearer token in the Authorization header.

![Untitled](Backend/Untitled%206.png)

Looking the API documentation in the browser.

![Untitled](Backend/Untitled%207.png)

Admin user 

![Untitled](Backend/Untitled%208.png)

Updating the root password using the GUID.

![Untitled](Backend/Untitled%209.png)

After change, we were able to get the administrator JWT token.

![Untitled](Backend/Untitled%2010.png)

In the API there is a route that we can use to obtain machine files.

![Untitled](Backend/Untitled%2011.png)

Listing environment variables from the server.

![Untitled](Backend/Untitled%2012.png)

After many enumerations, we obtained the Python configuration file of the project, containing the JWT secret.

```python
"file": "/home/htb/uhc/app/core/config.py"
```

![Untitled](Backend/Untitled%2013.png)

Crafting a new JWT using the secret token.

![Untitled](Backend/Untitled%2014.png)

With the debug permission, now we can use the route “/api/v1/admin/exec” to execute commands remotely.

![Untitled](Backend/Untitled%2015.png)

Reading the “auth.log” file inside UHC we can obtain the root password.

![Untitled](Backend/Untitled%2016.png)

Catching the flag.

![Untitled](Backend/Untitled%2017.png)
