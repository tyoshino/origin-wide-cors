# Origin-Wide CORS

Editor: Takeshi Yoshino

## Introduction

Extra round trip caused by CORS preflight has been adding significant delay to XHR/Fetch with non-simple headers/method. This is preventing performance sensitive applications from adopting web platform.

The Origin-Wide CORS mechanism improves this by allowing an origin server to announce CORS capability and parameters widely across resources.

## Design points

Starting with just summarizing ideas proposed at https://github.com/whatwg/fetch/issues/210

- general resource policy setting infra (e.g. CSP) vs. CORS only
  - https://github.com/whatwg/fetch/issues/210#issuecomment-211441909
  - mnot's original proposal
  - https://github.com/mikewest/cors-rfc1918/issues/7
- the address should be under control of the origin's administrator
- hierarchical design or single point
- extend the existing CORS preflight or ...
  - Origin-Wide-CORS-Supported: yes
    - Merge to Origin-Wide-CORS?
  - Origin-Wide-CORS: Origin= ; Headers= ;
- patterns
- file fetching + parsing vs. via HTTP headers
  - well-known vs. Access-Control-Policy: here
    - https://github.com/whatwg/fetch/issues/210#issuecomment-211441909
    - https://github.com/whatwg/fetch/issues/210#issuecomment-212193386
- expiration
  - Max-Age
  - Etag like versioning?
- scope, realm
  - https://github.com/whatwg/fetch/issues/210#issuecomment-179506649
  - each directory vs. subdirectories https://github.com/whatwg/fetch/issues/210#issuecomment-179561072
- include credentialed or non-credentialed only
- API side switch or internally? https://github.com/whatwg/fetch/issues/210#issuecomment-178476230
- should allow proxies to cache the CORS information https://github.com/whatwg/fetch/issues/210#issuecomment-178269098
- resolving inconsistent responses https://github.com/whatwg/fetch/issues/210#issuecomment-186501663
- policy signing https://github.com/whatwg/fetch/issues/210#issuecomment-211441909
  - JOSE
- JSON, CBOR, ...

## Plans

- Start with just allowing for announcing that the origin understands CORS and therefore it's safe to omit preflight. And then, extend it to enable announcing capability in more fine-grained form and detailed parameters
  - shouldn't be simply extended to HTTPS from HTTP
  - --> Too dangerous? https://github.com/whatwg/fetch/issues/210#issuecomment-233588495
- Have almost the same level of cumbersomeness as current preflight to prevent people from copy&pasting

## Strawman

- On the initial cross-origin non-simple request to an origin X from origin Y
  - The client A issues a preflight request to resource R
    - The preflight request MAY include `Origin-Wide-CORS-Supported: yes` header to announce it understands Origin-Wide CORS protocol
  - If a server B sees the `Origin-Wide-CORS-Supported` header entry in the received CORS preflight or actual CORS request, it MAY include the `Origin-Wide-CORS` header whose value is a Origin-Wide CORS descriptor
    - The `Origin-Wide-CORS-Supported` header must have either a value `yes` or `version` parameter or `tag` parameter
    - Any entry in the Origin-Wide CORS descriptor whose `url` field is pointing at a resource which is not under R, it must be ignored.

- On the initial request to a ...

### Origin-Wide-CORS header

The `Origin-Wide-CORS` header has a JSON formatted value quoted by quoted-string if needed.

```
{
  "cors": [
    {
      "url": "/service",
      "origin": "http://www.example.com",
      "max-age": 31536000,
      "credentials": false,
      "method": "get",
      "headernames": ["x-requested-with", "custom"]
    },
    ...
  ],
  "csp": ...
  "upgrade-insecure-requests": ...
}
```

- This parameter will be stored into the CORS preflight cache with the `origin-wide-cors` flag
  - Pathes such as "/service", "/service/x", "/service/x/y", "/service/x/y?z=foo", etc. are requested, this cache will be instantiated to a CORS preflight cache entry without the `origin-wide-cors` flag (creates entries for each headername in headernames)
    - matching is done from the first entry to the last entry
      - https://github.com/whatwg/fetch/issues/210#issuecomment-211470853
    - Partial update is not supported
    - The instantiation doesn't happen if there's existing active entry
  - Pathes such as "/servicex", "/servicex/y", etc. are not affected.
- Note: `origin` field doesn't support the wildcard "*". A concrete origin must be specified.
- `credentials` defaults to **false** meaning that this `Origin-Wide-CORS` parameters affects only non-credentialled requests
- The only allowed value for `credentials` is **false** for the initial version of the protocol
- An Origin-Wide-CORS descriptor fetched over HTTP is not applicable to fetches over HTTPS
- An Origin-Wide-CORS descriptor fetched over HTTPS is applicable to fetches over both HTTPS and HTTP

### Issues

- The descriptor gets cached together with the resource R? Separately maintained?
  - -> only in response to OPTIONS?

- Only root considering security concerns?
  - https://github.com/whatwg/fetch/issues/210#issuecomment-211793513

### General Origin-Wide Policy

This protocol can be generalized to allow for announcing various policies in addition to the CORS.

## Alternatives

- Standardize how to embed non-simple headers in a URL
  - See [this post](https://github.com/whatwg/fetch/issues/210#issue-129531743) by @wenbozhu
- Foreign fetch ServiceWorker

## References

- Well-Known Resources for HTTP OPTIONS: https://mnot.github.io/I-D/http-options-resources/

## Acknowledgement

Thanks to Ben Kelly, bifurcation, Jonas Sicking, roryhewitt, Mark Nottingham, Wenbo Zhu.
