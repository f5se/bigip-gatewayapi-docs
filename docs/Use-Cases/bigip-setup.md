## BIG-IP Setup

After the bigip-kubernetes-gateway controller is deployed, the first step is to create a gatewayclass resource.

A gatewayclass resource corresponds to a BIG-IP. According to the definition of gatewayclass by [SIGNetworks](https://gateway-api.sigs.k8s.io/){:target="_blank"}, we know that gatewayclass marks the implementation carrier of gateway-related resources, and in the bigip-kubernetes-gateway senarios, the carrier corresponding to the gatewayclass is BIG-IP.

gatewayclass.yaml: 
```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: GatewayClass
metadata:
  name: bigip
spec:
  controllerName: f5.io/gateway-controller-name
  parametersRef:
    group: core
    kind: ConfigMap
    name: my-configmap
    namespace: kube-system
```

The configmap `my-configmap` in the `parametersRef` section is the BIG-IP configuration file.

BIG-IP can be configured by executing `kubectl apply -f gatewayclass.yaml` when the bigip-kubernetes-gateway controller receives this event from the user. The controller will parse the relevant configuration in the `spec` and configure the BIG-IP device, including the network, basic partition, and so on.