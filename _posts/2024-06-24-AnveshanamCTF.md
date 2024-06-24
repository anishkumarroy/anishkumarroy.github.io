---
title: Anveshanam CTF 2024
date: 2024-06-24 23:02:30 +0530
categories: [CTFs]
tags: [Cloud, Forensics, Rev]
image: /images/anveshanam.png
---

> This was a CTF hosted by IIT Jammu in collaboration with DRDO<br>
> I managed to solve 17 challenges in this CTF, though I can't include the writeups of all the challenges I solved, as they made the challenges inaccessbile just after the CTF ended.

## CLOUD SECURITY

### Bucket Breach

We were provided with AWS access key and secret access key.<br>
First of all I did aws configure and started enumeration
![aws configure](/images/bucket_breach.png)

We can see that there is a bucket - `question1bucket`, and on listing the files in the bucket, I got some interesting looking files.<br>

![](/images/bucket_breach1.png)

I copied the files into my local system.<br>
On opening the `final_flag.html` page, I saw some binary text at the bottom of the page -
![](/images/binary)

Went directly to [cyberchef](https://gchq.github.io/CyberChef/) to decode the binary and got the flag

![](/images/bucket_breach3.png)

### Version Vortex

In this challenge too, we were provided with AWS access key ID and secret access key.<br>
However the challenge desciption hinted something towards version. I started my enumeration by listing the DB instances in AWS.
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

Again we were given AWS access key, and secret access key. The challenge description hinted to look for some sort of secret.
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
![secret](/images/flag.png)

