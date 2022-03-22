---
layout: post
title:  "SSH Server"
author: erik
categories: [ Protocolos ]
image: https://user-images.githubusercontent.com/47476901/128584491-96367ea6-31d6-4904-aa32-3e84a6069ca1.png
beforetoc: ""
toc: false
tags:  [ SSH, Blue Team ]
---
Configuracion basica de SSH

# Table of Contents
1. [Configurar SSH en el servidor](#configssh)
2. [Servidor reconocible en la red](#sshreconocible)
3. [Configurar SSH en el PC](#configsshpc)
4. [Compartir clave pública](#publickey)
5. [Login al SSH](#sshlogin)

---

**1-Configurar SSH en el servidor** <a name="configssh"></a>

Para comenzar con el SSH debemos ejecutar el siguiente comando:

```bash
ssh-keygen
```

![ssh-keygen](https://user-images.githubusercontent.com/47476901/128584495-375f4c41-f1ed-44b2-a6e7-da3c3a9dc5c7.png)

**2-Servidor reconocible en la red** <a name="sshreconocible"></a>

Para que nuestro ordenador pueda conectarse al servidor tienen que estar en la misma red, es decir si lanzamos un ping tienen que responder ambos.

- Servidor a ordenador:

![ServerToPc](https://user-images.githubusercontent.com/47476901/128584499-2339f6ad-d33c-48b9-b71e-6b7d09396257.png)

- Ordenador a Servidor:

![PcToServer](https://user-images.githubusercontent.com/47476901/128584503-5039e056-b93c-42e9-b199-5b92c78d7cd9.png)

**3-Configurar SSH en el PC** <a name="configsshpc"></a>

Hacemos lo mismo que en el servidor:
```bash
ssh-keygen
```
![ssh-keygenPC](https://user-images.githubusercontent.com/47476901/128584508-69e8c690-d46c-44b8-9fd2-9be81f8783b4.png)

**4-Compartir clave pública** <a name="publickey"></a>

Para poder conectarnos al servidor sin tener que usar la contraseña podemos mandar nuestra clave pública al "autorized_keys" del servidor.

```bash
cat .\.ssh\id_rsa.pub | ssh erik@192.168.1.155 "cat >> ~/.ssh/authorized_keys"
```

![autorized](https://user-images.githubusercontent.com/47476901/128584510-6b4afd14-b4ed-4830-a7d0-76310e1911a5.png)

**5-Login al SSH** <a name="sshlogin"></a>

Al acabar esto ya podemos logearnos al ssh sin necesidad de contraseña.

![LoginSSH](https://user-images.githubusercontent.com/47476901/128584515-e424dd37-e0ce-4bee-8b80-f813f19f3033.png)
