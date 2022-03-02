---
layout: post
title:  "Attack Active Directory"
author: erik
categories: [ Active Directory ]
image: https://user-images.githubusercontent.com/47476901/131404883-b4abe28e-a673-4660-ba86-fe2f46530810.jpg
beforetoc: ""
toc: false
tags: [ Active Directory, Red Team ]
---

En esta máquina vulneraremos un dominio de Active Directory.

# Table of Contents
1. [Enumeration Active Directory](#EnumerationAD)
2. [Explotation](#Explotation)

## Enumeration AD: <a name="EnumerationAD"></a>
#### Nmap:
```bash
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2021-08-30 16:59:43Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
49665/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49670/tcp open  msrpc         Microsoft Windows RPC
49673/tcp open  msrpc         Microsoft Windows RPC
49680/tcp open  msrpc         Microsoft Windows RPC
49716/tcp open  msrpc         Microsoft Windows RPC
```
Dominio:
- vulnnet-rst.local

#### SmbClient:

En la enumeracion de samba listamos todos los archivos compartidos, y luego inspecionaremos uno a uno.
- <a href="https://www.samba.org/samba/download" target="_blank">SmbClient</a>

![smbclient_listing](https://user-images.githubusercontent.com/47476901/131402701-ce0fe921-d129-4471-82c6-24501f0e1308.png)

- SMB: VulnNet-Enterprise-Anonymous

![VulnNet-Enterprise-Anonymous](https://user-images.githubusercontent.com/47476901/131402731-ef0d65e7-9a2d-44f0-ab8b-2f70068d502b.png)

- SMB: VulnNet-Business-Anonymous

![VulnNet-Business-Anonymous](https://user-images.githubusercontent.com/47476901/131402753-1cfcbf4f-28d5-48ca-b453-9857aa0b8ff3.png)

Analizamos los nombres que nos muestan los ficheros para crear un diccionario personalizado y usarlo al enumerar los posibles usuarios en el AD.

![recogeruser](https://user-images.githubusercontent.com/47476901/131402776-869348bf-d3b3-4911-9ef5-d36760d71c3b.png)

Diccionario:

![diccionario](https://user-images.githubusercontent.com/47476901/131402807-9f24c798-b8d9-49fb-88a5-31e39fee9995.png)

#### Kerbrute:

Hacemos fuerza bruta para saber que usuarios son validos a nivel de dominio usando nuestro diccionario creado anteriormente
- <a href="https://github.com/ropnop/kerbrute" target="_blank">Kerbrute</a>

![kerbrute](https://user-images.githubusercontent.com/47476901/131402818-de939578-3873-45ef-a4e9-4b306357a722.png)

#### impacket-lookupsid:

Extrae los usuarios validos del sistema.
- <a href="https://github.com/SecureAuthCorp/impacket/blob/master/examples/lookupsid.py" target="_blank">Lookupsid</a>

![lookupsid](https://user-images.githubusercontent.com/47476901/131402828-1f05eac2-80d5-494e-8599-cda86e24c50a.png)

#### impacket-GetNPUsers:

Si tenemos una lista de usuarios validos hacemos el ataque **as-rep roast**

<b><i><em style="font-size: 13px;">
El tostado AS-REP es una técnica que permite recuperar hashes de contraseña para usuarios que tienen seleccionada la propiedad No requiere autenticación previa de Kerberos: esos hashes se pueden descifrar sin conexión, de manera similar a como se hace en T1208: Kerberoasting
</em></i></b>

GetNPUsers intentará recopilar las respuestas AS_REP que no son de autenticación previa para una lista determinada de nombres de usuario. Estas respuestas se cifrarán con la contraseña del usuario, que luego se puede descifrar sin conexión.
- <a href="https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetNPUsers.py" target="_blank">GetNPUsers</a>

![impacket-GetNPUsers](https://user-images.githubusercontent.com/47476901/131402842-2e67866b-7d8a-4e54-b42c-99af25852bb5.png)

#### John The Ripper:
Crackeamos el hash para sacar la contraseña del usuario
- <a href="https://www.openwall.com/john/" target="_blank">JohnTheRipper</a>

![John](https://user-images.githubusercontent.com/47476901/131403225-2ff909e6-56fe-4a43-9914-b5f94c849fbe.png)
- Usuario: t-skid
- Contraseña: tj072889*  

#### Crackmapexec:
Esta herramienta se usa para recopilar informacion sobre el active directory.
- <a href="https://github.com/byt3bl33d3r/CrackMapExec" target="_blank">Crackmapexec</a>

![Crackmapexec](https://user-images.githubusercontent.com/47476901/131402847-0984fe0e-917b-4fa6-b023-ec6a28f41727.png)

- Crackmapexec module spider_plus:
De manera recursiva muestra los archivos a los que tiene acceso el usuario indicado.

![spider](https://user-images.githubusercontent.com/47476901/131402860-f4ecb13d-9ca1-4685-9c8d-f268a8b777b2.png)

Nos crea el resultado en tmp.
Lo inspeccionamos y encontramos una ruta interesante.

![spiderresult](https://user-images.githubusercontent.com/47476901/131402873-285ae9b8-6e24-4fda-ac50-77a84663e196.png)

#### smbclient NETLOGON:
Entramos a la carpeta netlogon y extraemos el fichero para analizarlo posteriormente.

![netlogon](https://user-images.githubusercontent.com/47476901/131402885-6c21b663-7759-45f2-972a-68d564fa9f1e.png)

#### Password User:
En el script encontramos una contraseña de un usuario

![password user](https://user-images.githubusercontent.com/47476901/131402900-e6f113d0-5982-4411-bea3-0fb77f1f2dc7.png)
Una vez tengamos la contraseña del usuario probamos a ver que hashes podemos dumpear con la tecnica de pass the hash

## Explotation: <a name="Explotation"></a>
#### Pass the hash

Con esto podemos darnos acceso al servidor sin necesidad de aportar una contraseña

![passthehash](https://user-images.githubusercontent.com/47476901/131402921-beb788f6-17a0-4a14-8f07-feeac377e66a.png)

El hash del administrador nos permite entrar sin contraseña a su cuenta remotamente.
Para ello usamos psexec:
- <a href="https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py" target="_blank">Psexec</a>

```bash
impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:c2597747aa5e43022a3a3049a3c3b09d  Administrator@10.10.204.28
```
Ya somos administradores del dominio

![psexec](https://user-images.githubusercontent.com/47476901/131402932-5a53aa6a-11fc-422e-882d-84737500e182.png)

