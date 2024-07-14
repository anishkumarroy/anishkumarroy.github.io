---
title: Permx - HackTheBox Writeup
date: 2024-07-15 01:00:00 +0530
categories: [HackTheBox]
tags: [Linux, HTB]
image: /images/permx.png
---

> Easy level machine on Hackthebox, involving a CVE to get initial access, and leveraging the permissions on a file to get root access

Starting off with an nmap scan 
```plaintext
# Nmap 7.94SVN scan initiated Mon Jul  8 02:06:43 2024 as: nmap -A -T4 -vvv -oN nmapscan 10.10.11.23
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
Nmap scan report for 10.10.11.23
Host is up, received echo-reply ttl 63 (0.34s latency).
Scanned at 2024-07-08 02:06:44 EDT for 75s
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 e2:5c:5d:8c:47:3e:d8:72:f7:b4:80:03:49:86:6d:ef (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAyYzjPGuVga97Y5vl5BajgMpjiGqUWp23U2DO9Kij5AhK3lyZFq/rroiDu7zYpMTCkFAk0fICBScfnuLHi6NOI=
|   256 1f:41:02:8e:6b:17:18:9c:a0:ac:54:23:e9:71:30:17 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP8A41tX6hHpQeDLNhKf2QuBM7kqwhIBXGZ4jiOsbYCI
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.52
|_http-server-header: Apache/2.4.52 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://permx.htb
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=7/8%OT=22%CT=1%CU=39732%PV=Y%DS=2%DC=T%G=Y%TM=668B8
OS:23F%P=x86_64-pc-linux-gnu)SEQ(SP=FA%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)SE
OS:Q(SP=FA%GCD=1%ISR=10C%TI=Z%CI=Z%TS=A)SEQ(SP=FA%GCD=1%ISR=10C%TI=Z%CI=Z%I
OS:I=I%TS=A)OPS(O1=M542ST11NW7%O2=M542ST11NW7%O3=M542NNT11NW7%O4=M542ST11NW
OS:7%O5=M542ST11NW7%O6=M542ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88
OS:%W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M542NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%
OS:S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%
OS:RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W
OS:=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
OS:U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%D
OS:FI=N%T=40%CD=S)

Uptime guess: 10.787 days (since Thu Jun 27 07:14:22 2024)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=250 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 5900/tcp)
HOP RTT       ADDRESS
1   312.06 ms 10.10.16.1
2   138.70 ms 10.10.11.23

Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Jul  8 02:07:59 2024 -- 1 IP address (1 host up) scanned in 76.58 seconds

```

Got two ports open. On visiting the webpage on port 80, we get - 
![desc](/images/permx_web.png)
So, we need to add `permx.htb` in our `/etc/hosts` file 
![etc_hosts](/images/permx_etc.png)
Now, on visiting the web page, we see an e-learning platform -
![e-learning](/images/permx_learn.png)
There was not much on that webpage. So I began directory fuzzing and vhost fuzzing. I found a subdomain and added that in the `/etc/hosts` file too.
![lms](/images/permx_lms.png)
![lms_hosts](/images/permx_lmshosts.png)
Visited the page, and found a login page. We can see, that it is using Chamilo which is an open-source e-learning and content management system.
![lmslogin](/images/permx_lmslogin.png)
Looked for version, but didn't find anything specific. Wappalyzer and curl request, just showed version 1.
![version](/images/permx_version.png)
On looking at the searchsploit database, found some exploits, but all of them were for authenticated session in `chamilo`.<br>
Searched for exploit related to version 1 of Chamilo, and got one - CVE-2023-4220
![exploit](/images/permx_exploit.png)
[Here](https://github.com/m3m0o/chamilo-lms-unauthenticated-big-upload-rce-poc) is the github page of the exploit.
Started a listener, and ran the exploit.
![exploit](/images/permx_run.png)
![exploit1](/images/permx_run1.png)
Got a shell as `www-data`
![www-data](/images/permx_www.png)
Now, started enumeration, and looked for files, that might have hardcoded credentials in it, and found one - 
![creds](/images/permx_creds.png)
Logged into mysql database using it, and found some hashes, but wasn't able to crack them.
Now, I checked the users in the system
![users](/images/permx_users.png)
It had two users, so tried to login as `mtz` using the password that we got in the `configuration.php` file, and got successful login.
![mtz](/images/permx_mtz.png)
On running `sudo -l` found that we have access to a file, which we can run as sudo.
![opt](/images/permx_opt.png)
Checking the contents of the file - 
![contents](/images/permx_setfacl.png)
We can see that it is using a binary `setfacl` which is used to define specific access rights to files and directories.
Now, I first tried to create a symlink for the root flag, and set read access to it for the `mtz` user, but I wasn't able to do that, probably because, we don't have access to `/root` directory itself.<br>
So, next thing we can do is set a symlink to the `/etc/sudoers` file, and change the permissions (`mtz ALL=(ALL:ALL) ALL`)for the user `mtz`.
Or, we can create a symlink of the `/etc/passwd` file and `/etc/shadow` file and then add another root in `/etc/passwd` and update the password in `/etc/shadow` and then login using that user.

Getting access with the sudoers file - 
![sudoers](/images/permx_sudoers.png)

Getting access using the passwd, and shadow file -<br>
You can generate the password for the root user, that we will create using openssl as - 
```bash
openssl passwd -1 <password_here>
```
![shadow](/images/permx_shadow.png)



