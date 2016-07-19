# Origin Wide CORS

Extra round trip caused by CORS preflight has been adding significant delay to XHR/Fetch with non-simple headers/method. This is preventing performance sensitive applications from adopting web platform.

The Origin Wide CORS mechanism improves this by allowing an origin server to announce CORS capability and parameters widely across resources.

## Design points

Starting with just summarizing ideas proposed at https://github.com/whatwg/fetch/issues/210

- the address should be under control of the origin's administrator
- hierarchical design or single point
- extend the existing CORS preflight or ...
  - Origin-Wide-CORS-Supported: yes
    - Merge to Origin-Wide-CORS?
  - Origin-Wide-CORS: Origin= ; Headers= ;
- patterns
- expiration
  - Max-Age
- scope, realm
  - https://github.com/whatwg/fetch/issues/210#issuecomment-179506649
- include credentialed or non-credentialed only
- API side switch or internally? https://github.com/whatwg/fetch/issues/210#issuecomment-178476230
- should allow proxies to cache the CORS information https://github.com/whatwg/fetch/issues/210#issuecomment-178269098

## Plan

- Start with just allowing for announcing that the origin understands CORS and therefore it's safe to omit preflight
- Extend it to enable announcing capability in more fine-grained form and detailed parameters

## References

- Well-Known Resources for HTTP OPTIONS: https://mnot.github.io/I-D/http-options-resources/

## Acknowledgement

Thanks to Ben Kelly, bifurcation, Jonas Sicking, roryhewitt, Mark Nottingham, Wenbo Zhu.
