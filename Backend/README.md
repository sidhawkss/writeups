# Backend
Nmap scan to discover open ports and service versions.

![Untitled](Backend/Untitled.png)

Fuzzing directory:

![Untitled](Backend/Untitled%201.png)

API route:

![Untitled](Backend/Untitled%202.png)

Docs route:

![Untitled](Backend/Untitled%203.png)

Response of the API:

![Untitled](Backend/Untitled%204.png)

Finding the signup method to register in the application: fuzzing with post method 

![Untitled](Backend/Untitled%205.png)

It needs a body field, and the content needs to be passed with JSON

![Untitled](Backend/Untitled%206.png)

Registering an account

![Untitled](Backend/Untitled%207.png)

The login isn’t in JSON, it’s in normal body post request and url encoded.

![Untitled](Backend/Untitled%208.png)

To see the documentation use the bearer token:

![Untitled](Backend/Untitled%209.png)

API documentation.

![Untitled](Backend/Untitled%2010.png)

Admin user 

![Untitled](Backend/Untitled%2011.png)

Updating password:

![Untitled](Backend/Untitled%2012.png)

Updating:

![Untitled](Backend/Untitled%2013.png)

Admin access token:

![Untitled](Backend/Untitled%2014.png)

File returned by API:

![Untitled](Backend/Untitled%2015.png)

Environment variables:

![Untitled](Backend/Untitled%2016.png)

Secret key from the code:

```python
"file": "/home/htb/uhc/app/core/config.py"
```

![Untitled](Backend/Untitled%2017.png)

Craft new JWT.

![Untitled](Backend/Untitled%2018.png)

Now we can use the route “/api/v1/admin/exec” to execute commands.

![Untitled](Backend/Untitled%2019.png)

We can find the root password in the auth.log file.

![Untitled](Backend/Untitled%2020.png)

![Untitled](Backend/Untitled%2021.png)
