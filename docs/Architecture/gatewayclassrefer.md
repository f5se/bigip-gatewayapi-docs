# GatewayClass and BIG-IP References

The relationship between GatewayClass and BIG-IP is designed as a many-to-one relationship, that is, multiple gatewayclasses can correspond to the same BIG-IP, the `name` field of the GatewayClass resource is mapped as the partition name on the BIG-IP, and the gateway-related resources associated with the GatewayClass will be created as ltm resources under that partition.

![image](./gatewayclass-bigip.png)

When multiple GatewayClass resources correspond to the same BIG-IP, they must use the same BIG-IP configuration information, that is, the same ConfigMap, as shown below.

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
    # -> Here, the Configmap referenced by multiple GatewayClasses must be unique
    group: core
    kind: ConfigMap
    name: my-configmap
    namespace: kube-system
```

This configmap will be saved by the controller to the BIG-IP DataGroup, and an error will be reported when another configmap is attempted.