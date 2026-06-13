---
title: Connected - HackTheBox Writeup
date: 2026-06-09 16:00:00 +0530
categories: [HackTheBox]
tags: [Linux, HTB]
image: /images/connected_logo.png
---

> This was an easy-difficulty machine on Hack The Box that involved exploiting a SQL injection vulnerability (associated with a known CVE) to gain initial access. After obtaining a foothold on the target, privilege escalation to root was achieved by abusing writable files and triggering incron jobs

Starting with Nmap scan

```plaintext
PORT    STATE SERVICE  REASON         VERSION
22/tcp  open  ssh      syn-ack ttl 63 OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 4e:60:38:6f:e7:78:6c:ca:58:62:a1:f1:56:ae:8d:30 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCZBL8VwwAo5nMO8NKi+tOD98pIamOTde7sVlAJmP1Lp5urgs8hzvYIVElaEWZdxjHZM5hggtGg8Tmqzn2tOeNsN3rh/JTcXCjtc2izUuwLb18s5GgMHkBooT6UBCdcztPySFILnedHcFusfxSPTVTSIrwGaxLKULJ/qn2ClQ6BBp60NqQg0Da93fbm/5NS6OtZYdWdfcW4oyN/LWQcfFo/OYFjzWng+1pU+gfeuWic4iW2eg9qmWq43Och4oNJ3VAYh8MpXaKuoaDi+J7R6f60ADTQ6Kg/oSHKj8RV0zySax8qHt+Q2987wcdXuCnI+6oREQIUHu1s3z+rmnT2k4Mx
|   256 12:41:55:26:9d:ad:3d:e8:bf:4e:31:aa:d7:d1:a5:d2 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBH4ZlpWhdGD2bgi63EUvvzRx/sv8EvmVBLOFPVarhdPQcqCL69SyCtU0JLlNqdLxKGUbh5t1/9BvGU7+cXZdt1E=
|   256 8e:b6:96:e0:21:83:5d:1d:ce:8d:e2:6a:dd:38:c6:75 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIB5pB+WpL08kZ9YCgPA7QRnKjCsHY/R9oNeUQF1LD5Ms
80/tcp  open  http     syn-ack ttl 63 Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16)
|_http-title: Did not follow redirect to http://connected.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16
443/tcp open  ssl/http syn-ack ttl 63 Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16
|_http-title: 400 Bad Request
| http-methods: 
|_  Supported Methods: GET HEAD POST
| ssl-cert: Subject: commonName=pbxconnect/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--/organizationalUnitName=SomeOrganizationalUnit/emailAddress=root@pbxconnect/localityName=SomeCity
| Issuer: commonName=pbxconnect/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--/organizationalUnitName=SomeOrganizationalUnit/emailAddress=root@pbxconnect/localityName=SomeCity
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-11-30T14:07:27
| Not valid after:  2026-11-30T14:07:27
| MD5:     2530 86e8 e962 6d48 36f8 e524 bf79 cc5a
| SHA-1:   6997 e786 d78e 2d0a dcb4 f449 7f65 ba12 52ef 0466
| SHA-256: 46b9 6671 74f5 9939 af02 a812 993c a389 bf84 c67a de5e 94b1 6c01 43d3 fac9 b666
```

On visiting the webpage on port 80, we see that it is running FreePBX. The version for the same is visible in the website footer.

![FreePBX version](/images/connected_freepbx_version.png)

On looking for a bit, found that it is vulnerable to SQL injection. I used this [exploit](https://github.com/watchtowrlabs/watchTowr-vs-FreePBX-CVE-2025-57819/blob/main/watchTowr-vs-FreePBX-CVE-2025-57819.py)

Ran the exploit, and it uploaded a webshell. 

![exploit](/images/connected_runningexploit.png)

![PoC](/images/connected_poc_exploit.png)

I got a reverse shell connection from here now

![Initial access](/images/connected_initialaccess.png)

After performing further enumeration and running LinPEAS, I discovered that incron jobs were configured and running on the system.

![incron jobs](/images/connected_incron.png)

After looking at the incron jobs, one of them stands out, as it is sourcing a file which is writable as user `asterisk`

![Content of the incron job](/images/connected_incron_content.png)

![Writable conf](/images/connected_writableconf.png)

Now, we can append into the writable conf file, and then we can trigger that incron job by making changes to `/var/spool/asterisk/sysadmin/dahdi_restart`

![root](/images/connected_root.png)

