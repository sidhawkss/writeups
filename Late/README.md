# Late

![Late.png](Late/Late.png)

**June 15, 2022**

Page on port 80:

![Untitled](Late/Untitled.png)

Vhost enumeration:

![Untitled](Late/Untitled%201.png)

Vhost discovered:

![Untitled](Late/Untitled%202.png)

If we send an image with a text:

![Untitled](Late/Untitled%203.png)

The response of the server is a tag with the text of the image:

![Untitled](Late/Untitled%204.png)

Knowing this action we can send a SSTI payload to look if the application responds with an execution.

Sending image:

![Untitled](Late/Untitled%205.png)

Response:

![Untitled](Late/Untitled%206.png)

Sending the payload to call the sub classes it returns other subclass that doesn’t import system.

![Untitled](Late/Untitled%207.png)

![Untitled](Late/Untitled%208.png)

Finding the location to call the class: So we need to subtract 1 to call the correct class.

![Untitled](Late/Untitled%209.png)

Payload to get the reverse shell:

![Untitled](Late/Untitled%2010.png)

![seven.jpg](Late/seven.jpg)

### Root

Looking the running process with pspy

```python
./pspy64 -f
```

![Untitled](Late/Untitled%2011.png)

Script to write the reverse shell:

![Untitled](Late/Untitled%2012.png)

Get session using SSH:

![Untitled](Late/Untitled%2013.png)

Wait the execution of the script and get the root user:

![Untitled](Late/Untitled%2014.png)