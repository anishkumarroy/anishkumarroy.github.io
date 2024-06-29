---
title: Anveshanam CTF 2024
date: 2024-06-24 23:02:30 +0530
categories: [CTFs]
tags: [Cloud, Forensics, Rev]
image: /images/anveshanam.png
---

> This was a CTF hosted by IIT Jammu in collaboration with DRDO<br>
> I managed to solve 17 challenges in this CTF.

## CLOUD SECURITY

### Bucket Breach
**Challenge Description:**

![description](/images/bucket_breach_desc.png)

Found AWS access ID, and secret access key in the above IP.
![aws configure](/images/bucket_breach.png)

We can see that there is a bucket - `question1bucket`, and on listing the files in the bucket, I got some interesting looking files.<br>

![](/images/bucket_breach1.png)

I copied the files into my local system.<br>
On opening the `final_flag.html` page, I saw some binary text at the bottom of the page -
![](/images/binary)

Went directly to [cyberchef](https://gchq.github.io/CyberChef/) to decode the binary and got the flag

![](/images/bucket_breach3.png)

### Version Vortex
**Challenge Description:**

![description](/images/version_vortex_desc.png)
```plaintext
Access key ID: AKIASQUTIFWBI5UZN5UF
Secret access key: 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
```
In this challenge too, we are provided with AWS access key ID and secret access key.<br>
However the challenge desciption hints something towards version. I started my enumeration by listing the DB instances in AWS.
![db-instances](/images/version_vortex1.png)
![db-instances](/images/version_vortex2.png)
![db-instances](/images/version_vortex3.png)

Here we can see, that we got new credentials in the Taglists.<br>
We can also explicitly output the taglists using the following command -
```bash
aws rds list-tags-for-resource --resource-name <DBInstanceARN here>
```

![list-tag](/images/version_vortex4.png)
I then configured AWS with the new credentials I found and continued my enumeration further.
![enum](/images/version_vortex5.png)
![enum](/images/version_vortex6.png)

On getting details and version about the IAM policy, we can see an URL alongside the `resource` name.
![url](/images/version_vortex7.png)

Visiting the URL, found some hex data -

![url](/images/version_vortex8.png)

On decoding the data using Cyberchef, got the flag-
![url](/images/version_vortex9.png)

### Stealth Secret
**Challenge Description:**

![description](/images/stealth_secret_desc.png)
```plaintext
Access key ID: AKIATOHGMZD4XCBX2EAF
Secret access key: 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
Region: Mumbai
```

Again we are given AWS access key, and secret access key. The challenge description hints to look for some sort of secret.
Firstly I tried to list buckets, but it says access denied. 
Then I tried to list secrets, but for that too, we didn't have access.<br>
Then Listed all the IAM users -
![iam](/images/1.png)

On viewing the policy of `ques3_usr2` found an interesting policy name, but here we are the user - `ques3_use1`.
![policy](/images/2.png)

So, we need to somehow get the credentials for `ques3_usr2` to view the secret.<br>
On doing further enumeration -
![enum](/images/3.png)

Getting more info on the policy - 
![enum](/images/4.png)

We can see, that there is an IP address mentioned alongside the resource name. Visiting the IP address, got the below webpage -
![webpage](/images/5.png)

Checking the source code, found AWS credentials, so configured AWS using those credentials 
![creds](/images/6.png)
![configure](/images/7.png)

Now, for this user, I was able to list the secrets. Found an interesting secret and on getting the secret value, found the flag.
![secret](/images/secret_flag.png)

### Minute-mystery
**Challenge Description:**

![desc](/images/minute_mystery_desc.png)
```plaintext
DockerImage: cyberseciitjammu/minute-mystery:lts
```
Pulled the docker image into my kali machine, and tried to run it.<br>
![docker](/images/minute_mystery1.png)
But it exitted without starting properly. 
I restarted the docker container and then used `docker exec` to run a new process inside the running container.<br>
I got a flag, which was not the actual flag.
![fake_flag](/images/fake_flag.png)
I then looked further, and found some SQL credentials, which looked interesting
![flag](/images/minute_mystery2.png)
Then I decoded the hex value, and got the flag - 
![actualflag](/images/minute_mystery_flag.png)


## WEB

### Easy-web
**Challenge Description:**

![desc](/images/easy_web_desc.png)

On visiting the webpage, we have a Marquee text that says - ` My friend only loves to solve **hard-web** challenges. Anything else would give him bad time!`

![web](/images/easy_web_text.png)
There was nothing else on the webpage. On looking at the cookie, it was base64 encoded - 
![cookie](/images/easy_web-cookie.png)
Changed the cookie with `hard-web` value, and base64 encoded it. After reloading the page, got the flag.
![flag](/images/easy_web_flag.png)


### Newdevblog
**Challenge Description:**

![desc](/images/desc.png)

We are given a website with a search feature.<br>
On testing the search functionality, I found it to be vulnerable to reflected XXS, and Server Side Template Injection<br>
![detection](/images/detection.png)
Assuming that the name of the flag file is `flag.txt`, we can try to leverage SSTI to get the contents of the flag. 
Wappalyzer shows that it is made on Python's Flask.
We can use multiple payloads to get the contents of `flag.txt` leveraging different functions of flask.<br>
Payload:
{% raw %} 
```python
{{get_flashed_messages.__globals__.__builtins__.open('/etc/passwd').read()}}
```
{% endraw %}

`get_flashed_messages` is a common Flask function used to retrieve messages flashed in the session.<br>
`__globals__` gives access to global namespace <br>
`__builtins__` gives access to builtin python functions such as `open`
![flag](/images/flag1.png)
We can also use the `os` library to cat out the contents of flag.txt file - 
![another flag](/images/flag.png)

### Whitelist2Solve
**Challenge Description:**

![desc](/images/whitelist2solve_desc.png)

I have created a video on this challenge. You can check it out here - 


## FORENSICS

### recycleBF
**Challenge Description:**

![desc](/images/recyclebf_desc.png)

You can access the challenge files [here](https://github.com/anishkumarroy/AnveshanamCTF_files/tree/master/forensics/recycleBF)<br>
On Unzipping the `RBF.zip` file, found an `INFO2` file.
![unzip](/images/unzip.png)
Checking the file type it says - `INFO2: Windows Recycle Bin INFO2 file (Win2k - WinXP)`
On searching online, found that it is a file used by older versions of windows to keep track of the files that have been deleted and moved to the Recycle Bin.<br>
Now, we need to get the time, at which the file was deleted, so that we can unrar the `flag.txt.rar` file.
On researching a bit, I got to know about a tool - [`rifiuti2`](https://github.com/abelcheung/rifiuti2)<br>
Running the tool on the file `INFO` got the date and time at which the flag file was deleted 
![rifiuti2](/images/rifiuti2.png)
Unrared the `flag.txt.rar` file with the password format given in the challenge description and got the flag - 
![unrar](/images/unrar.png)