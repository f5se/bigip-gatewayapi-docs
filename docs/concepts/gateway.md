# Gateway
A Gateway describes how traffic can be translated to Services within the cluster. That is, it defines a request for a way to translate traffic from somewhere that does not know about Kubernetes to somewhere that does. For example, traffic sent to a Kubernetes Service by a cloud load balancer, an in-cluster proxy, or an external hardware load balancer. While many use cases have client traffic originating “outside” the cluster, this is not a requirement.

It defines a request for a specific load balancer config that implements the GatewayClass’ configuration and behaviour contract. The resource may be created by an operator directly, or may be created by a controller handling a GatewayClass.

As the Gateway spec captures user intent, it may not contain a complete specification for all attributes in the spec. For example, the user may omit fields such as addresses, TLS settings. This allows the controller managing the GatewayClass to provide these settings for the user, resulting in a more portable spec. This behaviour will be made clear using the GatewayClass Status object.

A Gateway may be attached to one or more Route references which serve to direct traffic for a subset of traffic to a specific service.

