# Undetected

![Undetected.png](Undetected/Undetected.png)

**May 28, 2022**

Nmap scan to discover open ports and service versions.

![Untitled](Undetected/Untitled.png)

First contact with the website:

![Untitled](Undetected/Untitled%201.png)

The button redirects to another subdomain:

![Untitled](Undetected/Untitled%202.png)

store.djewlry.htb

Scanning the directories with fuzz-bo0m it returns a interesting PATH: The return was a PHP/eval-stdin.php and this have a vulnerability.

![Untitled](Undetected/Untitled%203.png)

[https://packetstormsecurity.com/files/cve/CVE-2017-9841](https://packetstormsecurity.com/files/cve/CVE-2017-9841)

[https://web.archive.org/web/20170701212357/http://phpunit.vulnbusters.com/](https://web.archive.org/web/20170701212357/http://phpunit.vulnbusters.com/)

We can execute PHP commands using POST request and sending a PHP data:

![Untitled](Undetected/Untitled%204.png)

Reading readable files of own user:

```jsx
find / -readable 2>&-
```

Interesting file in /var/backups:

![Untitled](Undetected/Untitled%205.png)

Download the file to analyze:

![Untitled](Undetected/Untitled%206.png)

Is interesting to see what is this section of code:

![Untitled](Undetected/Untitled%207.png)

Decoding

![Untitled](Undetected/Untitled%208.png)

Cracking the hash of the decode, the result was:

```jsx
ihatehackers
```

Login in SSH

![Untitled](Undetected/Untitled%209.png)

On the apache2 modules folder the mod_read.so object was changed on a different day.

```python
/usr/lib/apache2/modules
```

![Untitled](Undetected/Untitled%2010.png)

Is interesting look the strings of this shared object but the machine doesn’t have the strings binary, so we need to download and do the strings command:

The binary appears to have a base64:

![Untitled](Undetected/Untitled%2011.png)

Was done some changes in SSHD file:

![Untitled](Undetected/Untitled%2012.png)

The -d option is used to set a string: 

![Untitled](Undetected/Untitled%2013.png)

The -r of the date binary is:

![Untitled](Undetected/Untitled%2014.png)

Now let’s download the SSHD file to analyze.

![Untitled](Undetected/Untitled%2015.png)

Interesting section of code of a backdoor activation:

![Untitled](Undetected/Untitled%2016.png)

Backdoor code:

![Untitled](Undetected/Untitled%2017.png)

It compares the password with the backdoor

![Untitled](Undetected/Untitled%2018.png)

So we need to mount this hex to dump in the order

```python
0x5b
0xa9f4
0xbcf0b5e3
0xb2d6f4a0fda0b3d6
0xfdb3d6e7
0xf7bbfdc8
0xa4b3a3f3
0xf0e7abd6
```

Change the endianness according to the array size, and xor based on the xor on the code.

![Untitled](Undetected/Untitled%2019.png)

This is the password of the root user, just login in and get the root flag.

```jsx
ssh root@10.10.11.146
```