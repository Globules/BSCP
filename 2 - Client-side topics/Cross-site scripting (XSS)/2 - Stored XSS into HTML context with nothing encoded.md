## 2 - Stored XSS into HTML context with nothing encoded

This lab contains a stored cross-site scripting vulnerability in the comment functionality.

To solve this lab, submit a comment that calls the alert function when the blog post is viewed.


## Notes

- Possibility to leave a comment

## Resume

1. Go on any post page
2. Leave a comment with XSS payload

## Solve

Go on any page where you can leave a comment : 

`/post?postId=6`

Scroll down and leave a comment, on the **Comment Section** use the payload : 

```js
<script>alert()</script>
```