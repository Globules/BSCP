## Labs tips

Every lab from access control has:

- `/admin` or hidden admin endpoints
- user identifiers (id, username, email) in requests
- role-based logic (admin, user, etc.)
- parameters or cookies controlling privileges
- possibility to modify requests (IDOR / parameter tampering)

## Link

### BSCP Academy

- [Access control](https://portswigger.net/web-security/access-control)

### BSCP Labs

- [Unprotected admin functionality](https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality)
- [Unprotected admin functionality with unpredictable URL](https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality-with-unpredictable-url)
- [User role controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-role-controlled-by-request-parameter)
- [User role can be modified in user profile](https://portswigger.net/web-security/access-control/lab-user-role-can-be-modified-in-user-profile)
- [User ID controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter)
- [User ID with unpredictable user IDs](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-unpredictable-user-ids)
- [User ID with data leakage in redirect](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-data-leakage-in-redirect)
- [User ID with password disclosure](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-password-disclosure)
- [Insecure direct object references (IDOR)](https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references)
- [URL-based access control bypass](https://portswigger.net/web-security/access-control/lab-url-based-access-control-can-be-circumvented)
- [Method-based access control bypass](https://portswigger.net/web-security/access-control/lab-method-based-access-control-can-be-circumvented)
- [Multi-step process without access control](https://portswigger.net/web-security/access-control/lab-multi-step-process-with-no-access-control-on-one-step)
- [Referer-based access control](https://portswigger.net/web-security/access-control/lab-referer-based-access-control)
- [Location-based access control bypass](https://portswigger.net/web-security/access-control/lab-location-based-access-control-can-be-circumvented)

### Other resources

- [What is Broken Access Control?](https://www.youtube.com/watch?v=hIzeGCiRn1M)