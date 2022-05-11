---
layout: post
title:  "SSH Server"
author: erik
categories: [ Protocols ]
image: https://user-images.githubusercontent.com/47476901/128584491-96367ea6-31d6-4904-aa32-3e84a6069ca1.png
beforetoc: ""
toc: false
tags:  [ SSH, Blue Team ]
---
Configuracion basica de SSH

# Table of Contents
1. [Configuring SSH on the server](#configssh)
2. [Recognizable server in the network](#sshrecognizable)
3. [Configuring SSH on the PC](#configsshpc)
4. [Public key sharing](#publickey)
5. [SSH Login](#sshlogin)

---

### 1-Configuring SSH on the server <a name="configssh"></a>

To start with SSH we must execute the following command:

```bash
ssh-keygen
```

![ssh-keygen](https://user-images.githubusercontent.com/47476901/128584495-375f4c41-f1ed-44b2-a6e7-da3c3a9dc5c7.png)

### 2-Recognizable server in the network  <a name="sshrecognizable"></a>

For our computer to be able to connect to the server, they must be in the same network, that is to say, if we launch a ping, both must respond.

- Server to computer:

![ServerToPc](https://user-images.githubusercontent.com/47476901/128584499-2339f6ad-d33c-48b9-b71e-6b7d09396257.png)

- Computer to Server:

![PcToServer](https://user-images.githubusercontent.com/47476901/128584503-5039e056-b93c-42e9-b199-5b92c78d7cd9.png)

### 3-Configuring SSH on the PC <a name="configsshpc"></a>

We do the same as on the server:
```bash
ssh-keygen
```
![ssh-keygenPC](https://user-images.githubusercontent.com/47476901/128584508-69e8c690-d46c-44b8-9fd2-9be81f8783b4.png)

### 4-Public key sharing <a name="publickey"></a>

To be able to connect to the server without having to use the password we can send our public key to the "autorized_keys" of the server.

```bash
cat .\.ssh\id_rsa.pub | ssh erik@192.168.1.155 "cat >> ~/.ssh/authorized_keys"
```

![autorized](https://user-images.githubusercontent.com/47476901/128584510-6b4afd14-b4ed-4830-a7d0-76310e1911a5.png)

### 5-SSH Login <a name="sshlogin"></a>

After finishing this we can log in to the ssh without password.

![LoginSSH](https://user-images.githubusercontent.com/47476901/128584515-e424dd37-e0ce-4bee-8b80-f813f19f3033.png)
