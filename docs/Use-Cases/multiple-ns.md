## Cross-Namespace routing

In this scenario, we demonstrate the process of attaching httproute to gateway which might be in a different namespace. This allows user access control to be applied differently across Namespaces for Routes and Gateways, effectively segmenting access and control to different parts of the cluster-wide routing configuration.

### Gateway allows Routes from all namespaces

```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: gateway201
  namespace: namespace1
  labels:
    domain: k8s-gateway.nginx.org
spec:
  gatewayClassName: bigip36
  listeners:
  - name: http
    port: 80
    protocol: HTTP
    allowedRoutes:
      namespaces:
        from: All
  addresses:
    - value: 10.250.15.2

---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: test-ns-all
  namespace: default
spec:
  parentRefs:
    - name: gateway201
      sectionName: http
      namespace: namespace1
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

When the value of allowedRoutes.namespaces.from is `All`, it means this gateway does not care what namespace the routes are from.
So although the Gateway and HTTPRoute are from different namespaces above, it does not matter.

### Gateway allows Routes from the same namespace

```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: gateway201
  namespace: default
  labels:
    domain: k8s-gateway.nginx.org
spec:
  gatewayClassName: bigip36
  listeners:
  - name: http
    port: 80
    protocol: HTTP
    allowedRoutes:
      namespaces:
        from: Same
  addresses:
    - value: 10.25.15.3

---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: test-ns-same
  namespace: default
spec:
  parentRefs:
    - name: gateway201
      sectionName: http
      namespace: default
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

When the value of allowedRoutes.namespaces.from is `Same`, it means this gateway only allowes routes from the same namespace to attach
to it. In the example above, both the gateway and HTTPRoute are from `default` namespace.

### Gateway allows Routes from namespaces filtered by namespace selector

```yaml
---

apiVersion: v1
kind: Namespace
metadata:
  name: newlable-ns
  labels:
    newlable: "newlable"

---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: gateway201
  namespace: namespace1
  labels:
    domain: k8s-gateway.nginx.org
spec:
  gatewayClassName: bigip36
  listeners:
  - name: http
    port: 80
    protocol: HTTP
    allowedRoutes:
      namespaces:
        from: Selector
        selector:
          matchLabels:
            newlable: "newlable"
  addresses:
    - value: 10.25.15.4

---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: test-ns-selector
  namespace: newlable-ns
spec:
  parentRefs:
    - name: gateway201
      sectionName: http
      namespace: namespace1
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

When the value of allowedRoutes.namespaces.from is `Selector`, it means this gateway allows routes from those namespaces that satisfy
the namespace selector. In this example, routes whose namespace has label `newlable=newlable` are allowed to attach to the gateway.

Besides `AllowedRoutes.namespaces.From`, `AllowedRoutes.namespaces.Kinds` determines which kinds of routes can be attached to `Gateway`. For some advanced configuration of `AllowedRoutes`, refer to [here](./allowedroutes.md) for more details.
