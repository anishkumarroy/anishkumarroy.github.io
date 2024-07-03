---
title: Anveshanam CTF 2024
date: 2024-06-24 23:02:30 +0530
categories: [CTFs]
tags: [Cloud, Forensics, Web, Steganography]
image: /images/anveshanam.png
---

> This was a CTF hosted by IIT Jammu in collaboration with DRDO<br>
> I managed to solve 17 challenges in this CTF.

## CLOUD SECURITY

### Sandwich
**Challenge Description:**

![desc](/images/sandwich_desc.png)

```plaintext
Docker Image: cyberseciitjammu/sandwich
```
In this challenge, we need to recover a deleted file from a docker container.<br>
I pulled the docker image. 
![pull](/images/sandwich_pull.png)
Started the container, but there was nothing in it..
![run](/images/sandwich_run.png)
At first I was clueless, but on researching a bit, I got to know about the `dive` tool, with which we can look at the different layers of the image.<br>
You can install the tool using- `sudo apt-get install dive`
You can run `dive` as -
![dive](/images/sandwich_dive.png)
![rundive](/images/sandwich_diverun.png)
We can see the different layers in it, and we can also see, on which layer, the `data.txt` file was delete, and in which layer it was created.<br>
Now, we can use the `docker save` command to save all the layers to an archive, and then get the deleted file from there.
![layers](/images/sandwich_layers.png)
![layer1](/images/sandwich_layer1.png)
![layer2](/images/sandwich_layer2.png)
![layer3](/images/sandwich_layer3.png)
Moved to the layer, on which the `data.txt` file was created.
Found another archive, unzipped it, and got the flag
![layer4](/images/sandwich_layer4.png)
![flag](/images/sandwich_flag.png)


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

### No_sudo
**Challenge Description:**

![desc](/images/nosudo_desc.png)

Access the challenge file [here](https://drive.google.com/file/d/1B318PFp5OiuQE444Uf_-sWSTQkIXfv4k/view?usp=sharing)

Imported the VM into virtualbox and fired up my kali machine. 
I discovered the IP of the given box using the commnd - `sudo arp-scan -l`. Then I SSHed into the machine using the provided credentials.
I could have done enumeration, but without any 2nd thought just downloaded linpeas in the box by hosting a python server in my kali machine and downloading from there.<br>
I instantly got a privilege escalation vector in the linpeas output -
![docker](/images/nosudo_docker.png)
Here, we can see that the user is a member of the docker group. Users added to the docker group, can run docker without using `sudo`.<br>
The `flag.txt` file was in the user's home directory. We can start a container with privileged access, and mount the home directory in the container
![flag](/images/nosudo_flag.png)
After going to the `/host` directory, the directory where I mounted `anveshanam's` user home directory, I found the flag.

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

### Rapid_rush
**Challenge Description:**

![desc](/images/rapid_desc.png)

On, opening the webpage, we have some options over there.
On trying to buy the flag -
![buy](/images/rapid_buyflag.png)
We can see, that it says, we need 401 more to buy the flag.<br>
Checking the current balance, we have an amount of 5000 at present.
![buy](/images/rapid_curbalance.png)
On checking the vouchers, we have four of them.
![buy](/images/rapid_vouchers.png)
On applying one of the vouchers, our amount is increased by 100.
![recharge](/images/rapid_recharge.png)
![success](/images/rapid_success.png)
So, even if we apply all the vouchers, we would only have an amount of 5400, i.e. 1 less than the required amount. As the challenge's name is `Rapid_rush` it hints towards making requests fast, i.e. a race condition vulnerability.
I captured one of the recharge requests using burpsuite, created a tab group in repeater, and put two of the same requests in the tab group, then used last-byte sync attack (as the server supports HTTP/1.1).<br>
Doing the above, I was successfully able to increase the amount by 200 more, using a single voucher code
![increased](/images/rapid_increased.png)
Then I used the other two vouchers, simply, which increased the price to 5500.
![price](/images/rapid_pricemore.png)
Then I bought the flag.
![flag](/images/rapid_flag.png)

### Eternity
**Challenge Description:**

![desc](/images/eternity_desc.png)

On going to the challenge link we see- 
![web](/images/eternity_wait.png)
On clicking on `Continue Waiting` button, we're redirected to a `/secret-site` endpoint 
![deny](/images/eternity_deny.png)
I captured the request to `/secret-site` endpoint using Burpsuite.<br>
As we can see, that it is revolving around time, and we need to make the time to a very large value to be able to access the page. <br>
The trick to this challenge was to add a `time` cookie in the request headers.
On sending a large positive value of time, it didn't work, but on sending a negative value, I got the flag. 
![flag](/images/eternity_flag.png)


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


## STEGANOGRAPHY

### Secrets of the Enchanted Forest
**Challenge Description:**

![desc](/images/forest_desc.png)

You can download the challenge file from [here](https://github.com/anishkumarroy/AnveshanamCTF_files/blob/master/Steganography/Secrets%20of%20the%20Enchanted%20Forest/img1.zip)

Unzipped the `img1.zip` file, and got an `img1.jpeg` file.<br>
First of all I tried to analyze the image using [`stegsolve`](https://github.com/zardus/ctf-tools/blob/master/stegsolve/install) but didn't find anything interesting.<br>
Then on using steghide, it was asking for password. Looked at the challenge description carefully, and noticed that the `FOREST` word is capitalized, which might be hinting towards it to be the password.<br>
Tried the passphrase to extract hidden files using steghide....and yeah!! it worked and hence got the flag.
![flag](/images/forest_flag.png)

### Convert_it
**Challenge description:**

![desc](/images/convert_desc.png)

Download the challenge file from [here](https://github.com/anishkumarroy/AnveshanamCTF_files/blob/master/Steganography/convert_it/task.zip)

On unzipping the `task.zip` file, we have another `testdata.zip` file and a whole bunch of EXEs, over there.
![exes](/images/convert_unzip.png)
Initially I was trying to analyze the EXEs, and look for any interesting data, but didn't find anything. I moved on to the `testdata.zip` file and it was password protected.<br>
I used `fcrackzip` tool and luckily it cracked the password.
I don't know what the EXE files were there for..<br>
Unzipped `testdata.zip`, got a text file out of it, and then the flag.
![flag](/images/convert_flag.png)

### Jack_is_waiting
**Challenge Description:**

![desc](/images/jack_Desc.png)

Access the challenge files [here](https://github.com/anishkumarroy/AnveshanamCTF_files/tree/master/Steganography/jack_is_waiting)

Here, we are given a `user.jpg` file and a zip file. 
I am not sure what the intented solution was, but again in this case too, I was able to crack the password of the ZIP file just by using `fcrackzip`.<br>
After that I unzipped the file and got the flag 
![flag](/images/jack_flag.png)

### Eye Can Learn
**Challenge description:**

![desc](/images/eye_desc.png)

Access the challenge file [here](https://github.com/anishkumarroy/AnveshanamCTF_files/blob/master/Steganography/Eye%20can%20Learn/audio.gif)

Given a GIF file, I first splitted it into frames using a website - <https://ezgif.com/split><br>
I saved all of them, and joined them one by one using canva.
There were a total 5 QR codes. You can check out those QR codes [here](https://github.com/anishkumarroy/AnveshanamCTF_files/blob/master/Steganography/Eye%20can%20Learn/qr_codes.pdf)<br>
Scanned the QR codes one by one, and found this format in every QR code - `www.<a_part_of_the_flag_here>`, like - `www.4dff619e`. I instantly understood, that we need to join those flag parts, and moreover, the flags in this CTF were of UUID format, so I was already able to understand, what will the first (8 chars in length) and last part (the one with 12 characters in length), I just needed to figure out the order of remaining 3 parts, which will make a total of 6 combinations.<br>
So figured out these possible combinations - 
```plaintext
flag{4dff619e-c743-930d-439f-0df6a09d2618}
flag{4dff619e-c743-439f-930d-0df6a09d2618}
flag{4dff619e-930d-c743-439f-0df6a09d2618}
flag{4dff619e-930d-439f-c743-0df6a09d2618}
flag{4dff619e-439f-c743-930d-0df6a09d2618}
flag{4dff619e-439f-930d-c743-0df6a09d2618}
```
Checked them one by one, and one of them worked out.


## REVERSING

### Deja Vu
**Challenge Description:**

![desc](/images/dejavu_Desc.png)

Access the challenge file [here](https://github.com/anishkumarroy/AnveshanamCTF_files/blob/master/reversing/D%C3%A9j%C3%A0%20vu/user.out)<br>
I have created a video explaining this challenge, you can check it out [here](https://www.youtube.com/watch?v=GTsTqgYdehw)

