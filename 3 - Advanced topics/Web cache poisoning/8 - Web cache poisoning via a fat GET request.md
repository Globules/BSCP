## Web cache poisoning via a fat GET request

This lab is vulnerable to web cache poisoning. It accepts GET requests that have a body, but does not include the body in the cache key. A user regularly visits this site's home page using Chrome.

To solve the lab, poison the cache with a response that executes alert(1) in the victim's browser.

## Notes



## Resume



## Solve