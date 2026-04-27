## Labs tips

Every lab from path traversal has:

- file retrieval functionality (images, documents, etc.)
- parameters referencing file paths (e.g. `filename`, `file`, `path`)
- lack of proper validation or sanitization on user input
- possibility to traverse directories using sequences like `../`
- filters that can often be bypassed (encoding, absolute paths, null bytes)

## Link

### BSCP Academy

- [Path traversal](https://portswigger.net/web-security/file-path-traversal)

### BSCP Labs

- [File path traversal, simple case](https://portswigger.net/web-security/file-path-traversal/lab-simple)
- [Traversal sequences blocked with absolute path bypass](https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)
- [Traversal sequences stripped non-recursively](https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively)
- [Traversal sequences stripped with superfluous URL-decode](https://portswigger.net/web-security/file-path-traversal/lab-superfluous-url-decode)
- [Validation of start of path](https://portswigger.net/web-security/file-path-traversal/lab-validate-start-of-path)
- [Validation of file extension with null byte bypass](https://portswigger.net/web-security/file-path-traversal/lab-validate-file-extension-null-byte-bypass)

### Other resources

- [What is directory traversal? (file path traversal) - Web Security Academy](https://www.youtube.com/watch?v=NQwUDLMOrHo)