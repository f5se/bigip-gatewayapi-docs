# GatewayClass and BIG-IP References

## Relationships Between GatewayClass, Controller and BIG-IP

There is an n-1-1 relationship between `GatewayClass`, Controller and BIG-IP.

Controller and BIG-IP are one-to-one relationships, and BIG-IP here can be understood as a set of BIG-IP, such as two BIG-IPs are HA relationships with each other.

In the same K8S cluster, a controller controls a group of BIG-IPs and is responsible for different gatewayClasses.

The relationship between GatewayClass and Controller is designed as a many-to-one relationship, that is, multiple gatewayclasses can correspond to the same BIG-IP, the `name` field of the GatewayClass resource is mapped as the `partition` name on the BIG-IP, and the gateway-related resources associated with the GatewayClass will be created as ltm resources under that partition.

The mapping between GatewayClass and Controller is achieved through the following properties:

`controllerName` in `GatewayClass` <=> `--controller-name`

The `controllerName` field corresponds to the `--controllerName` parameter at startup, if this field cannot match the --controllerName parameter, this gatewayclass resource will not be processed by the controller.

gatewayclass.yaml: 
```yaml
---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: GatewayClass
metadata:
  name: bigip
spec:
  controllerName: f5.io/gateway-controller-name
  # parametersRef in the gatewayclass resource configuration will be ignored.
  # parametersRef: 
    # group: 
    # kind: 
    # name:
    # namespace: 
```

### When the GatewayClass Resource Changes

When the user changes the name of the GatewayClass, it means that the user deletes the original GatewayClass and creates a new GatewayClass.

Controller will erase the partition corresponding to the GatewayClass name and all resources under it on the BIG-IP. Then the controller creates a new partiton of the GatewayClass name and rebuild the partition's resources.

Therefore, for users, changing the GatewayClass name needs to be cautious.

