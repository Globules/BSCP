## Web cache poisoning via an unkeyed query parameter

This lab is vulnerable to web cache poisoning because it excludes a certain parameter from the cache key. A user regularly visits this site's home page using Chrome.

To solve the lab, poison the cache with a response that executes alert(1) in the victim's browser.

Hint :

Websites often exclude certain UTM analytics parameters from the cache key.

## Notes



## Resume



## Solve