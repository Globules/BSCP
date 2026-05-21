## Parameter cloaking

This lab is vulnerable to web cache poisoning because it excludes a certain parameter from the cache key. There is also inconsistent parameter parsing between the cache and the back-end. A user regularly visits this site's home page using Chrome.

To solve the lab, use the parameter cloaking technique to poison the cache with a response that executes alert(1) in the victim's browser.

Hint :

The website excludes a certain UTM analytics parameter

## Notes



## Resume



## Solve