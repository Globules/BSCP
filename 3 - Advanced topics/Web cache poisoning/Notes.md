## Constructing a web cache poisoning attack

Generally speaking, constructing a basic web cache poisoning attack involves the following steps:

1. Identify and evaluate unkeyed inputs.
2. Elicit a harmful response from the back-end server.
3. Get the response cached.

## Identify and evaluate unkeyed inputs

Any web cache poisoning attack relies on manipulation of unkeyed inputs, such as headers. Web caches ignore unkeyed inputs when deciding whether to serve a cached response to the user. This means you can use them to inject your payload and elicit a **poisoned** response which, if cached, will be served to all users whose requests have the matching cache key.

The first step is identifying unkeyed inputs that are supported by the server. You can do this manually by adding random inputs to requests and observing whether they affect the response. Sometimes the effect is obvious, such as direct reflection in the response. Other times it is more subtle and requires comparison between responses with and without the injected input.

## Param Miner

You can automate this process by using the Param Miner extension in Burp. Right-click a request and choose **Guess headers**. Param Miner then runs in the background, sending requests containing different candidate headers from its built-in list. If one of those headers affects the response, Param Miner reports it in Burp.

A cache buster should be used when testing on a live website. Otherwise, your requests might poison the cache for real users. You can add a unique parameter to the request line manually, or configure Param Miner to add cache busters automatically.

## Elicit a harmful response

Once you identify an unkeyed input, the next step is understanding how the website processes it. If the input is reflected without proper sanitization, or if it is used to dynamically generate other data, it may be a good entry point for cache poisoning.

## Get the response cached

Manipulating inputs to elicit a harmful response is only half the battle. You also need the response to be cached. Whether a response is cached depends on factors such as file extension, content type, route, status code, and response headers. This often requires experimentation across different pages and cache behaviors.

## Exploiting cache design flaws

In some cases, web cache poisoning vulnerabilities arise from general flaws in cache design. In other cases, the implementation of the cache or the website introduces unexpected behavior that can be exploited.

A common pattern is when an unkeyed input is reflected in a cacheable response without proper sanitization. For example, a header such as `X-Forwarded-Host` may be used to dynamically generate a URL in the response. If that header is not included in the cache key, an attacker may poison the cached response with injected content.

Another common case is unsafe handling of resource imports. Some websites use unkeyed headers to generate URLs for imported scripts or other resources. If an attacker can cause the response to reference a domain they control, they may get malicious content imported by victims who receive the cached response.

Cookies can also be involved. If a cookie influences the response but is ignored by the cache key, the cached response may reflect attacker-controlled content to other users.

Sometimes multiple headers must be combined. For example, one header may be used to force a redirect while another controls the target of that redirect. Together, they can produce a cacheable malicious response.

## Exposing too much information

Some responses reveal information that helps with cache poisoning. For example, cache-control directives, cache age, or cache status headers can show whether a response is cached and how long it will remain valid.

The `Vary` header can also help by showing which request headers are part of the cache key. This can be useful when targeting specific user agents or other subsets of users.

## Exploiting cache implementation flaws

Some caches apply transformations to keyed components when building the cache key. That can create exploitable discrepancies between what the cache keys and what the application processes.

### Unkeyed port

The `Host` header is often part of the cache key, but some systems exclude the port. In that case, the port can be used as a payload carrier while still preserving a cache key that matches normal requests.

### Unkeyed query string

Some caches exclude the entire query string from the cache key. This can make reflected issues more dangerous because a poisoned response can be served to users visiting a normal URL, not just a specially crafted one.

### Unkeyed query parameters

Sometimes only specific query parameters are excluded from the cache key. If those parameters are not used by the application, they may still be useful as a cloaking mechanism for malicious input.

### Parameter cloaking

Parsing differences between the cache and the application can allow payloads to be hidden inside otherwise excluded parameters. For example, one parser may treat a second `?` as the start of a new parameter while another does not. This can allow an attacker to inject a value the application will use, while the cache still sees an innocent key.

A related issue occurs when the application and cache disagree on delimiters such as `&` and `;`. If duplicate parameters are interpreted differently, the application may use the attacker’s value while the cache stores the harmless one.

### Fat GET requests

In some cases, the HTTP method is not keyed. This can allow a POST-like payload in the body of a GET request to poison the cache. If method override headers are supported and unkeyed, the effect can be similar.

### Dynamic content in resource imports

If a cached resource file reflects input into imported content, that can lead to code execution or CSS-based attacks. In some cases, cache poisoning can make a site import malicious content or inject CSS that affects users across the site.

### URL normalization

If the cache normalizes encoded and unencoded input to the same cache key, it may be possible to exploit otherwise unexploitable reflected XSS. The attacker poisons the cache with a raw payload, and the victim’s encoded request maps to the same key.

### Cache key injection

If the cache concatenates keyed components without proper escaping, an attacker may be able to inject extra separators into the cache key itself. That can make two different requests map to the same cached entry, enabling poisoning.

## Poisoning internal caches

Some applications implement caching inside the application itself rather than relying only on an external cache. These internal caches may store reusable fragments rather than full responses. If a fragment is shared widely, poisoning it can affect many pages or even the entire site.

Internal caches can be difficult to identify because there is often no obvious feedback. Signs include content from multiple requests appearing together, or input reflected across pages where it was never injected directly.

Because these caches may not have a meaningful cache key, standard cache-busting techniques may not work. When testing, it is safer to use a domain you control so that you can manage the consequences if something is cached unexpectedly.

## Prevention

The safest defense is to disable caching when it is not needed. If caching is required, restrict it to truly static responses and be careful about what counts as static.

Other good defenses include:

- Do not rely on unnecessary request headers.
- Rewrite requests instead of excluding inputs from the cache key.
- Avoid accepting fat GET requests.
- Patch client-side issues even if they seem unexploitable.
- Review how third-party technologies handle headers, query strings, and normalization.

## Key idea

Web cache poisoning works when an attacker can make the application generate a harmful response while the cache stores it under a key that also matches normal victim requests. The exploit is successful when a poisoned response is later served to other users.