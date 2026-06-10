## Features to Enumerate

- Avatar upload
- Uploaded file URL
- Extension validation
- MIME type validation
- File content validation
- Apache `.htaccess`

---

## What to look for

- PHP execution in upload directory
- Trusted `Content-Type`
- Path traversal in `filename`
- Extension blacklist bypass
- Null byte filename bypass
- Image content validation bypass
- EXIF metadata PHP payload

## Common endpoint

- /my-account/avatar
- /files/avatars/
- /files/

