# Tesla Fleet API public-key host

Tiny static site for:

`https://tesla.brianandkathi.com/.well-known/appspecific/com.tesla.3p.public-key.pem`

Tesla requires this exact path on the Allowed Origin domain: HTTP 200, no redirect, raw PEM starting with `-----BEGIN PUBLIC KEY-----`. Do not host the private key.

## Files

- `public/.well-known/appspecific/com.tesla.3p.public-key.pem` — public key only
- `public/_headers` — `Content-Type: application/x-pem-file` on that path
- No SPA fallback, no Functions, no `_redirects`

The matching private key is **not** in this repo. It lives at `%USERPROFILE%\.tesla-fleet\private-key.pem`.

## Deploy

Live preview (PEM already verified here):

`https://tesla-public-key.brian-952.workers.dev/.well-known/appspecific/com.tesla.3p.public-key.pem`

HTTP 200, `Content-Type: application/x-pem-file`, no redirect, raw `-----BEGIN PUBLIC KEY-----`. Tesla still needs that same file on `tesla.brianandkathi.com`, so the DNS CNAME has to move off Google Sites.

This is a static-assets Worker (same files as a Pages project). Direct Pages API access is not on the current token; connect this GitHub repo in the Cloudflare dashboard if you want a `*.pages.dev` hostname.

```powershell
cd tesla-public-key
npx wrangler deploy
```

Dashboard Pages (needed for `tesla.brianandkathi.com` while DNS stays at Google):

1. [Workers & Pages → Create → Pages → Connect to Git](https://dash.cloudflare.com/?to=/:account/workers-and-pages)
2. Repo `bhwithun/tesla-public-key`
3. Build command empty, output directory `public`
4. **Custom domains** → `tesla.brianandkathi.com`

`brianandkathi.com` DNS is not on Cloudflare (Google Sites / `ghs.googlehosted.com`). Replace only the `tesla` record:

| Type | Name | Target |
|------|------|--------|
| CNAME | tesla | tesla-public-key.pages.dev |

Do not change `brianandkathi.com` itself. After Pages is connected, use the hostname Cloudflare shows if it is not `tesla-public-key.pages.dev`.

## Verify (before Tesla partner register)

```powershell
curl.exe -sI "https://tesla.brianandkathi.com/.well-known/appspecific/com.tesla.3p.public-key.pem"
curl.exe -s "https://tesla.brianandkathi.com/.well-known/appspecific/com.tesla.3p.public-key.pem"
```

Need HTTP 200 (not 301/302/308) and a body that starts with `-----BEGIN PUBLIC KEY-----`.

If the other Grok session already generated a different key pair, replace the public PEM here, redeploy, and keep using that session's private key instead of `~\.tesla-fleet\private-key.pem`.
