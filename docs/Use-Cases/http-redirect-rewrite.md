## HTTP path redirects and rewrites

`HTTPRoute` can be used to redirect clients' request or rewrite the paths sent to the backends using `filters`.

In this page, we will show how to use the `filters` to do the redirect or rewrite.

We ignore the configuration of `GatewayClass` and `Gateway`, and directly show the configurations of different `HTTPRoute` resources.

### Redirects


```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: test-filter-requestredirect
spec:
  parentRefs:
  - name: gateway
    sectionName: http
  hostnames:
  - gateway.test.automation
  rules:
  - filters:
      - type: RequestRedirect
        requestRedirect:
          scheme: https
          hostname: www.example.com
          # path:     -> experimental in v0.5.1
          #   type: ReplaceFullPath
          #   replaceFullPath: /fake
          port: 443
          statusCode: 301
    backendRefs:
    - name: tea
      port: 80
```

In this `HTTPRoute` configuration, when we access `http://gateway.test.automation`, the traffic will be redirect to `https://www.example.com/`:

```shell
$ curl 10.250.18.119 -H "Host: gateway.test.automation" -v
*   Trying 10.250.18.119:80...
* Connected to 10.250.18.119 (10.250.18.119) port 80 (#0)
> GET / HTTP/1.1
> Host: gateway.test.automation
> User-Agent: curl/7.79.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
* HTTP 1.0, assume close after body
< HTTP/1.0 301 Moved Permanently
< Location: https://www.example.com:443/
< Server: BigIP
* HTTP/1.0 connection set to keep alive!
< Connection: Keep-Alive
< Content-Length: 0
<
* Connection #0 to host 10.250.18.119 left intact
```

As shown here, the virtual responses `< HTTP/1.0 301 Moved Permanently`.

### Rewrites

*Experimental in gateway api v0.5.1, will be supported in future.*

```yaml
# ---

# apiVersion: gateway.networking.k8s.io/v1beta1
# kind: HTTPRoute
# metadata:
#   name: test-filter-urlrewrite
# spec:
#   parentRefs:
#   - name: gateway
#     sectionName: http
#   hostnames:
#   - "cafe.example.com"
#   rules:
#   - filters:
#       - type: URLRewrite
#         urlRewrite:
#           hostname: www.example.com
#           path: 
#             type: ReplaceFullPath
#             replaceFullPath: /fake
#     backendRefs:
#     - name: tea
#       port: 80
```