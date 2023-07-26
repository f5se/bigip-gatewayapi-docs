# Overview [WIP]

## What is BIG-IP Next

At its core, it’s still the same BIG-IP that F5 customers know and trust, simply designed and
rearchitected for the future. BIG-IP Next is the next generation BIG-IP software built to offer
greater automation opportunities, scalability, and ease-of-use for organizations running
applications on-premises, in the cloud, or out at the edge. Powerful declarative APIs are the
foundation for BIG-IP Next’s API-first design, making it faster and easier for DevOps, NetOps,
and other BIG-IP-reliant teams to manage and automate their BIG-IP deployments.

A completely rearchitected software layer built on a modern framework provides the basis for
significantly improved control plane scale and performance, reduced cloud footprint for lower
operational costs, and rapid instance upgrades. Carrying forward the comprehensive suite
of advanced BIG-IP functionality developed over the past 20 years, BIG-IP Next continues
to deliver everything from application security and access controls to local and global traffic
management—and availability across the same breadth of deployment and consumption
models as its predecessor.

## What is BIG-IP Next Kubernetes GatewayAPI Controller

BIG-IP Next Kubernetes GatewayAPI Controller ("Controller next" in short) is one of GatewayAPI downstream [implementations](https://gateway-api.sigs.k8s.io/implementations/){:target="_blank"}.

The "Controller Next" is responsible for resolving 2 problems,

* Automatical application deliveries on downstream BIG-IP Next instance(s).
* Network connectivity setup between Kubernetes CNI and BIG-IP Next(s) traffic network.

## Resource Monitoring

Like all other CRD implementations, the Controller monitors Kubernetes GatewayAPI CRD resources (gateway.networking.k8s.io/v1beta1) and converts them to BIG-IP Next-related configurations for deploying to BIG-IP Next devices.

The Controller connects to the upstream Kubernetes, monitors Gateway API updating events from the cluster through the list-watch mechanism. The mechanism was encapsulated in `client-go`(k8s.io/client-go) and `controller-runtime`(sigs.k8s.io/controller-runtime).

In order to access various necessary resources of Kubernetes, we need to use specific `ServiceAccount` and `ClusterRole` that have specific permissions. Please refer to [installation guide](../guides/getstarted.md) for permission details.

## Resource Mapping

TODO



We are actively work on the integration of BIG-IP Next, will update. If you want to learn what is BIG-IP Next, kindly please click [here](https://www.f5.com/pdf/products/an-introduction-to-big-ip-next-f5s-next-generation-big-ip-software.pdf).
