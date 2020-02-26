---
layout: single
title:  "NotWest CTF Writeup"
---
No flags are given here, but you can see how to get them!

## Task 2
The image we want to perform stego on is in Task 1, hiding in plain sight!

We can use steghide to pull out the flag.

```
steghide extract -sf flag.jpg
```
No password is required, so just press enter!
# Task 3
### Question 1
The file is encoded in Morse Code, because Morse code makes use of dots (.) and dashes (-).

We can decode it using [CyberChef](https://gchq.github.io/CyberChef/#recipe=From_Morse_Code('Space','Line%20feed')), which allows us to input files. 

### Question 2
The hint says it makes use of A-F, 0 - 9. That means it is hexadecimal. 

Again, [CyberChef](https://gchq.github.io) supports Hexadecimal file inputs.

### Question 3
One of the most common & popular encoding methods is Base64. 

In CTFs, we use Base64 all the time. Whether it is to copy & paste an SSH key from a server, or trying to read encoded data.

No surprise here that [CyberChef](https://gchq.github.io) also supports Base64 decoding from a file.

# Task 4
Passwords are often hashed, which means we apply a one way function to them. We can hash the password, but we are not supposed to un-hash the hash back into a password. However, as we'll see in this section!
## Question 1
This is Base64. We can use [CyberChef](https://gchq.github.io) to decode it!
## Question 2
We can use [Hash-Identifier](https://tools.kali.org/password-attacks/hash-identifier) to identify the hash type.

```
~/Documents/cybersoc took 41s 
➜ hash-identifier
   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #
   #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #
   #                                                             By Zion3R #
   #                                                    www.Blackploit.com #
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------
 HASH: a3f7c3dbd1d9d1951a6ab9ed155fac17

Possible Hashs:
[+] MD5
```

This tells us it is MD5.

The hint says to use a Wordlist. A wordlist is a list of words, typically passwords. We normally use [Rockyou.txt](https://www.kaggle.com/wjburns/common-password-list-rockyoutxt). It is a password list obtained from a defunct social media site which was hacked.

Let's run Hashcat, which is the most popular hash cracking software (and also the fastest).

To run Hashcat, we need to know what mode is used. We can grep the help manual to find the mode used by Hashcat.
```
➜ hashcat -h | grep MD5
      0 | MD5                                              | Raw Hash
```

Now we write the command:

```
hashcat -m 0 -a 0 'a3f7c3dbd1d9d1951a6ab9ed155fac17'  /usr/share/wordlists/rockyou.txt
```

Alternatively, we can use an online Hash lookup program such as [HashKiller](https://hashes.com/decrypt/basic).
## Question 3

Let's run Hashcat again. 
```
hashcat -m 100 -a 0 '1e8e42a6f33bcce606ace8a2789cd366c8f288e9'  /usr/share/wordlists/rockyou.txt
```
# Task 5
## Flag 1
The first flag is in the source code.
## Flag 2
The second flag is in Robots.txt.
## Flag 3
The third flag is in the Javascript source code. 

The flag is obfusicated, which means it is hidden. Luckily it is the string "666C61677B636F6C6F6E336C5F6D7573746172647D" which is Hexadecimal.

We decode it from Hex and we have the flag!
## Flag 4
The final flag is the icon for the webserver, which we can view properly in the source code.
# Task 7
The name is "inclusion", and it is a website.

This means it is local file inclusion. Which means we may be able to get files from the same directory the webserver is in. In some instances, we can get all files from any directory regardless of where the webserver is.

Reading the source code we find a comment:
```html
<!--<td><a href="?page=flag.php">Flag</a></td>-->
```
`?page=flag.php`. If you take a look at the URL, you'll notice we use ?pages to signify pages:
```
http://notwest.cybersoc.cf:42069/?page=about.php
```
Going to
```
http://notwest.cybersoc.cf:42069/?page=flag.php
```

Doesn't show us anything. What about the source code?

```html
  <!-- Now we no longer include, this file won't work anymore! -->
<?php
  echo file_get_contents("/flag.txt");
?>
```

Now we know the file is `/flag.txt`. 

If we ask the webapp to go to /flag.txt, like so:
```
http://notwest.cybersoc.cf:42069/?page=/flag.txt
```

We get the flag!

Something a lot of newbies (and I myself do) is to try and go to `?page=flag.txt` instead of `/flag.txt`. Make sure you're not caught out!
# Task 8

As you may have seen, all of the flags start with `flag`. Open the file up in your favourite text editor (such as Notepad) and just run a find for "flag{".

Alternatively, use Grep. Which does the exact same thing but supports more advance finding features. 

On the Cybersoc CTF team, we reguarly make use of Grep to find flags.
# Task 9
Put the image into a barcode reader, We get this as a result:
```
M1CONNOR/HARLAN       EERPW7D6STNMUCEZY3071 215 8B  619  10A1651354182
```
We can use [this blogpost](https://krebsonsecurity.com/2015/10/whats-in-a-boarding-pass-barcode-a-lot/) to work out what this data means.
![img](https://krebsonsecurity.com/wp-content/uploads/2015/10/bpdecoded.png)
# Task 10
Base64 42 times (hope you learnt how loops on CyberChef worked ;) )
# Task 11
## Question 1
This flag is on /another-page.html
## Question 2
We see this comment in the source:
```
<!-- /img is still being developed. -->
```
https://michalani.github.io/ctf/img/
Which takes us to:
https://michalani.github.io/ctf/img/close-up-of-a-cup-of-tea.jpg

We run `exiftool` on this image to get the flag.
## Question 3
Usually when people want to hide things, they put it into Robots.txt to tell Google to not index it. Looking at robots, we see this page:
```
User-Agent: *
Disallow: *
Disallow: /private/transaction/
```

If we look at the source, we can find a link to another page. Following that page, we are teased with:
```
So close yet so far.
```
Looking at the URL, we can see some Base64.
```
ZmxhZ3tnZXR0aW5nX3dhcm1lZF91cH0==
```
Going to Cyberchef, we can see it is invalid. Deleting an = sign (which is padding in B64) gives us the flag.

# Question 4
Some websites have sitemaps, which tell us all the pages on the site. Visting the sitemap at:
[https://michalani.github.io/ctf/sitemap.xml](https://michalani.github.io/ctf/sitemap.xml)
lets us see this link:
```
https://michalani.github.io/ctf/leak.html
```

Sitemaps are invaluable tools for hackers. For example, you may write a diary on a blog you own and then find out your friends found the sitemap and they start reading your personal life through a Discord chat at 2am while you rush to delete the entries. Hasn't happened to me. Nope.

The flag is in Binary. We can decode it from Binary.

Then we decode it from ROT24.
[https://gchq.github.io/CyberChef/#recipe=Magic(3,false,false,''/disabled)From_Binary('Space')ROT13(true,true,24)&input=MDExMDEwMDAgMDExMDExMTAgMDExMDAwMTEgMDExMDEwMDEgMDExMTEwMTEgMDExMDEwMTAgMDExMDAwMTEgMDExMDAxMDEgMDExMDExMDEgMDEwMTExMTEgMDExMTAxMTAgMDExMDEwMTAgMDExMDAxMTEgMDEwMTExMTEgMDExMDAxMDAgMDExMDAxMTEgMDExMDAxMTEgMDExMTExMDEg](https://gchq.github.io/CyberChef/#recipe=Magic(3,false,false,''/disabled)From_Binary('Space')ROT13(true,true,24)&input=MDExMDEwMDAgMDExMDExMTAgMDExMDAwMTEgMDExMDEwMDEgMDExMTEwMTEgMDExMDEwMTAgMDExMDAwMTEgMDExMDAxMDEgMDExMDExMDEgMDEwMTExMTEgMDExMTAxMTAgMDExMDEwMTAgMDExMDAxMTEgMDEwMTExMTEgMDExMDAxMDAgMDExMDAxMTEgMDExMDAxMTEgMDExMTExMDEg)
# Task 12
We can use Zip2John to turn the Zip folder into something that John can crack. Then we get John to crack the password!
Or we can use [FCrackZip](http://manpages.ubuntu.com/manpages/precise/en/man1/fcrackzip.1.html) to do the same, minus the Zip2John part.
# Task 13
For those of you who are more astute, there are 3 people from Cybersoc that have worked on this CTF. One of them is Harlan, who uploaded an XOR encryptor / decryptor. The same one he used to make the problem!

[https://github.com/c3-ctf/xorcrypt](https://github.com/c3-ctf/xorcrypt)

But fear not if you didn't find this, Googling ["XOR decoder Python"](https://stackoverflow.com/questions/20557999/xor-python-text-encryption-decryption) brings up a nice example.

# Task 15
Looking at the source code, we can see that it says it is secure against cross site scripting. Which means it is not secure against cross site scripting (developers are stupid).

The XSS protection states:
> content="default-src 'self' 'unsafe-inline'"
Which means we cannot send data outside of the server. 

What way is there for us to exfiltrate data without sending it to an external server?

We have a token box, and this token box prints incorrect tokens to the screen.

When we have an incorrect token, it says "incorrect" so we don't want to include that. 

We also want to b64 encode the token, to allow for easy transportation.

Also we want our script to submit the form with the token as b64.

Also also just as an extra "lol good luck" you can't send the entire page so you just have to send the token.

```html
<script>if (!document.getElementById("result").innerHTML.includes("incorrect") &&!document.getElementById("result").innerHTML.includes("No token")  ) {document.getElementById("token").value = btoa(document.getElementById("result").innerHTML); document.getElementById("form").submit();}</script>
```

Good luck, have fun.

# Task 16
## Question 1
[https://www.youtube.com/watch?v=0Jx8Eay5fWQ](https://www.youtube.com/watch?v=0Jx8Eay5fWQ)
## Question 2
SQLMap on risk 3 and level 3. 
Reason is because at the low levels SQLmap defaults to time based injections, whereas at higher risk and level it defaults to boolean based injection which is more noisy on the server, but much faster.
Password is Hackers related.
[https://www.youtube.com/watch?v=0Jx8Eay5fWQ](https://www.youtube.com/watch?v=0Jx8Eay5fWQ)
## Question 3 & 4
Harlan began to explain this to me, and then I fell asleep. If you want to find out how this works, please ask Harlan.

If you really must know it's something to do with CBC padding oracles.
