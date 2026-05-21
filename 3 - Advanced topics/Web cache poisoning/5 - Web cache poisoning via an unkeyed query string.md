## Web cache poisoning via an unkeyed query string

This lab is vulnerable to web cache poisoning because the query string is unkeyed. A user regularly visits this site's home page using Chrome.

To solve the lab, poison the home page with a response that executes alert(1) in the victim's browser.

Hint :

- If you're struggling, you can use the Pragma: x-get-cache-key header to display the cache key in the response. This applies to some of the other labs as well.

- Although you can't use a query parameter as a cache buster, there is a common request header that will be keyed if present. You can use the Param Miner extension to automatically add a cache buster header to your requests.

## Notes



## Resume



## Solve