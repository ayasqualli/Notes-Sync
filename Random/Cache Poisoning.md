When a request includes the `Authorization` header, the server assumes the response includes senstitive, user-specific data. In that case, it marks the response as private by default to prevent it from being cached and reused by others.

If the response doesn't include that header, the application must explicitly define it as private using the response header below. This prevents accidental reuse of sensitive data across users.

![[Pasted image 20251130134441.png]]

When the `Cache-Control: header` is set to `private`, this directiove ensures the response stays in private cache (like the browser) and doesn't go to shared caches.

For extra safety, you can also instruct proxies not to cache certain responses at all, especially for sensitive URIs by using headers like `Cache-Control: no-store` or ``no-cache`

## Shared cache:
 Shared caches fall in 2 broads categories:
- **Public , shared proxy caches** serve  multiple users and aim to improve performance by delivering cached content from the edge like *Cloudflare*
- **Managed, shared proxy caches** sit within corporate networks. Admins can fine-tune caching behaviour and even override standard HTTP caching rules for example *a company proxy server*

## Server-side origin caching
`origin` cache stores responses locally on the application server itself.
This cache helps reduce backend load by reusing results for identical requests. It never interacts with clients, which means attackers can't poison it except in rare or misconfigured setups.

> ==**Whether private, shared, or origin-based, all HTTP caches follow the same RFC-defined mechanisms. The way they store, reuse, and invalidate content depends on headers, configuration, and context - but the core logic remains consistent.**==

## Cache it or not ?
A response is cacheable if:
	1. **It includes any explicit cache-related response header** (e.g `Cache-Control`, `Expires`, `ETag`, ``Vary``, etc)
	2. **It does'nt include** `Cache-Control: no-store`
	3. **It has a final or heuristically cacheable status code** (eg 200, 203, 204, 206, 300, 301, 404, 414, 501)
	4. **It uses a cacheable HTTP method** (`GET` and `HEAD` by default, `POST` only if explicitly allowed via headers like `Cached-Control`)
	5. 