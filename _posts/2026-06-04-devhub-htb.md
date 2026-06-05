---
title: DevHub - HackTheBox Writeup
date: 2026-05-30 16:00:00 +0530
categories: [HackTheBox]
tags: [Linux, HTB]
image: /images/devhub_icon.png
---

>This medium-rated HTB machine begins with an RCE vulnerability in MCP, followed by privilege escalation through privileged processes. The final step involves exploiting a tool-calling function of MCP to obtain the root user's SSH key and gain full system access.

# ------------------------ PROTECTED CONTENT -----------------------------

<!-- Starting with Nmapscan

```plaintext
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.15 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 35:78:2e:79:0d:87:13:05:2f:53:8e:e7:3c:55:b6:4c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPWeIVAL8xAfqZkJzRocGOpKCgXQk807PgJQqBcvDCiTcyFYlXvFY0v+sI1XXnYKghVRDkCxYy23sjlFMceuifE=
|   256 dd:56:8e:bc:da:b8:38:3e:9a:cd:0b:74:ee:53:85:f8 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAWDVyu6UXTR8XbXiFXOJx0xwUVCRheT9hT20o1VbEht
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://devhub.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running (JUST GUESSING): Linux 4.X|5.X|2.6.X|3.X (97%), MikroTik RouterOS 7.X (87%)
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:6 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 4.15 - 5.19 (97%), Linux 5.0 - 5.14 (97%), Linux 2.6.32 - 3.13 (91%), Linux 3.10 - 4.11 (91%), Linux 3.2 - 4.14 (91%), Linux 5.14 - 6.8 (91%), Linux 2.6.32 - 3.10 (91%), Linux 4.19 - 5.15 (91%), OpenWrt 22.03 (Linux 5.10) (90%), Linux 4.15 (88%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.99%E=4%D=5/31%OT=22%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=6A1BC393%P=x86_64-pc-linux-gnu)
SEQ(SP=108%GCD=1%ISR=107%TI=Z%II=I%TS=A)
SEQ(SP=FC%GCD=1%ISR=104%TI=Z%II=I%TS=A)
OPS(O1=M542ST11NW7%O2=M542ST11NW7%O3=M542NNT11NW7%O4=M542ST11NW7%O5=M542ST11NW7%O6=M542ST11)
WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)
ECN(R=Y%DF=Y%TG=40%W=FAF0%O=M542NNSNW7%CC=Y%Q=)
T1(R=Y%DF=Y%TG=40%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=Y%DF=Y%TG=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
U1(R=N)
IE(R=Y%DFI=N%TG=40%CD=S)
```

Visited the webpage on port 80

![homepage](/images/devhub_port.png)

Here we can see, that it is hinting towards port 6274 where MCP inspector is deployed. Visiting port 6274, we find the MCPJam Inspector, which is used to build and test MCP servers. The version running was - 1.4.2

![MCP](/images/devhub_mcp_jam_version.png)

After looking for exploit, found that it is vulnerable to an unauthenticate RCE CVE-2026-23744.
Used this [exploit](https://sploitus.com/exploit?id=PACKETSTORM:217697) to get initial access 

![Initial Access](/images/devhub_initial_access.png)

Users in the machine - 

![Users](/images/devhub_users.png)

We need to escalate our privileges to the `analyst` user. After some enumeration, found that a jupyter-lab process was running as the user `analyst`, which also leaks the token. We can leverage that to get shell from the jupyter-lab console if we forward that instance to our machine. 

![jupyter](/images/devhub_jupyter.png)

I used chisel for port forwarding

![Chisel](/images/devhub_chisel_port_forwarding.png)

After that we can acess the jupyter-lab instance on our system locally. We can execute the reverse shell payload to get shell as analyst 

![Analyst access](/images/devhub_initial_access1.png)

Earlier, when we saw the processes, running as analyst, we also saw a `/opt/opsmcp/server.py` script running. We didn't have access to it, as `mcp-dev` user. As we are `analyst` now, we can take a look into the script 

I found interesting info from the script, that can give us the SSH key of the root user. It is a MCP tool server, and it has a tool to get the SSH keys for the root user. We can just call that tool with the required headers to obtain the SSH key.

![key](/images/devhub_imp_details.png)

![Function](/images/devhub_imp_details1.png)

![ssh tool](/images/devhub_imp_details2.png)

We can now make a curl requested with the required headers to get the SSH key 

![SSH key](/images/devhub_gettingSSHKey.png)

![Formatting](/images/devhub_sshkey.png)

Got Root access 

![Root](/images/devhub_rootaccess.png) -->
