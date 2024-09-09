---
title: Hack Havoc CTF 2024
date: 2024-09-03 15:00:00 +0530
categories: [CTFs]
tags: [Crypto, Rev, Web, OSINT]
image: /images/hackhavoc.png
---

> It was an individual CTF, and a one-month long CTF organised by CTF, comprising of challenges from web, reversing, cryptography and OSINT

## REVERSING

### Rev is Easy
**Challenge Description:**

![desc](/images/havoc_rev.png)

Given a binary file, on running strings on the file, got the flag
![flag](/images/reviseasy_strings.png)

### Go Crazy !!!
**Challenge Description:**

![desc](/images/gocrazy_desc.png)

Again, we were given a binary file
```bash
$ file killswxtchhhhhhhhhhhhhhhhhhhhh                                                                                                                                                      
killswxtchhhhhhhhhhhhhhhhhhhhh: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=3d5036ca533ce18880ef40f60
e9ddae6a7eb301f, for GNU/Linux 3.2.0, not stripped   
```

But this time, running strings on the binary, didn't give anything interesting. Opened the binary in Ghidra, and directly viewed the main function
![ghidra](/images/gocrazy_secret.png)

In this we can see, that our input is being compared to a predefined variable. If it matches then it outputs the flag. I directly converted the hex values of the flag to ascii, and got the flag.
![flag](/images/gocrazy_flag.png)

### Who’s Really Dunked?
**Challenge Description:**

![desc](/images/dunked_desc.png)

Given a txt file. On opening the text file, there was encoded data in it. If we look at the challenge description carefully. There is somekind of hint in it.
Pasted the contents of the text file in to cyberchef, and found that it was decoded using Base92 and then ROT47. After that directly got the flag in the JS code - 
![flag](/images/dunked_flag.png)
Hence the flag was - `CM{News_Alerts_Incident}`

### The Key to Nowhere
**Challenge Description:**

![desc](/images/nowhere_desc.png)

Again, we have got a binary. If we try to run the binary, it is asking us for a key - 
![key](/images/nowhere_run.png)
On running strings on the binary, I see pyinstaller, .pydata and all, which suggests that it is packed using pyinstaller.
We can look for an unpacker for it, and in this case we can use `pyinstxtractor`. We can download it from here - [pyinstxtractor](https://github.com/extremecoders-re/pyinstxtractor). I cloned the github repo, and decompiled the binary
![decompile](/images/nowhere_decompile.png)

Now, we have unpacked the files, we will decompile the pyc files to get the readable code.<br>
In the output of the extraction we can see, that it mentions `3.pyc` as its possible entry point. We can try to decompile it using `uncompyle6`
To download this we can use `pip install uncompyle6`.
But on using uncompyle6, I was getting a version error. 
![error](/images/nowhere_error.png)
Did a quick google search to look for other options, and found this website that can be used to decompile the python bytecode files - [pylingual](https://pylingual.io/). <br>
After decompiling the source code - 
![hidden](/images/nowhere_hidden.png)
We can see, the `get_hidden_key()` function. We can directly get the key from there.
![key](/images/nowhere_key.png)
Now, we can just run the binary, enter the key and get the flag.
![flag](/images/nowhere_flag.png)
Alternatively, we can just run the `get_hidden_flag()` function's code, and get the flag directly.

### Awwwwwwwwwwwwwww!!
**Challenge Description:**

![desc](/images/awww_desc.png)

We are given an image and a text file. The file consists of the following content -
```plaintext
awa awa awa awawawa awa awawawawa awa awawawa awa awa awa awawa awa awa awawawa awa awa awa awawawa awa awawawa awa awa awawa awa awa awa awawawa awa awa awa awa awawa awa awawawa awa awa awawawa awa awa awawawa awa awa awawa awawa awa awawawa awa awa awa awawawa awa awawawa awa awawa awawa awa awa awawawa awawa awawa awa awa awa awawawa awawa awawawa awa awa awawawa awawawa awa awawa awa awawawa awa awa awa awawawa awa awawawa awa awa awa awa awa awa awa awawawa awa awa awa awa awa awa awa awawawa awa awa awa awawa awa awa awawawa awa awa awa awawawa awa awawawa awa awa awawa awa awa awa awawawa awa awa awa awa awawa awa awawawa awa awa awawawa awa awa awawawa awa awa awawa awawa awa awawawa awa awa awa awawawa awa awawawa awa awawa awawa awa awa awawawa awawa awawa awa awa awa awawawa awawa awawawa awa awa awawawa awawawa awa awawa awa awawawa awa awa awa awawawa awa awawawa awa awa awa awa awa awa awa awawawa awa awa awa awa awa awa awa awawawa awa awa awa awa awa awa awa awawawa awawa awa awa awa awa awa awawawa awawawa awawa awa awa awawawa awawawawawawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa awa awa awa awawa
```
And the given image is as follows - 
![awa](/images/awww_image.png)

We can see, that it is using AWA5.0 interpreter. On searching in google, found that it is acutally a code used for autistic people and is a type of esoteric programming language. I found [this video](https://www.youtube.com/watch?v=DY70AcaXq40) on youtube which helped to understand it much better.
Also found the [documentation](https://github.com/TempTempai/AWA5.0/blob/main/Documentation/AWA5.0%20Specification.pdf) of this language in the video description.
After reading this documentation a bit I converted the AWA code into binary. 
For the conversion I wrote this following code - 
```python
a= ['awa', 'awa', 'awawawa', 'awa', 'awawawawa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awawawa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awawawa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awawa', 'awawa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awawawa', 'awa', 'awawa', 'awawa', 'awa', 'awa', 'awawawa', 'awawa', 'awawa', 'awa', 'awa', 'awa', 'awawawa', 'awawa', 'awawawa', 'awa', 'awa', 'awawawa', 'awawawa', 'awa', 'awawa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awawawa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awawawa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awawa', 'awawa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awawawa', 'awa', 'awawa', 'awawa', 'awa', 'awa', 'awawawa', 'awawa', 'awawa', 'awa', 'awa', 'awa', 'awawawa', 'awawa', 'awawawa', 'awa', 'awa', 'awawawa', 'awawawa', 'awa', 'awawa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awawawa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awawawa', 'awawa', 'awa', 'awa', 'awa', 'awa', 'awa', 'awawawa', 'awawawa', 'awawa', 'awa', 'awa', 'awawawa', 'awawawawawawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa', 'awa', 'awa', 'awa', 'awawa']


binary = ''
for i in a: 
	if i == 'awa':
		binary += '0'
	else:
		no_of_wa = i.count('wa') -1 
		binary += '0'+('1'*no_of_wa)
		

print(binary)
```

Then I converted the binary back into instruction and arguments manually taking the documentation as reference, and got the following code - 
```plaintext
red 
pop
sbm 2
sbm 3
sbm 4
sbm 1 
sbm 6
sbm 5
sbm 3 
sbm 10 
sbm 20
sbm 22
sbm 25
sbm 3 
sbm 0
sbm 0
sbm 2 
sbm 3
sbm 4
sbm 1 
sbm 6
sbm 5 
sbm 3 
sbm 10 
sbm 20 
sbm 22
sbm 25
sbm 3
sbm 0 
sbm 0 
sbm 0 
sbm 16
sbm 26
sbm 31
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
prn
```
But this also didn't help me to figure out the flag.<br>
If we look carefully at the given image, we can see, that we are given output of the code, but not the input. Which means we need to figure out the input somehow.
I tried inputting radom data, and it was getting jumbled. The output provided in the code was - `owoosHiai1w1aia_awJ3ally!0awwa_0` which is of 32 characters.
So I took an input of 32 distinct characters to check how it is getting jumbled. For that I used the input - `abcdefghijklmnopqrstuvwxyzABCDEF`
![output](/images/awww_output.png)
But the output was less than 32 characters, i.e of 27 characters
![length](/images/awww_length.png)

I then checked which letters where getting missed. 
![missing](/images/awww_missing.png)

The letters `k`, `q`, `v`, `x`, and `z` were missing. I checked the documentation once again and found this interesting part, which I was missing earlier.
![doc](/images/awww_interesting.png)
I then renewed my input to include numbers in place of those characters. So, this time I used the input - `abcdefghij1lmnop2rstu3w4y5ABCDEF`
![correct](/images/awww_correct.png)
The output that I got this time, was of 32 characters. Now I just did some coding to find out which characters were getting jumbled at which position
Then simply reversed the jumbling with the output that we have in the given image, and got the flag.
![flag](/images/awww_flag.png)
Hence the flag was - `CM{awawawawaawa_00ooosHii11i_J3lly!}`

## WEB

### We're rolling
**Challenge Description:**

![desc](/images/rolling_desc.png)

Checked the robots.txt file of the website and got the flag - 
![flag](/images/rolling_robots.png)

### Drunken website
**Challenge Description:**

![desc](/images/drunken_desc.png)

So, we are given a website, with texts written in many different languages -
![webpage](/images/drunken_webpage.png)
I translated all of them to english, but they were not much useful. Checked the source code, and found nothing much interesting except this - 
![source](/images/drunken_source.png)
I translated the text that looked like a flag, but that too was a rabbit hole. I then checked if their is any functionality in the website, and clicked the `Case studies` option, and boom, found some options, and that was actually `homepage.html` which I was earlier ignoring.
![info](/images/drunken_boom.png)
After that I checked the source code, and found a page as `0.html`, went to the page and got the flag.
![page](/images/drunken_page.png)
![flag](/images/drunken_flag.png)

### A Shakespearian Tragedy
**Challenge Description:**

![desc](/images/tragedy_desc.png)

Here is the given webpage - 
![webpage](/images/tragedy_webpage.png)
In the webpage, we can see the text `et-tu-brute`, which may be kind of hints to bruteforce for directories. I used `ffuf` to bruteforce for directories -
![ffuf](/images/tragedy_ffuf.png)

Found the above directories. Looked into all them one by one, and their source code too, and found a base58 encoded flag in the source code of `/users` endpoint
![base58](/images/tragedy_base92.png)
Decoded it in cyberchef, and found the jumbled flag as below - 
![flag](/images/tragedy_flag.png)
Actually it is one of the quote from Caesar  - `I came, I saw, I conquered` , so corrected the flag as - `CM{i_c4me_i_s4w_i_c0nqur3d}`

### Bidden Funhouse
**Challenge Description:**

![desc](/images/bidden_desc.png)

I created a video for it, you can watch it over [here](https://www.youtube.com/watch?v=A3ULB_RkqSU)


### The Shell Shocker
**Challenge Description:**

![desc](/images/shocker_desc.png)

I created a video for it, explaining this challenge, you can watch it [here](https://www.youtube.com/watch?v=A3ULB_RkqSU&t=420s)


## OSINT

### CyberMaterial’s Cyber-Sleuth Newsletter
**Challenge Description:**

![desc](/images/newsletter_desc.png)

If we look at the challenge description properly, the text - **Hacker leaked unreleased Netflix content** is highlighted. I went to Cybermaterial's linkedin page directly, as they keep on posting these kind of stuff on linkedin. I looked through all the posts one by one, and searched for the keyword `netflix` and then found the post with the flag.

![flag](/images/newsletter_flag.png)

### Meet me here !
**Challenge Description:**

![desc](/images/meet_desc.png)

We are given this image - 
![image](/images/meet_image.jpg)
On the person's T-Shirt we can see, `Cover6 Solutions` written, and we can also see the logo. On searching got that Cover6 solutions is actually a cybersecurity firm.<br>
On the back of the photo, we can see `Subway`, `BP gas station` (from the logo) and `waffle Inn`
On searching for Cover6 solutions on Google maps, got the location of the company (alternatively we can also check the linkedin page of the company)
![location](/images/meet_cover6.png)
Now, in the **nearby** option, if we search for waffle inn, we will get the location of the image. Searching it now isn't working because now it is permanently closed. But here is the exact [location](https://www.google.com/maps/place/Waffle+Inn/@38.191234,-78.9918428,3a,25.2y,320.42h,93.19t/data=!3m7!1e1!3m5!1sDBRVuV0iZ7Zr6Kke86Nw1Q!2e0!6shttps:%2F%2Fstreetviewpixels-pa.googleapis.com%2Fv1%2Fthumbnail%3Fcb_client%3Dmaps_sv.tactile%26w%3D900%26h%3D600%26pitch%3D-3.185753309119235%26panoid%3DDBRVuV0iZ7Zr6Kke86Nw1Q%26yaw%3D320.4238624464195!7i16384!8i8192!4m14!1m7!3m6!1s0x89b49f5c986c7dd3:0x18d296d41162934b!2sWaffle+Inn!8m2!3d38.1916476!4d-78.9917246!16s%2Fg%2F1vspscnq!3m5!1s0x89b49f5c986c7dd3:0x18d296d41162934b!8m2!3d38.1916476!4d-78.9917246!16s%2Fg%2F1vspscnq?coh=205410&entry=ttu&g_ep=EgoyMDI0MDkwNC4wIKXMDSoASAFQAw%3D%3D) of the image that I found while solving the challenge as at that time the Waffle Inn was open -
![waffleinn](/images/meet_waffleinn.png)
Hence the flag was - `CM{Laurel_Hill_Rd}`


### APT Intel Hunt
**Challenge Description:**

![desc](/images/intel_desc.png)

The challenge description mentions about cybermaterial report and APT group lazarus. I searched for cybermaterial material reports, and found their website - [cybermaterial](https://cybermaterial.com/).<br>
Searched for `lazarus` and found this blog post - 
![lazarus](/images/intel_lazarus.png)
Intially I didn't find anything directly in the blog post. THen I started reading it line by line, and then I discovered a pastebin link on hovering over a line on the post (later on I think they made the link visible clearly)
![pastebin](/images/intel_pastebin.png)
On looking at the pastebin data carefully, I noticed that there is some different hex type looking data in each line
![hexdata](/images/intel_hexdata.png)
I wrote the hex data in cyberchef, and coverted it to ascii, and got the flag
![flag](/images/intel_flag.png)

### Catch me !!!
**Challenge Description:**

![desc](/images/catchme_desc.png)

We are given a image - 
![mob](/images/catchme_mob.png)
In the challenge description, we are given a mobile number. In USA, the first 3 digits of the mobile number, corresponds to the area code.
Searching for the 702 area code, I found that it is actually Las vegas. 
![lasvegas](/images/catchme_lasvegas.png)
Then I searched for `Al Capone's mob` as given in the challenge description who he is a member of and found about this museum
![museum](/images/catchme_museum.png)
If we look at the image of the museum, it is same as the building in the challenge file.
![mobmuseum](/images/catchme_mobmuseum.png)
So the flag was - `CM{the_mob_museum}`

### Oops! Where Did I Hide the Flag?
**Challenge Description:**

![desc](/images/hide_desc.png)

The challenge description says, that the flag is hidden in a video post, and it also mentions about news, alerts, and incidents. I directly went to their Youtube channel - [cybermaterial](https://www.youtube.com/@cybermaterial) and watched their recent video completely to look for the flag, but didn't find any. On looking at the description, I found new, alerts and incidents mentioned separately.
![news](/images/hide_news.png)

But I didn't find the flag there. So, I checked the description of all the videos one by one, and found the flag in the description of this [video](https://www.youtube.com/watch?v=iM4vtqkhmIo)
![flag](/images/hide_flag.png)


## CRYPTO

### Green Flags
**Challenge Descrition:**

![desc](/images/greenflag_desc.png)

We are given this image with flags -
![source](/images/greenflag_source.png)

These are actually signal flags. We can search on google for signal flag decoder. I used [this](https://www.dcode.fr/maritime-signals-code) to decode.
Entered all the flags one by one manually and got the text - 
![flag](/images/greenflag_flag.png)
Hence the flag was - `CM{nato_signals}`

### I can't see it
**Challenge Descrition:**

![desc](/images/cantsee_desc.png)

We are given a text file with braille script in it -
```plaintext
⠉⠍⠸⠣⠹⠼⠁⠼⠑_⠃⠗⠼⠙⠼⠁⠇⠇⠼⠉_⠼⠁⠎_⠼⠙⠼⠉⠁⠇⠸⠜
```

Directly searched for braille decoder on google. I tried some decoder earlier, but it was giving me incorrect results as - 
![incorrect](/images/cantsee_incorrect.png)
![incorrect](/images/cantsee_incorrect1.png)

But then, [this](https://wecapable.com/braille-translator/braille-to-english-text/) decoder worked for me, and gave me the exact output -
![flag](/images/cantsee_flag.png)
Hence the flag was - `CM{th15_br41ll3_1s_431l}`

### Digital Black Hole
**Challenge Descrition:**

![desc](/images/digitalhole_desc.png)

We are given a file with the name `cRYPTO.PNG`. Though the file is not a PNG file, on checking the file type it just says, ASCII text - 
```terminal
$ file cRYPTO.PNG 
cRYPTO.PNG: ASCII text, with very long lines (18134), with no line terminators
```
Opened the fine in a text editor and there was binary data in it as - 
```plaintext
110000,00110001,00110001,00110000,00110000,00110000,00110000,00101100,00110000,00110000,00110001,00110001,00110.......and so on
```
I directly opened cyberchef, and first of all removed the commas (`,`).<br>
Then the cyberchef's magic conversion helped to decode it completely after that, and I got the flag.
![flag](/images/digitalhole_flag.png)

### Dear Trithemius,
**Challenge Descrition:**

![desc](/images/trithemius_desc.png)

We are given two files as follows - 
Dear_Trithemius.txt
```plaintext
Dear Trithe,

As I write this, my heart brims with emotions that words struggle to express. I feel compelled to share my feelings with you, hoping they reach you as deeply as they reside in me.

From the moment we met, I sensed that our bond was unique, as if the universe itself had brought us together. Each day, my admiration for you grows. Your kindness, your intellect, and your laughter are just a few of the many things that captivate me.

I treasure every memory we've made together, from our first meeting to our countless conversations. I’ll never forget the bliss of hearing you whisper those special words: “LPXH_Z_AZRDSQZWJI” They echo in my heart, reminding me of the precious gift you are.

You are my confidante, my rock, my soulmate. I love you for your strength, your vulnerability, and the way you make me a better person.

With all my heart, I promise to cherish and love you always.

Yours forever,

Siegfried
```

lovelettter.go
```python
def to_my_honey(owo):
    return ord(owo) - 0x41

def from_your_lover(uwu):
    return chr(uwu % 26 + 0x41)

def encrypt(billet_doux):
    letter = ''
    for heart in range(len(billet_doux)):
        letters = billet_doux[heart]
        if not letters.isalpha():
            owo = letters
        else:
            uwu = to_my_honey(letters)
            owo = from_your_lover(uwu + heart)
        letter += owo
    return letter

m = "imissyou"

c = encrypt(m)
print(c)
```

Here we can see, the file name is mentioned with the extension .go, but it is actually a python file. <br>
In the love letter, we can see this text - “LPXH_Z_AZRDSQZWJI” which we need to decode
I wrote a decryption script for that - 

```python
def to_my_honey(owo):
    return ord(owo) - 0x41

def from_your_lover(uwu):
    return chr(uwu % 26 + 0x41)

def decrypt(ciphertext):
    message = ''
    for heart in range(len(ciphertext)):
        letters = ciphertext[heart]
        if not letters.isalpha():
            owo = letters
        else:
            uwu = to_my_honey(letters)
            owo = from_your_lover(uwu - heart)
        message += owo
    return message

c = "LPXH_Z_AZRDSQZWJI"
m = decrypt(c)
print(m)
```
On runnning the script, got the flag
![flag](/images/trithemius_flag.png)

### My Secret X 'V' My Secret Y
**Challenge Descrition:**

![desc](/images/xandy_desc.png)

We are given hex data in this challenge, which possibly looks like a ciphertext. The challenge name slightly hints towards XOR.<br>
I directly went into cyberchef to try out the XOR operation, as we don't have any other data over here. First I converted in from hex, and then did XOR bruteforce. I got the flag, on doing XOR bruteforce two times.

![flag](/images/xandy_flag.png)