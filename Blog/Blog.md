# Welcome to my "Blog" CTF walkthorugh!
You can find this CTF and many more on https://tryhackme.com/

## Self note:
In order to make this walkthrough as practicall as possible,
I added only the relevant pictures and explenations, in order to make it straightforward.

## 🔎 Reconnaissance

I started this CTF with a regular Nmap scan, in order to get a snapshot of what I'm going to be dealing with.

<img align="center" src="Images/1.png">

As we can see, we have couple interesting ports, on port 80 we got a webpage (wordpress) and his version.
On port 139 and 445 we have SMB service, (tip: on linux SMB called "Samba", this is one way to know what is your target's OS).
<br><br>
So lets take a look on the webpage.

<img align="center" src="Images/2.png">
<br>
As we can see, there is kind of a blog and we can see two authors, one is "billy Joel" and the second is "Karen wheeler".
<br><br>
Let's anumerate this blog further, and run GoBuster.
<br>

<img align="center" src="Images/3.png">
<br>
We can see some interesting folders.
<br><br>

## Credentials harvesting 🧰
We found a "/wp-admin" that redirect us to a login form (also found in the website itself), and tried to enter a default username.

<img align="center" src="Images/4.png">
<br>
In this case I tried "admin" just for the check, and the login page disclose us that this is invalid username.
<br>
Can we brute force it in order to find a valid one?, sure.
<br>
but why work harder when we can work smarter?, For now I'll leave it here and move to enumerate the SMB shares just in case...
<br><br>
I used "SMBmap" for this job.

<img align="center" src="Images/5.png">
<br>
And we can see a share with read and write permissions, but this is a rabbit hole, there are some memes and a steg file of a picture that tell us we fall into the hole 😆.
<br><br>
Another tip, we can also enumerate SMB shares with Nmap as you can see below.

<img align="center" src="Images/6.png">
<br><br>

Ok, let's get back to the username enumeration, I told you we can work smarter so I'll show you how.
<br>
First we have a tool called "WPscan" that know to enumerate a lot of important things of wordpress sites.
<br>
I found an important upload folder and the two authors from before, furthermore, their usernames.
<br>
Billy Joel is bjoel and Karen Wheeler is kwheel.
<br><br>

<img align="center" src="Images/7.png">

I know you waited, so <b>anoter</b> tip!
<br>
On some versions of wordpress, there was a vulnerability that disclose us all the users on the website.
<br>
The way to use it is: site.com/wp-json/wp/v2/users
<br>

<img align="center" src="Images/8.png">
<br>
Cool, isn't it?
<br><br>

Let's brute force the login page with the credentials we have so far.
<br>
Let me tell you that bjoel not intended to be hacked, but his mom (kwheel), do.
<br>
I captured a request with burp suite in order to see the parameters I need in order to perform the brute force.
<br>

## Brute-force 🐉
<img align="center" src="Images/9.png">
<br>
I also added for you the login page when we trying to enter a valid username.
<br>
Notice how the login form tell us which user exist and which not.
<br>

<img align="center" src="Images/10.png">
<br><br>

Here I tried to Hydra those users:
<img align="center" src="Images/11.png">
<br><br>
<img align="center" src="Images/12.png">
<br><br>
And this is the cracked password of kwheel:
<img align="center" src="Images/13.png">
<br>

## Exploiting vulnerabilities 🔓
We know that the wordpress version is 5.0, let's search for vulnerabilities, I uses searchsplit and exploit-db.
<br>
<img align="center" src="Images/14.png">
<br><br>
Then I found RCE vuln using Metasploit, and filled the relevant parameters.
<br><br>
<img align="center" src="Images/15.png">
<br>
We got a shell! 🚀
<br><br>

## Got a shell 🐚
Short visit in on the file system and we can found the user.txt, but this is another rabbit hole.
<br>
<img align="center" src="Images/16.png">
<br>

I found a suspecious USB in the /media folder but only Billy and Root can watch the content 😢.
<br>
<img align="center" src="Images/17.png">
<br><br>

This means we have to perform lateral movement or escalate our privileges, so I started with my regular rutine of enumeration for PE vectors.
<br>

## Privilege Escalation 👑

As you can see below, I found a strange SUID, with root, runs on root privileges, awsome.
<img align="center" src="Images/18.png">
<br>
But what is this binary intended to do?
<br>
After analysing this binary, I found she is search for env variable called "admin" and checks for his value.
<br>
I want to make my explenation clear so I broke it down to four stages:
<img align="center" src="Images/19.png">
<br><br>

<b>Stage one:</b> I used the "ltrace" command that helped me to analyse the binary and what it searching for.
<br>
<b>Stage two:</b> I used the "env" command to check my environmental variables, but no "admin" there.
<br>
<b>Stage three:</b> I added a variable and set it to 1 (one means true).
<br>
<b>Stage four:</b> Now I checked the binary with "ltrace" again and watch what happened when I run it.
<br><br>
<b>We are root!🥇</b>
<br><br>

Let's check our flags on /media/usb/user.txt and /root.txt.
<br>
<img align="center" src="Images/20.png">

## Thank you for reading my walkthrough!































