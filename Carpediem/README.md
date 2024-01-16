# Carpediem

Nmap scan to discover open ports and service versions.

![Untitled](Carpediem/Untitled.png)

Subdomain enumeration:

![Untitled](Carpediem/Untitled%201.png)

Directory enumeration in portal subdomain.

![Untitled](Carpediem/Untitled%202.png)

Registration form of the platform:

![Untitled](Carpediem/Untitled%203.png)

The ID of the bikes appears to have a SQL Injection vulnerability:

![Untitled](Carpediem/Untitled%204.png)

Performing union select SQL Injection.

![Untitled](Carpediem/Untitled%205.png)

Extracting data from the portal database.

![Untitled](Carpediem/Untitled%206.png)

Dumping the table:

```jsx
'union+all+select+1,group_concat(username,"\n"),3,4,5+from+portal.users--+x
```

```jsx
'union all select 1,group_concat("Username: ",username," Password: ",password," logintype: ",login_type," id: ",id," address: ",address,"\n"),3,4,5 from portal.users-- x
```

Users with passwords in MD5 format:

![Untitled](Carpediem/Untitled%207.png)

We can register the account with some attributes:

![Untitled](Carpediem/Untitled%208.png)

We changed the login type on the registration:

![Untitled](Carpediem/Untitled%209.png)

If it not work try to update the login type on the profile management:

![Untitled](Carpediem/Untitled%2010.png)

As we change the login type, after some tries to find something interesting, we can login on the administrator webpage.

![Untitled](Carpediem/Untitled%2011.png)

The profile has an page to you change the user photo and other things.

![Untitled](Carpediem/Untitled%2012.png)

I found a vulnerability on the upload photo avatar, that allow us to upload an malicious PHP file.

![Untitled](Carpediem/Untitled%2013.png)

Forward two requests and we will can see the name of the file that was uploaded:

![Untitled](Carpediem/Untitled%2014.png)

The data was interpreted:

![Untitled](Carpediem/Untitled%2015.png)

Now we can look better the disabled functions of PHP reading the PHPINFO.

![Untitled](Carpediem/Untitled%2016.png)

It doesn’t have disabled functions.

![Untitled](Carpediem/Untitled%2017.png)

Sending a PHP file with reverse shell.

![Untitled](Carpediem/Untitled%2018.png)

The reverse shell give us a container.

![Untitled](Carpediem/Untitled%2019.png)

The file on “/var/www/html/portal/classesDBConnection.php”  has the database credentials:

![Untitled](Carpediem/Untitled%2020.png)

There are two hosts on the “/etc/hosts” file.

![Untitled](Carpediem/Untitled%2021.png)

With chisel we can forward the hosts and operate using the attack machine.

![Untitled](Carpediem/Untitled%2022.png)

Connecting to chisel.

![Untitled](Carpediem/Untitled%2023.png)

The connection with MYSQL using the obtained credentials.

![Untitled](Carpediem/Untitled%2024.png)

```jsx
portaldb
J5tnqsXpyzkK4XNt
3dQXeqjMHnq4kqDv

jhammond@carpediem.htb
```

MYSQL root password:

![Untitled](Carpediem/Untitled%2025.png)

Identifying active hosts.

![Untitled](Carpediem/Untitled%2026.png)

Host one, this is the main page: 

![Untitled](Carpediem/Untitled%2027.png)

Host two:

![Untitled](Carpediem/Untitled%2028.png)

Host three:

![Untitled](Carpediem/Untitled%2029.png)

Host four:

![Untitled](Carpediem/Untitled%2030.png)

Host five:

![Untitled](Carpediem/Untitled%2031.png)

Host six:

![Untitled](Carpediem/Untitled%2032.png)

The host four has a mongodb exposed service.

![Untitled](Carpediem/Untitled%2033.png)

After installing the mongo on the attack machine, we can connect on mongodb and dump the data:

```jsx
proxychains mongo 172.17.0.4:27017
```

![Untitled](Carpediem/Untitled%2034.png)

Data from admin:

![Untitled](Carpediem/Untitled%2035.png)

System sessions.

![Untitled](Carpediem/Untitled%2036.png)

Other sessions.

![Untitled](Carpediem/Untitled%2037.png)

There is a new domain called “truedesk”.

![Untitled](Carpediem/Untitled%2038.png)

Acessing Truedesk page.

![Untitled](Carpediem/Untitled%2039.png)

On the trudesk documentation we can look how to use the access_token stored in the database.

![Untitled](Carpediem/Untitled%2040.png)

Requesting the API:

![Untitled](Carpediem/Untitled%2041.png)

Command to display JSON on a readable way:

```jsx
curl -H "accesstoken: 22e56ec0b94db029b07365d520213ef6f5d3d2d9" -l "http://trudesk.carpediem.htb/api/v1/users" | node -e "console.log( JSON.stringify( JSON.parse(require('fs').readFileSync(0) ), 0, 1 ))"
```

To create an account we need to discover the role ID and the group ID to assign the user.
On the Truedesk documentation we can se the parameters to create an user: [https://docs.trudesk.io/v1/api/#Ticket](https://docs.trudesk.io/v1/api/#Ticket)

![Untitled](Carpediem/Untitled%2042.png)

Roles and groups ids:

![Untitled](Carpediem/Untitled%2043.png)

After send the request the account will be created.

![Untitled](Carpediem/Untitled%2044.png)

Login on the platform with success!

![Untitled](Carpediem/Untitled%2045.png)

Create a new user with all groups permission to show the tickets:

![Untitled](Carpediem/Untitled%2046.png)

Based on this message we can known that the machine has an VoiP system

![Untitled](Carpediem/Untitled%2047.png)

VOIP port 5060 is open.

![Untitled](Carpediem/Untitled%2048.png)

Trying to login on Zoiper:

![Untitled](Carpediem/Untitled%2049.png)

Credentials to login:

```jsx
User: 9650@10.10.11.167:5060
Pass: 2022
```

Now we can try to call the number *62 described on the message.

![Untitled](Carpediem/Untitled%2050.png)

Type the password on the call again:

![Untitled](Carpediem/Untitled%2051.png)

Now we call, listen the message and write the obtained password from voicemail: AuRj4pxq9qPk

![Untitled](Carpediem/Untitled%2052.png)

The pattern of email is the first + middle name.

![Untitled](Carpediem/Untitled%2053.png)

After sending the password on SSH, we were able to obtain the session.

![Untitled](Carpediem/Untitled%2054.png)

The Linpeas reported a SSL cert.

![Untitled](Carpediem/Untitled%2055.png)

TCPDUMP capabilities.

![Untitled](Carpediem/Untitled%2056.png)

Inspecting the traffic with TCPDUMP:

![Untitled](Carpediem/Untitled%2057.png)

We can try decrypt the packets data using the certificate.

![Untitled](Carpediem/Untitled%2058.png)

Collect some that from the docker interface with TCP dump and analyze with Wireshark.

![Untitled](Carpediem/Untitled%2059.png)

I found another packet that is a login on the panel.

![Untitled](Carpediem/Untitled%2060.png)

```jsx
jpardella
tGPN6AmJDZwYWdhY
```

To get a reverse shell on the application, you can download a theme and add a reverse shell. I crafted 3 reverse-shells, my own ip, the IP of the hacked machine and the [localhost](http://localhost) of the hacked machine. 

![Untitled](Carpediem/Untitled%2061.png)

Reverse shells.

![Untitled](Carpediem/Untitled%2062.png)

Zip the theme again:

![Untitled](Carpediem/Untitled%2063.png)

Upload the theme:

![Untitled](Carpediem/Untitled%2064.png)

After upload the theme, click on “Enable newly added themes”.

![Untitled](Carpediem/Untitled%2065.png)

Set as default:

![Untitled](Carpediem/Untitled%2066.png)

After request the main page, the reverse shell will be spawned getting the use root.

![Untitled](Carpediem/Untitled%2067.png)

Database config on settings.php file:

![Untitled](Carpediem/Untitled%2068.png)

```jsx
mysql://backdrop:34tB8RGtgtJjZ2Tz@localhost/backdrop
```

The login on mysql with the credentials.

![Untitled](Carpediem/Untitled%2069.png)

Searching a lot I found a new Docker escape technique that matches with our requirements. [https://unit42.paloaltonetworks.com/cve-2022-0492-cgroups/](https://unit42.paloaltonetworks.com/cve-2022-0492-cgroups/)

Script running

![Untitled](Carpediem/Untitled%2070.png)

Create a index.php file:

![Untitled](Carpediem/Untitled%2071.png)

Cronjob running the script:

![Untitled](Carpediem/Untitled%2072.png)

Writing a new index.php file.

![Untitled](Carpediem/Untitled%2073.png)

After some seconds the script was executed and a root session was obtained.