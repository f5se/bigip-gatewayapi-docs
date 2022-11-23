# GatewayClass and BIG-IP References

### Relationships Between GatewayClass, Controller and BIG-IP

There is an n-1-1 relationship between GatewayClass, Controller and BIG-IP.

Controller and BIG-IP are one-to-one relationships, and BIG-IP here can be understood as a set of BIG-IP, such as two BIG-IPs are HA relationships with each other.

In the same K8S cluster, a controller controls a group of BIG-IPs and is responsible for different gatewayClasses. (The current implementation considers the case of Standalone BIG-IP.)

The relationship between GatewayClass and Controller(also as BIG-IP) is designed as a many-to-one relationship, that is, multiple gatewayclasses can correspond to the same BIG-IP, the `name` field of the GatewayClass resource is mapped as the partition name on the BIG-IP, and the gateway-related resources associated with the GatewayClass will be created as ltm resources under that partition.

The mapping between GatewayClass and Controller is achieved through the following properties:

The `controllerName` field of spec in GatewayClass <=> `--controller-name` in the controller startup parameter

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

### When the GatewayClass Resource Changes

GatewayClass specifies the controller name used to implement the related gateway resource, and also associates a Configmap containing BIG-IP configuration information. The handling strategy when the user makes changes to the GatewayClass resource is:

* The GatewayClass name has been changed

  When the user changes the name of the GatewayClass, it means that the user deletes the original GatewayClass and creates a new GatewayClass, and the Controller will erase the partition corresponding to the GatewayClass name and all the information under it on the BIG-IP. Then the controller creates a new partiton of the GatewayClass name and rebuild the partition's Gateway resources.

  Therefore, for users, changing the GatewayClass name needs to be cautious.

* Changes to the Configmap Reference

  Changing the Configmap reference is prohibited by the Controller, that is, new Configmap is not allowed to be used to make changes to the BIG-IP configuration, otherwise it will cause unpredictable problems. In this case, the Controller identifies the GatewayClass status as unavailable until the user changes back to the original Configmap reference.

  At the implementation level, the controller will configure BIG-IP when it receives the corresponding Configmap for the first time, and save the Configmap reference information to BIG-IP DataGroup, and subsequent Configmaps with different names will be rejected.

* Configmap content changes

  In the current implementation, changes to the contents of the Configmap are not allowed for the time being.

  In the future, users can make changes to Configmap, the controller allows users to change the BIG-IP configuration by updating Configmap. 

  The details have not yet been considered, and this requirement needs to be carefully considered to avoid the implementation complexity of BIG-IP configuration changes and the impact on the current business.
