# Welcome to my "Olympus" CTF walkthorugh!
You can find this CTF and many more on https://tryhackme.com/

## Self note:
In order to make this walkthrough as practicall as possible,
I added only the relevant pictures and explenations, in order to make it straightforward.

## :mag_right: Reconnaissance:
So, the firts thing to do In almost every CTF is to scan the network we are deal with.
In this case I used Nmap.

<img align="center" src="Images/1.png">

As we can see, we have two open ports, one is for SSH and the second for an HTTP server.
So lets investigate the web server and see what we can find there.

<img align="center" src="Images/2.png">

They tell us that the website is under development, but the older version is stil accessible.
Lets add the domain name to our /etc/hosts file and perform directory enumeration.
We want to enumerate the directories in order to find attack surface.

<img align="center" src="Images/3.png">

We found some interesting directories, but the most interest is the "~webmaster".
And after short visit in the website, I found an input field. 

## 💉 SQL injection:

<img align="center" src="Images/4.png">

Then I tried to inject a singel quotation mark ('), and received a MySQL error.
This error points on SQL database and specifically MySQL.

<img align="center" src="Images/5.png">

In addition, I found that every "category" I tried to access has a parameter in the URL.

<img align="center" src="Images/6.png">

At first I tried to perform manual SQL injection, (In my opinion this is the best practice to perform manual attacks, this how you get better and really understand what are you doing).
Unfortunately, I did not succeed, so I used SQLmap.
At first I've captured the HTTP request that includes the vulnerable parameter with burp suite, and then used it for the "-r" flag on SQLmap.

<img align="center" src="Images/7.png">

Lets take a look in the content we have found.
First the flag 😆!

<img align="center" src="Images/8.png">

Do not forget this strange file.

<img align="center" src="Images/9.png">

Also some credentials.

<img align="center" src="Images/10.png">

## 🔓 Password Cracking:

So let's sort it into individual files.

<img align="center" src="Images/11.png">

And crack "Prometheus.txt" with John, (the others took too long so probably out of scope).

<img align="center" src="Images/12.png">

We found the password for prometheus, what can we do with it? 
Remember the website from earlier?
We can log in and find the CMS panel.

## CMS and VHOST's💻

<img align="center" src="Images/13.png">

Interesting, It looks like we have a subdomain here.
It's called VHOST, VHOST can come in two shapes, one is for assign many IP's to one
domain name, it's called "IP-based" Vhost, and the seconed is the opposite,
It's ment for assign many domain names to one IP and called "Name-based" Vhost.

<img align="center" src="Images/14.png">

So lets add the "chat.olympus.thm" to our /etc/hosts, as we did earlier.

<img align="center" src="Images/15.png">

## Get a Shell 🐚
And look what we have found!

<img align="center" src="Images/16.png">

Remember the strange file we found earlier? now make you calculations according
to what I've makred in red and understand how it works.
Also upload a simple PHP reverse shell.

<img align="center" src="Images/17.png">

Prometheus told Zeus that he found an upload folder, you know the drill.

<img align="center" src="Images/18.png">

Finally we can check here the strange file from earlier.
But it is a rabbit hole 😆.

<img align="center" src="Images/19.png">

Just kidding, let's exploit this.
Remember the shell I've uploaded earlier to the chat?
So I canno't find it 😢, but we have access to the SQL database.
Enumerate the DB again and find the shell filename.

<img align="center" src="Images/20.png">

<img align="center" src="Images/21.png">

Let's listen to income traffic according to the port in the php shell file.
And it's seems we got our shell!

<img align="center" src="Images/22.png">

After little a manual enumeration of the filesystem I found the second flag.

<img align="center" src="Images/23.png">

## Lateral movement & Privilege Escalation 👑

Now we want to escalate our privileges, so as a basic I very recommend you do
develop a basic routine of enumeration before you trying to perform more advanced
privilege escalation.
In this case it's really worked.

<img align="center" src="Images/24.png">

This SUID binary looks strange, I didn's recognized it and so GTFObins.
Lets check what it's intended to do.
At first I read that as CPU-tils, and then understood that this is CP-utils 😆.
This binary has the privileges of Zeus, and I know Zeus has ".ssh" folder in his /home directory.
After short thought, I tried to copy his "id_rsa" file into /dev/shm/id_rsa.

<img align="center" src="Images/25.png">

This file is the private ssh key and we can use it in order to log in to his user.
So I started a simple python server and transfered the file to my Linux machine.
As a rule of thumb, "id_rsa" needs only Read and Write permission of their owner in order to work. 
So the command "chmod 600 id_rsa" will do the work.

<img align="center" src="Images/26.png">

After I tried to log in, I noticed that this file needs a password.
Again, you know the drill 🚀.

<img align="center" src="Images/27.png">

And we got a connection!

<img align="center" src="Images/28.png">

After long visit in Zeus account in search of privilege escalation vector, I found 
an interesting folder in /var/www/html that I wasn't able to read with www-data.
Cat the .php file and you will find a variable called $pass and its value.
After I searched the path in my browser I've found a sort of login page.
Don't forget we have the password!!!

<img align="center" src="Images/29.png">

And we found a root shell backdoor.

## Root Backdoor 🚪

<img align="center" src="Images/30.png">

In the webpage we instructed to add the ?ip=(your ip)$port=(your port) to the URL as
parameters.
But first open a listener.

<img align="center" src="Images/31.png">

We are root!!!
The root flag located in /root/root.flag

<img align="center" src="Images/32.png">

## Bonus Flag 🏳️

Prometheus recommend us to log in with SSH to fint the bonus flag, and so we do.
But what is the root password? 
Just kidding, we rooted the machine and we are the owners now.
As the owner I want the root's password to be "1" 😆.

<img align="center" src="Images/33.png">

Prometheus recemmended us to use Regex in order to find the bonus flag.
I know that all the flags have something in common.
So I used "sudo grep -r "flag{" / 2>/dev/null".

<img align="center" src="Images/34.png">

## Thank you for reading my walkthrough!
