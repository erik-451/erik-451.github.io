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
Maquina Linux nivel medio

# Table of Contents
1. [Enumeración](#enumeracion)
2. [Explotación](#explotacion)
3. [Escalación de Privilegios](#escalacion)

---

## Enumeración: <a name="enumeracion"></a>

**Nmap:**
```bash
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2 (FreeBSD 20161230; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((FreeBSD) PHP/5.6.32)
```

**Puertos:**
- 22 SSH 
- 80 HTTP

La web muestra varios nombres de archivos php y una funcion que los inspecciona.

![Web](https://user-images.githubusercontent.com/47476901/124363915-20b4da00-dc36-11eb-8437-46720f7f3b8d.png)

Uno de los 4 archivos que muestra la web para testear nos llama la atención dado a que se llama "listfiles.php".
Lo ejecutamos en la función de la web para ver que contiene.

![listfiles](https://user-images.githubusercontent.com/47476901/124363919-24486100-dc36-11eb-8add-515567ee3da4.png)

Vemos que la ruta del script de listfiles.php termina en un archivo llamado "pwdbackup.txt"

- Contiene un texto encodeado en base64 y nos menciona que está 13 veces encodeado.

![pwdbackup](https://user-images.githubusercontent.com/47476901/124363925-2a3e4200-dc36-11eb-8651-32c183fcf500.png)

Decodeamos el base64 13 veces, yo lo haré de esta forma:
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

El resultado del texto encodeado parece ser una contraseña.

```
Contraseña: Charix!2#4%6&8(0
```

## Explotación: <a name="explotacion"></a>

Recordamos que la funcion de la web podia leer archivos...
Probemos con a mostrar /etc/passwd para ver si encontramos un usuario con el que usar la contraseña encontrada.

![LFI](https://user-images.githubusercontent.com/47476901/124363927-2f02f600-dc36-11eb-8699-73640adac21e.png)

Encontramos un usuario llamado Charix.

![passwd](https://user-images.githubusercontent.com/47476901/124363939-362a0400-dc36-11eb-865b-4c3e0bdf673e.png)

Sabemos que el servidor corre SSH, probamos a conectarnos con los datos recopilados.
```
User: charix
Password: Charix!2#4%6&8(0
```

![LoginSSH](https://user-images.githubusercontent.com/47476901/124363942-3924f480-dc36-11eb-87f2-1fe8ff82fbec.png)

## Escalación de privilegios: <a name="escalacion"></a>

- Encontramos un zip llamado secret.zip

![secretzip](https://user-images.githubusercontent.com/47476901/124363944-3c1fe500-dc36-11eb-9304-d427d8b9ec34.png)

Nos lo traemos a nuestra maquina y lo descomprimimos.

![netcat](https://user-images.githubusercontent.com/47476901/124363946-3fb36c00-dc36-11eb-85a4-320df942e353.png)

Esta protegido con contraseña, usaremos la contraseña del usuario:

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
Podemos comprobar si el usuario root está corriendo un servicio...
- Esta corriendo un servidor VNC por el puerto 5901

![ps aux](https://user-images.githubusercontent.com/47476901/124363949-46da7a00-dc36-11eb-89c9-a14fc484bd73.png)

Ya sabemos por donde tirar la escalación de privilegios.
Realizaremos un log forwarding redirigiendo el puerto al ssh para poder tener conexion directa al servicio VNC

Redirigimos la conexión al SSH para posteriormente acceder al VNC 

```bash
ssh -L 5901:127.0.0.1:5901 charix@10.10.10.84
```

![LogPoisoningSSH](https://user-images.githubusercontent.com/47476901/124363955-4b9f2e00-dc36-11eb-8e08-a3d572165a38.png)

Se puede observar en el netstat que tenemos conexión al puerto 5901 (VNC).

![netstat](https://user-images.githubusercontent.com/47476901/124364613-83a87000-dc3a-11eb-8d11-13f5b9ada104.png)

Usaremos el archivo del zip encontrado anteriormente para logearnos al VNC.
Estamos dentro.
```bash 
vncviewer 127.0.0.1:5901 -passwd secret 
```

![VNC](https://user-images.githubusercontent.com/47476901/124363966-5c4fa400-dc36-11eb-9487-9f117882af43.png)

Ya somos root
- Flag de root

![root txt](https://user-images.githubusercontent.com/47476901/124363970-607bc180-dc36-11eb-9876-f709a43b82a7.png)

#### Maquina Completada
