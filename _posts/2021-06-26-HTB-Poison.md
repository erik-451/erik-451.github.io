---
layout: post
title:  "HackTheBox - Poison"
author: erik
categories: [ HTB ]
image: https://user-images.githubusercontent.com/47476901/155025245-af677067-1430-4cfa-957e-8220a8dd4790.png
beforetoc: ""
toc: false
tags: [ HTB, VNC, Linux ]
---
Linux machine medium level

# Table of Contents
1. [Enumeration](#enumeration)
2. [Exploitation](#exploitation)
3. [Escalation of Privileges](#escalation)

---

## Enumeration: <a name="enumeration"></a>

**Nmap:**
```bash
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2 (FreeBSD 20161230; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((FreeBSD) PHP/5.6.32)
```

**Ports:**
- 22 SSH 
- 80 HTTP

The web shows several php file names and a function that inspects them.

![Web](https://user-images.githubusercontent.com/47476901/124363915-20b4da00-dc36-11eb-8437-46720f7f3b8d.png)

One of the 4 files that the web shows for testing catches our attention because it is called "listfiles.php".
We run it in the web function to see what it contains.


![listfiles](https://user-images.githubusercontent.com/47476901/124363919-24486100-dc36-11eb-8add-515567ee3da4.png)

We see that the path of the listfiles.php script ends in a file called "pwdbackup.txt".

- It contains base64 encoded text and mentions to us that it is 13 times encoded.

![pwdbackup](https://user-images.githubusercontent.com/47476901/124363925-2a3e4200-dc36-11eb-8651-32c183fcf500.png)

We decode the base64 13 times, I will do it this way:
```bash
 echo "Vm0wd2QyUXlVWGxWV0d4WFlURndVRlpzWkZOalJsWjBUVlpPV0ZKc2JETlhhMk0xVmpKS1IySkVU
bGhoTVVwVVZtcEdZV015U2tWVQpiR2hvVFZWd1ZWWnRjRWRUTWxKSVZtdGtXQXBpUm5CUFdWZDBS
bVZHV25SalJYUlVUVlUxU1ZadGRGZFZaM0JwVmxad1dWWnRNVFJqCk1EQjRXa1prWVZKR1NsVlVW
M040VGtaa2NtRkdaR2hWV0VKVVdXeGFTMVZHWkZoTlZGSlRDazFFUWpSV01qVlRZVEZLYzJOSVRs
WmkKV0doNlZHeGFZVk5IVWtsVWJXaFdWMFZLVlZkWGVHRlRNbEY0VjI1U2ExSXdXbUZEYkZwelYy
eG9XR0V4Y0hKWFZscExVakZPZEZKcwpaR2dLWVRCWk1GWkhkR0ZaVms1R1RsWmtZVkl5YUZkV01G
WkxWbFprV0dWSFJsUk5WbkJZVmpKMGExWnRSWHBWYmtKRVlYcEdlVmxyClVsTldNREZ4Vm10NFYw
MXVUak5hVm1SSFVqRldjd3BqUjJ0TFZXMDFRMkl4WkhOYVJGSlhUV3hLUjFSc1dtdFpWa2w1WVVa
T1YwMUcKV2t4V2JGcHJWMGRXU0dSSGJFNWlSWEEyVmpKMFlXRXhXblJTV0hCV1ltczFSVmxzVm5k
WFJsbDVDbVJIT1ZkTlJFWjRWbTEwTkZkRwpXbk5qUlhoV1lXdGFVRmw2UmxkamQzQlhZa2RPVEZk
WGRHOVJiVlp6VjI1U2FsSlhVbGRVVmxwelRrWlplVTVWT1ZwV2EydzFXVlZhCmExWXdNVWNLVjJ0
NFYySkdjR2hhUlZWNFZsWkdkR1JGTldoTmJtTjNWbXBLTUdJeFVYaGlSbVJWWVRKb1YxbHJWVEZT
Vm14elZteHcKVG1KR2NEQkRiVlpJVDFaa2FWWllRa3BYVmxadlpERlpkd3BOV0VaVFlrZG9hRlZz
WkZOWFJsWnhVbXM1YW1RelFtaFZiVEZQVkVaawpXR1ZHV210TmJFWTBWakowVjFVeVNraFZiRnBW
VmpOU00xcFhlRmRYUjFaSFdrWldhVkpZUW1GV2EyUXdDazVHU2tkalJGbExWRlZTCmMxSkdjRFpO
Ukd4RVdub3dPVU5uUFQwSwo="|base64 -d |base64 -d|base64 -d |base64 -d|base64 -d |base64 -d|base64 -d |base64 -d|base64 -d |base64 -d|base64 -d |base64 -d|base64 -d
```

The result of the encoded text appears to be a password.

```
Contraseña: Charix!2#4%6&8(0
```

## Exploitation: <a name="exploitation"></a>

We remember that the web function could read files...
Let's try displaying /etc/passwd to see if we can find a user to use the password found.

![LFI](https://user-images.githubusercontent.com/47476901/124363927-2f02f600-dc36-11eb-8699-73640adac21e.png)

We found a user named Charix.

![passwd](https://user-images.githubusercontent.com/47476901/124363939-362a0400-dc36-11eb-865b-4c3e0bdf673e.png)

We know that the server runs SSH, we try to connect with the collected data.
```
User: charix
Password: Charix!2#4%6&8(0
```

![LoginSSH](https://user-images.githubusercontent.com/47476901/124363942-3924f480-dc36-11eb-87f2-1fe8ff82fbec.png)

## Privilege escalation: <a name="escalacion"></a>

- We found a zip file called secret.zip

![secretzip](https://user-images.githubusercontent.com/47476901/124363944-3c1fe500-dc36-11eb-9304-d427d8b9ec34.png)

We bring it to our machine and unzip it.

![netcat](https://user-images.githubusercontent.com/47476901/124363946-3fb36c00-dc36-11eb-85a4-320df942e353.png)

It is password protected, we will use the user's password:

```bash
unzip secret.zip
Archive:  secret.zip
	[secret.zip] secret password: Charix!2#4%6&8(0
```

![unzip](https://user-images.githubusercontent.com/47476901/124363947-42ae5c80-dc36-11eb-93f4-a6b0a57103ab.png)

Con el comando:
```bash
ps aux
```
We can check if the root user is running a service...
- Is running a VNC server on port 5901
- 
![ps aux](https://user-images.githubusercontent.com/47476901/124363949-46da7a00-dc36-11eb-89c9-a14fc484bd73.png)

Now we know how to perform the privilege escalation.
We will perform a log forwarding redirecting the port to the ssh to be able to have direct connection to the VNC service.

Redirect the connection to SSH to access the VNC later on. 

```bash
ssh -L 5901:127.0.0.1:5901 charix@10.10.10.84
```

![LogPoisoningSSH](https://user-images.githubusercontent.com/47476901/124363955-4b9f2e00-dc36-11eb-8e08-a3d572165a38.png)

It can be seen in the netstat that we have connection to port 5901 (VNC).

![netstat](https://user-images.githubusercontent.com/47476901/124364613-83a87000-dc3a-11eb-8d11-13f5b9ada104.png)

We will use the zip file found above to login to the VNC.
We are in.
```bash 
vncviewer 127.0.0.1:5901 -passwd secret 
```

![VNC](https://user-images.githubusercontent.com/47476901/124363966-5c4fa400-dc36-11eb-9487-9f117882af43.png)

Ya somos root
- Flag de root
- 
![root txt](https://user-images.githubusercontent.com/47476901/124363970-607bc180-dc36-11eb-9876-f709a43b82a7.png)

**Completed Machine**
