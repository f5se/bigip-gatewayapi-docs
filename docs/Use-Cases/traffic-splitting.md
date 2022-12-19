## HTTP traffic splitting

In this usecase, we will understand the slightly more complex application method of `HTTPRoute`. 

You can implement the grayscale publishing of the application through `HTTPRoute`, and smoothly transition traffic to new services.

See [here](./simple-gateway.md) for `GatwayClass` and `Gateway` definitions.

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
            value: /test1
      backendRefs:
        - name: coffee
          port: 80
          weight: 1
        - name: tea
          port: 80
          weight: 9
    - matches:
        - path:
            type: PathPrefix
            value: /test2
      backendRefs:
        - name: coffee
          port: 80
          weight: 9
        - name: tea
          port: 80
          weight: 1
```


*Refer [here](./service-definition.md) for services `tea` and `coffee` definition.*

In the above `httproute` definition, we see that there are two rules:

* when we access **/test1**, 90% of the traffic is forwarded to the **tea** service
* when we access **/test2**, 90% of the traffic is forwarded to the **coffee** service

You may change the two ratios, and run `kubectl apply -f httproute.yaml` again to achieve a change in the traffic ratio.