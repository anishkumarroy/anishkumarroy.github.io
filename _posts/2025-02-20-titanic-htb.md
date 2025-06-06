---
title: Titanic - HackTheBox Writeup
date: 2025-02-20 16:00:00 +0530
categories: [HackTheBox]
tags: [Linux, HTB]
image: /images/titanic.png
---

> It is a easy level machine on HTB, involving LFI, gitea, and cracking PBKDF2 hashes. Hope you enjoy the writeup...

Starting with Nmap scan
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 73:03:9c:76:eb:04:f1:fe:c9:e9:80:44:9c:7f:13:46 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGZG4yHYcDPrtn7U0l+ertBhGBgjIeH9vWnZcmqH0cvmCNvdcDY/ItR3tdB4yMJp0ZTth5itUVtlJJGHRYAZ8Wg=
|   256 d5:bd:1d:5e:9a:86:1c:eb:88:63:4d:5f:88:4b:7e:04 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDT1btWpkcbHWpNEEqICTtbAcQQitzOiPOmc3ZE0A69Z
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.52
|_http-server-header: Apache/2.4.52 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://titanic.htb/
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=2/16%OT=22%CT=1%CU=35827%PV=Y%DS=2%DC=T%G=Y%TM=67B1
OS:85B9%P=x86_64-pc-linux-gnu)SEQ(SP=106%GCD=1%ISR=10B%TI=Z%CI=Z%TS=A)SEQ(S
OS:P=106%GCD=1%ISR=10B%TI=Z%CI=Z%II=I)SEQ(SP=106%GCD=1%ISR=10B%TI=Z%CI=Z%II
OS:=I%TS=A)OPS(O1=M542ST11NW7%O2=M542ST11NW7%O3=M542NNT11NW7%O4=M542ST11NW7
OS:%O5=M542ST11NW7%O6=M542ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%
OS:W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M542NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S
OS:=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%R
OS:D=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=
OS:0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U
OS:1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DF
OS:I=N%T=40%CD=S)
```

We see only two major ports open - Port 22, and Port 80.<br>
Before browsing the website, we need to add the domain to our `/etc/hosts` file.
![etchosts](/images/titanic_hosts.png)

Now, on browsing the website, we get a page, with booking service.

![booking](/images/titanic_booking.png)

On filling the details and clicking on Submit, it downloads a JSON file, which contails the details that we filled
```json
{"name": "hello", "email": "hello@hello.com", "phone": "932093092", "date": "2025-11-11", "cabin": "Deluxe"}
```
So, to understand the requests better, I opened burpsuite and captured the requests in Burp.

![request](/images/titanic_request.png)

We see, it makes a POST request, on clicking on the `Submit` button, and then it makes a GET request, fetching the ticket file.

Here, one thing that clicked my mind is to try LFI, and fetch other system files.<br>
On trying to fetch `/etc/passwd` file, it successfully worked, and I got the contents

![lfi](/images/titanic_lfi.png)

In the `/etc/passwd` file, we can see, that there is a user as `developer`

![users](/images/titanic_users.png)

So, I checked whether there is any file like `id_rsa` that will give us initial access, and looked for apache2 config files, but didn't find anything.
After that I started with VHOST scanning and directory bruteforcing.<br>
Didn't find anything interesting in directory bruteforcing, but found an interesting VHOST.

![vhost](/images/titanic_vhost.png)

Added the above vhost, to the `/etc/hosts` file too, then visited the domain.

![gitea](/images/titanic_gitea.png)

At the bottom left corner, we can see the version of Gitea. On researching a bit, found that it was already the new version of `gitea` and didn't find any exploit related to it.
Hence I registered, and found some listed repositories. 
![repo](/images/titanic_repo.png)

The last two repositories looked interesting. On opening the `docker-config` repo, found some creds related to MySQL.<br>
Moreover there was a `gitea` directory inside which there was a `docker-compose.yml` file. There we can see the gitea directory inside the /home directory of the user `developer`.

![directory](/images/titanic_dir.png)

Now, I searched for the location of the config files of `gitea`
![location](/images/titanic_conf.png)

Reading the documentation a little, figured out the path of config files ( as our gitea is hosted in the home directory inside the `data` folder hence the location will be `/home/developer/gitea/data/gitea/conf/app.ini`) - 
![config](/images/titanic_location.png)

Now, trying to access the file via the LFI vulnerability that we found earlier, got the path for the database file - 
![pathofdb](/images/titanic_database.png)

So, after that we can simply get the database by making a GET request in browser, as we won't be able to view the database contents in Burpsuite
![contents](/images/titanic_database_contents.png)

After downloading the DB file, we can view the contents in `sqlite3`. Found creds in the `user` table
![creds](/images/titanic_user.png)

It was a little confusing to understand, so I checked the table info to understand, which column has which data.

![pragma](/images/titanic_pragma.png)

So, we can just extract, our useful info from the table - 
![info](/images/titanic_info.png)

We can see the hashing algo as `PBKDF2`. To crack the hashes, we first of all need to know the format of the hashes in which they should be. Searched about it and got a great [blog](https://www.unix-ninja.com/p/cracking_giteas_pbkdf2_password_hashes) about it in a twitter post, which mentioned everything clearly.

![blog](/images/titanic_blog.png)

Saved the hashes in the format as mentioned and used `hashcat` to crack the hash for `developer` user
![hashcat](/images/titanic_hashcat.png)
![cracked](/images/titanic_cracked.png)

Using the cracked hash, tried to login via SSH as the user `developer` and it worked successfully, and hence got the user flag.

![access](/images/titanic_access.png)

Started doing enumeration, and found interesting file with SUID bit set 
![SUID](/images/titanic_suid.png)

Just running `/usr/bin/bash -p` gave us the root shell, and hence got the root flag.
