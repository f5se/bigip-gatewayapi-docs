# GatewayClass
GatewayClass defines a set of Gateways that share a common configuration and behavior. Each GatewayClass will be handled by a single controller, although controllers may handle more than one GatewayClass.

GatewayClass is a cluster-scoped resource. There must be at least one GatewayClass defined in order to be able to have functional Gateways. A controller that implements the Gateway API does so by providing an associated GatewayClass resource that the user can reference from their Gateway(s).

This is similar to IngressClass for Ingress and StorageClass for PersistentVolumes. In Ingress v1beta1, the closest analog to GatewayClass is the ingress-class annotation, and in IngressV1, the closest analog is the IngressClass object.
