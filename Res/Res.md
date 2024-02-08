# Welcome to my "Res" CTF walkthorugh 💻!
You can find this CTF and many more on https://tryhackme.com/

## Self note:
In order to make this walkthrough as practicall as possible,
I added only the relevant pictures and explenations, in order to make it straightforward.

## Reconnaissance🔍:
First of all, I run Nmap scan in order to check for open ports.
<br><br>
<img align="center" src="Images/1.png">
<br><br><br>

As we can see, we have two open ports(80, 6379) and now we can answer the first four questions!
<br><br>
1: 2
<br>
2: redis
<br>
3: 6379
<br>
4: 6.0.7
<br><br><br>

Let's enumerate further:
<br>
Nmap can scan the redis server and give some more helpful info:
<br><br>
<img align="center" src="Images/2.png">
<br><br><br>

In addition I checked the HTTP server at port 80 but this is just the default page of Apache...
<br><br>
<img align="center" src="Images/3.png">
<br><br><br>

So I decided to run a GoBuster scan in order to search for hidden pages:
<br><br>
<img align="center" src="Images/4.png">
<br><br><br>
Nothing interesting...
<br><br>

## Redis Exploitation🔓:
I don't know the Redis database like I know SQL's so I decided to search some information on the interner first...
<br>
I found a some websites for Redis exploitation but I want to recommend on "Hacktricks" that provide a lot of information about services and exploitation methods.
<br>
I tried to enter the Redis DB and found that there is no authentication...
<br><br>
<img align="center" src="Images/5.png">
<br><br><br>
Then I found that I can create a webshell and use it within the HTTP server from earlier.
<br><br>
<img align="center" src="Images/6.png">
<br><br><br>

I've tried to test it and the webpage really returned me the correct output.
<br><br>
<img align="center" src="Images/7.png">
<br><br><br>

## Getting a shell🐚:
So now, when we know we can run arbitraty commands, lets get a reverse shell!
<br><br>
<img align="center" src="Images/8.png">
<br><br><br>
I provided with the "www-data" user and checked the /home directory.
<br>
In Vianka's home directory I found the "user.txt" flag.
<br><br>
<img align="center" src="Images/9.png">
<br><br><br>

## Privilege Escalation💪:
Now we want to get her user, and perform a lateral movement.
<br>
So, I manually enumerated for privilege escalation vectors.
<br>
I found an unusual SUID called "XXD" with the Root's permissions, so I checked on GTFObins what it can do.
<br><br>
<img align="center" src="Images/10.png">
<br><br><br>

It turns out that misconfigured SUID permissions on this binary can lead to sensitive file reading so I decided to read the /etc/shadow file,
<br>
and succeed!
<br><br>
<img align="center" src="Images/11.png">
<br><br><br>

If I can read the /etc/shadow I can combine it with the /etc/passwd and further crack it with tools like JTR.
<br><br>
<img align="center" src="Images/12.png">
<br><br><br>

Here I copied the two relevant fields on both files and entered the /passwd to a file called "user.txt" and the /shadow to a file called "pass.txt".
<br>
Then I unshadowed them to a one file and user JTR in order to reveal the password.
<br><br>
<img align="center" src="Images/13.png">
<br><br><br>
<img align="center" src="Images/14.png">
<br><br><br>

##Being Root👑:
I switched the user to Vianka and start looking for privilage escalation vectors again.
<br><br>
<img align="center" src="Images/15.png">
<br><br><br>

Note that when I run the "ID" command, I was able to see that Vianka is in the "Sudo" group, what means that she probably can run some sudo commands.
<br>
I checked that with "sudo -l" command and I had her password, so it's not a problem.
<br><br>
<img align="center" src="Images/16.png">
<br><br><br>

Vianka in a big problem, she doesn't know how to configure permissions and she has a fully sudo rights, so I switched to Root easily!
<br>
Note how she has (ALL : ALL) ALL.
<br><br>
<img align="center" src="Images/17.png">
<br><br><br>

Thats it, now we can see the "root.txt" flag and finish the CTF!

## Thank you for reading my walkthrough!



























