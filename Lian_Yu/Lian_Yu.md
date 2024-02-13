# Welcome to my "Lian_Yu" CTF walkthorugh ⛰️!
You can find this CTF and many more on https://tryhackme.com/

## Self note:
In order to make this walkthrough as practicall as possible,
I added only the relevant pictures and explenations, in order to make it straightforward.

## Reconnaissance🔎:
As every CTF, we starting with recon, so let's run Nmap and some other tools as needed:
<br><br>
<img align="center" src="Images/1.png">
<br><br>

I checked the HTTP server, but the Index.html is not very interesting (feels like waste of time😆).
<br><br>
<img align="center" src="Images/2.png">
<br><br>

But don't forget that on the tasks, we have a question about a web directory, so I used GoBuster for this mission.
<br><br>
<img align="center" src="Images/3.png">
<br><br>

As you can see, Gobuster found a directory called "island" but this is an incorrect answer...
<br>
Let's try to access it anyway:
<br><br>
<img align="center" src="Images/4.png">
<br><br>

There is a hidden code word, (I found it without access the source code, as a rule I always click and drag websites😆),
<br>
But I'll show you how it looks like in the source code:
<br><br>
<img align="center" src="Images/5.png">
<br><br>
Note the h2 header, its color is white, and the hidden code word is "vigilante".
<br>

Yet, we don't have any answer for the second question...
<br>
That means we have to enumerate further:
<br><br>
<img align="center" src="Images/6.png">
<br><br>

We found another directory, called "2100", and this is the answer for the second question! ✅
<br>
Let's try access to the /island/2100:
<br><br>
<img align="center" src="Images/7.png">
<br><br>

Nothing interesting, but let's take a look on the source code just to be sure we don't miss anything:
<br><br>
<img align="center" src="Images/8.png">
<br><br>

This is a clue for us to try and search for a file with ".ticket" extensiton.
Luckily, GoBuster can handle it well with the -x flag:
<br><br>
<img align="center" src="Images/9.png">
<br><br>

After couple minutes, GoBuster found the file "green_arrow.ticket"
<br>
Now we can answer the third question ✅
<br>
So let's navigate there:
<br><br>
<img align="center" src="Images/10.png">
<br><br>

## Decode the hidden password🔓:
We can see a message with sort of a password or maybe encoded something...
So let's check if its really encoded:
<br><br>
<img align="center" src="Images/11.png">
<br><br>

As you can see, this is really an encoded message (encoded with base 58) and maybe this is the password for the FTP service?
<br>
Actually this is the password for the FTP and now we can answer the fourth question! ✅
<br><br>

## Access the FTP server📂:
Until now I've found a code word (vigilante) and a password for FTP (!#th3h00d).
<br>
Now I want to look what inside the FTP server using the credentials I found and maybe reveal something new.
<br><br>
<img align="center" src="Images/12.png">
<br><br>

I downloaded all the files from the /home/vigilante and kept enumerate the machine a little...
<br>
When I navigated the the /home and listed the directories, I've found another user called "slade":
<br><br>
<img align="center" src="Images/13.png">
<br><br>

## Stego🐊:
Ok, now I want to examine the files I've downloaded from the vigilante's home directory,
<br>
I think there is some hidden files within them...
<br>
So I started to use some tools in order to check it (strings, file, steghide, binwalk and others...),
<br>
I can tell you that the only file that should interest you is the "aa.jpg", there are two files within.
<br>
But first, let's extract the files:
<br><br>
<img align="center" src="Images/14.png">
<br><br>

As you can see, we have to enter a passphrase in order to extract the files, but I found a tool called "stegcracker" that can do the job for me!
<br><br>
<img align="center" src="Images/15.png">
<br><br>

And we successfully cracked the password!
<br>
So let's extract the "aa.jpg" again with the password "password".
<br><br>
<img align="center" src="Images/16.png">
<br><br>

Note that the extracted files are actually a .zip (ss.zip), so I unzipped it.
<br>
Then you can see the actuall files, "passwd.txt" and "shado".
<br>
By the way, "shado" is the answer for the fifth question ✅
<br>
Its also a clue, because "passwd.txt" sounds like the /etc/passwd (a linux file that contains all the users in the system),
<br>
And "shado" sounds like /etc/shadow (a linux file that contains all the passwords (encrypted) for the users in the system).
<br>
So we can assume that the shado refes to the password.
<br><br>
<img align="center" src="Images/17.png">
<br><br>

Remember earlier, when I listed the /home directory in order to find some other users?
<br>
So now you'll understand why:
<br>
I tried to SSH vigilante, but unseccessfully...
<br>
On the other hand, I know I have a valid password, but for who and how should I know about the other user?
<br>
But luckily we are smart enough to check those basic things!
<br>
So, lets SSH slade!
<br><br>
<img align="center" src="Images/18.png">
<br><br>

## SSH slade💻:

First, lets read the most important thing, the "user.txt" and paste it as an answer! ✅
<br><br>
<img align="center" src="Images/19.png">
<br><br>

Note that we have a strange file in the /home/slade, the ".Important":
<br><br>
<img align="center" src="Images/20.png">
<br><br>

## Rabbit hole🐰:
They want us to find a file called "Secret_Mission", actually I immediately knew it was rabbit hole...
<br>
But why not?
<br><br>
<img align="center" src="Images/21.png">
<br><br>

As I told you, just lorem ipsum (and waste of time😆).
<br><br>

## Privilege escalation👑:
So, let's do it in my way (as a kill chain and not as a story):
<br><br>
<img align="center" src="Images/22.png">
<br><br>

Wow, in the first try, in just a simple "sudo -l" we found a binary called "pkexec", let's see how we can exploit it:
<br><br>
<img align="center" src="Images/23.png">
<br><br>

Ok, so GTFObins gives us a command to run as sudo, let's try:
<br><br>
<img align="center" src="Images/24.png">
<br><br>

We rooted this machine, hope you learned something new, and most importantly enjoyed!

## Thank you for reading my walkthrough!
