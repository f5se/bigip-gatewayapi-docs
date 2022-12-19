## HTTP Routing

In this scenario, based on [Simple Gateway](./simple-gateway.md), we demonstrate the process of binding multiple httproutes in one gateway to forward traffic with different characteristics, to different backend servers.

In this page, we ignore the configuration of `GatewayClass` and `Gateway`, and directly show the configurations of different `HTTPRoute` resources.

### Request header based matching

```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: test-match-header
spec:
  parentRefs:
    - name: gateway
      sectionName: http
  hostnames:
    - gateway.test.automation
  rules:
    - matches:
        - headers:
            - name: test
              value: automation
      backendRefs:
        - name: test-service
          port: 80
```

When the domain name of the request is `gateway.test.automation` and the request header contains `test == automation`, the traffic is forwarded to the `test-service` service with port 80.

### Request method based matching

```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: test-match-method
spec:
  parentRefs:
    - name: gateway
      sectionName: http
  hostnames:
    - gateway.test.automation
  rules:
    - matches:
        - method: GET
        - method: OPTIONS
      backendRefs:
        - name: test-service
          port: 80
```

When the domain name is `gateway.test.automation` and the request method is `GET` or `OPTIONS`, the request is forwarded to the `test-service` service with port 80.

### Request path based matching

```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: test-match-path
spec:
  parentRefs:
    - name: gateway
      sectionName: http
  hostnames:
    - gateway.test.automation
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /path-test
      backendRefs:
        - name: test-service
          port: 80
```

When the request domain name is `gateway.test.automation` and the request path is `/path-test`, the request is forwarded to the `test-service` service with port 80.

### Request parameter based matching

```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: test-query-params
spec:
  parentRefs:
    - name: gateway
      sectionName: http
  hostnames:
    - gateway.test.automation
  rules:
    - matches:
      - queryParams:
        - name: test
          value: automation
      backendRefs:
      - name: test-service
        port: 80
```

When the request domain name is `gateway.test.automation`, and the request parameters contain `?test=automation`, the request is forwarded to the `test-service` service with port 80.

### Multiple matches in a HTTPRoute

```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: test-multiple-rules
spec:
  parentRefs:
    - name: gateway
      sectionName: http
  hostnames:
    - gateway.test.automation
  rules:
    - matches:
        - method: GET
        - method: OPTIONS
      backendRefs:
        - name: test-service1
          port: 80
    - matches:
      - queryParams:
        - name: test
          value: automation
      - path:
          type: PathPrefix
          value: /path-test
      backendRefs:
        - name: test-service2
          port: 80
    - backendRefs:
        - name: test-service3
          port: 80
```

When the request domain name is `gateway.test.automation`, and
* If the request method is `GET` or `OPTIONS`, the request is forwarded to the `test-service1` service with port 80.
* If request parameters contain `?test=automation`, **or** the path of the request is `/path-test`, and the request is forwarded to the `test-service2` service, port 80.
* In other cases, it is forwarded to the `test-service3` service, port 80.