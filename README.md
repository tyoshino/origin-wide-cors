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
- JSON, CBOR, ...
- JOSE

## Plan

- Start with just allowing for announcing that the origin understands CORS and therefore it's safe to omit preflight
  - shouldn't be simply extended to HTTPS from HTTP
- Extend it to enable announcing capability in more fine-grained form and detailed parameters

## Idea 1

- On the initial cross-origin non-simple request to an origin X from origin Y

- On the initial request to a 


## Alternatives

- Standardize how to embed non-simple headers in a URL
- Foreign fetch ServiceWorker

## References

- Well-Known Resources for HTTP OPTIONS: https://mnot.github.io/I-D/http-options-resources/

## Acknowledgement

Thanks to Ben Kelly, bifurcation, Jonas Sicking, roryhewitt, Mark Nottingham, Wenbo Zhu.
