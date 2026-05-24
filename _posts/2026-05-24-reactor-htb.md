---
title: Reactor - HackTheBox Writeup
date: 2026-09-24 16:00:00 +0530
categories: [HackTheBox]
tags: [Linux, HTB]
image: /images/reactor.png
---

> It is an easy HTB machine featuring a framework-based RCE and privilege escalation via an exposed root-owned debugging interface.

Starting with Nmap scan
```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 ce:fd:0d:82:c0:23:ed:6e:4b:ea:13:fa:4f:ea:ef:b7 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIoh32XcLYi0Kdad12SajqVyUVXfkDPaB7zZCDCMIJc+fv8JUJwyQRoqX/91+p6uD75Ggdp4VNzA7WasIkyo/4U=
|   256 f8:44:c6:46:58:7a:39:21:ef:16:44:e9:58:c2:f3:62 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPws9RyzoCW2cXzOFxeZCCt8rWcNu2umX2kqLLK6T+7H
3000/tcp open  ppp?    syn-ack ttl 63
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch, Accept-Encoding
|     x-nextjs-cache: HIT
|     x-nextjs-prerender: 1
|     x-nextjs-stale-time: 4294967294
|     X-Powered-By: Next.js
|     Cache-Control: s-maxage=31536000, 
|     ETag: "p02u6gnhufd8t"
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 17175
|     Date: Sat, 23 May 2026 19:10:50 GMT
|     Connection: close
|     <!DOCTYPE html><html lang="en"><head><meta charSet="utf-8"/><meta name="viewport" content="width=device-width, initial-scale=1"/><link rel="stylesheet" href="/_next/static/css/414e1be982bc8557.css" data-precedence="next"/><link rel="preload" as="script" fetchPriority="low" href="/_next/static/chunks/webpack-db0a529a99835594.js"/><script src="/_next/static/chunks/4bd1b696-80bcaf75e1b4285e.js" async=""></script><script src="/_next/static/chunks/517-d083b552e04dead1.js" async=""></script><script s
|   HTTPOptions: 
|     HTTP/1.1 400 Bad Request
|     vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch
|     Allow: GET
|     Allow: HEAD
|     Cache-Control: private, no-cache, no-store, max-age=0, must-revalidate
|     Date: Sat, 23 May 2026 19:10:52 GMT
|     Connection: close
|   Help, NCP, RPCCheck: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|   RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch
|     Allow: GET
|     Allow: HEAD
|     Cache-Control: private, no-cache, no-store, max-age=0, must-revalidate
|     Date: Sat, 23 May 2026 19:10:53 GMT
|_    Connection: close
```

There is a web app running on port 3000. On visiting the website, we can see, that it is just a static page.

Did some directory fuzzing, but didn't find anything. 
On further research , found that next JS 15.0.3 is using vulnerable react server components leading to unauthorized RCE (CVE-2025-55182)

Used the PoC from to get initial access - https://securitylabs.datadoghq.com/articles/cve-2025-55182-react2shell-remote-code-execution-react-server-components/

![Getting Initial Access](/images/reactor_getting_initial_access.png)

After that looked for users in the system.

![users](/images/reactor_users.png)

There are two users - `engineer` and `root`.
As we are `node` on the system we have to escalate our privilege to the `engineer` user to get the user flag

Looked in the app directory and found the sqlite database.

Connected using sqlite3 and found user hash for `engineer` and cracked it using hashcat

![hashes](/images/reactor_hashes.png)
![Hash cracked](/images/reactor_pass.png)

After that we can ssh into the system with this password as user `engineer` and hence got the user flag

![getting user access](/images/reactor_getting_user_access.png)

After further enumeration, found that a root owned node.js process was running which exposed the V8 debugging interface locally. This interface allows runtime inspection and execution of JavaScript within the context of the privileged process.

![Privileged process](/images/reactor_privileged_process.png)

We can do SSH port forwarding to interact with the port using Chrome Devtools of our system. This allows us to execute arbitrary Javascript commands directly inside the root owned Node.js process

![port forwarding](/images/reactor_ssh_portforwarding.png)

![getting root](/images/reactor_getting_root1.png)
![getting root](/images/reactor_getting_root2.png)

Got reverse shell and hence the root flag.
