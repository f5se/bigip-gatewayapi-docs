## Deploying a simple Gateway

Just like the official website describes [here](https://gateway-api.sigs.k8s.io/guides/simple-gateway/){:target="_blank"}, a `Gateway` can be deployed as an ingress. In this page, we provide another version of a simple gateway deployment.

In this usecase, we will have a preliminary understanding of the definition methods of `HTTPRoute` and related resources, and understand how `HTTPRoute` achieves the routing and forwarding capability of requests.

To demo this simple HTTPRoute usecase, we will create an `HTTPRoute` resource that defines traffic forwarding rules, and we need to create the `GatewayClass` and `Gateway` resource on which it depends. To demonstrate the effect, we also need to create a `Service` resource.

When we access the ingress IP defined in the `Gateway`, the traffic is forwarded to the backend service by the rule defined in `HTTPRoute`.

gatewayclass.yaml
```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: GatewayClass
metadata:
  name: bigip
spec:
  controllerName: f5.io/gateway-controller-name

```

gateway.yaml
```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: mygateway
  labels:
    domain: k8s-gateway
spec:
  gatewayClassName: bigip
  listeners:
  - name: http
    port: 80
    protocol: HTTP
  addresses:
    - value: 10.250.17.120

```

httproute.yaml
```yaml

---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: myhttproute
spec:
  parentRefs:
    - name: mygateway
      sectionName: http
  hostnames:
    - gateway.test.automation
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /test
          headers:
            - name: svc
              value: coffee
      filters:
        - type: RequestHeaderModifier
          requestHeaderModifier:
            add:
              - name: tester
                value: f5
      backendRefs:
        - name: coffee
          port: 80
```

*Refer [here](./service-definition.md) for `coffee` definition.*

In the above demo, the rules in `httproute.yaml` contains two parts: `matches` and `filters`, `matches` defines route matching rules, and `filters` defines the customization process for requests.

* matches: /test path also contains svc==coffee in the header
* filters: Add a new header tester = f5

therefore,

```shell

$ curl 10.250.17.120/test -H "Host: gateway.test.automation" -H "svc: coffee"

{
    "queries": {},
    "headers": {
        "Host": "gateway.test.automation",
        "User-Agent": "curl/7.79.1","Accept":"*/*", 
        "svc": "coffee",
        "tester": "f5"
    },
    "version": "1.1",
    "method": "GET",
    "remote-address": "10.42.7.0",
    "uri": "/test",
    "server_name": "COFFEE"
}

```