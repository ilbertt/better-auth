---
"better-auth": minor
"@better-auth/core": minor
"@better-auth/oauth-provider": minor
---

`baseURL` is now always the canonical, stable origin of your auth server. The object form (`baseURL: { allowedHosts, fallback, protocol }`) is removed. Multi-domain, preview, and white-label deployments now list their extra hosts in `trustedOrigins`, which already accepts both a static array and a per-request function.

Cookies and self-referential links (email verification, magic links, password reset) follow the host a request arrives on when that origin is trusted, and otherwise fall back to `baseURL`. Identity-bearing values stay anchored to `baseURL` so they no longer drift with the request host: the OAuth/OIDC issuer, JWT `iss`/`aud`, the social-login `redirect_uri`, and the Passkey relying-party id. To vary one of those per tenant, set that concern's own option (`jwt.issuer`, the OAuth Provider issuer, the Passkey `rpID`, or `oAuthProxy.productionURL`).

Migration: replace the object form with a canonical string plus `trustedOrigins`.

```ts
// Before
betterAuth({
  baseURL: { allowedHosts: ["myapp.com", "*.vercel.app"], fallback: "https://myapp.com" },
})

// After
betterAuth({
  baseURL: "https://myapp.com",
  trustedOrigins: ["https://*.vercel.app"],
})
```

For white-label domains stored in a database, pass a function to `trustedOrigins` that resolves valid origins from the request. The `BaseURLConfig` and `DynamicBaseURLConfig` types are removed from `@better-auth/core`.
