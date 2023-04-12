# Releases 

The bigip-kubernetes-gateway versions are released on dockerhub as [Docker images](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags){:target="_blank"}. 

The code repository is [f5devcentral/bigip-kubernetes-gateway](https://github.com/f5devcentral/bigip-kubernetes-gateway){:target="_blank"}.

## Release v0.2.1

### Docker Image:

[f5devcentral/bigip-kubernetes-gateway:v0.2.1-20230411](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags)

### Reference:

https://gateway-api.f5se.io/guides/getstarted

### Release Notes:

#### DevOps and Monitoring Support

* Add support for log levels instead of all "debug" by @zongzw in #26
* Add runtime dumps abiliity. by @zongzw in #32
* Enhance devops with adding dumping and trailing support. by @zongzw in #33
* Separate cni setup logic to standalone tool: f5-tool-setup-cni by @zongzw in #34
* Change the mod from gitee to github by @zhang-shengping in #38
* Add system test framework by @zongzw in #40
* Refine the codes to standard project layout. by @zongzw in #44
* Basic ginkgo test for tls gateway by @zhang-shengping in #45, #48, #49

#### Feature Support

* Add cross namespace routing support. by @zongzw in #23, #24
* Implement responseHeaderModifier by @zongzw in #28
* Add ReferenceGrant support. by @zongzw in #30
* Use template for irule generation. by @zhang-shengping in #32
* Add https function for gateway. by @zhang-shengping in #35
* Use referencegrant for secret cross-reference restriction. by @zongzw in #36
* Add webhook framework and automation yamls. by @zongzw @zhang-shengping in #37, #41, #42
* Bugfix: avoid deploying resources of other controllerName. by @zongzw in #43

#### Documentation Support

* Update compatibility docs; add more spec support. by @zongzw in #21
* Add gateway controller examples by @zongzw in #46


## Release v0.2.0

### Docker Image:

[f5devcentral/bigip-kubernetes-gateway:v0.2.0-20230216](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags)

### Reference:

https://gateway-api.f5se.io/guides/getstarted

### Release Notes:

#### DevOps and Monitoring Support

* Refine logging with given log level and tracing request id.

#### Feature Support

* Fix startup's bigip password issue ([PR](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/09ea7c214b738a1beca30eb43be32a2411e17662){:target="_blank"})

* Fix sig.k8s.io/controller-runtime dependency issue([PR](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/25b1fef46426cd1147bbddcbbbbb46a9d22c8a53){:target="_blank"}).

* Be compatible with sig gateway api v0.6.0 specification.

  * Add support for HTTPRoute.Filter.ResponseHeaderModifier.

  * Add support for ReferenceGrant for service binding restriction.

#### Documentation Support

* Update compatibility document for ReferenceGrant and responseHeaderModifier support.

## Release v0.1.1

### Docker Image:

[f5devcentral/bigip-kubernetes-gateway:v0.1.1-20230111](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags)

### Reference:

https://gateway-api.f5se.io/deploy/installation/

### Release Notes:

#### DevOps and Monitoring Support
* Refine conroller's [logging](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/95fd84b9d2216a88da93469e7d8c19a0c0cce01f){:target="_blank"}

#### Feature Support
* Add Support for Gateway.Listener.AllowedRouted Spec. See more details from the [PR](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/b4bbffb23b92ca709cbc5de9933e93ef7bc52ecb){:target="_blank"} and the [Usecase](../guides/multiple-ns.md)
* Add AS [configuration](../deploy/parameters.md#part-2-bigip-kubernetes-gateway-configmap) for calico mode. 

#### Documentation Support
* Update compatibility [docs](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/v0.1.1/docs/gateway-api-compatibility.md)

## Release v0.1.0

### Docker Image:

[f5devcentral/bigip-kubernetes-gateway:v0.1.0-20221226](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags)

### Reference:

https://gateway-api.f5se.io/deploy/installation/

### Release Notes:

#### DevOps and Monitoring Support
* Supports full DevOps capabilities, providing all [YAML files](https://github.com/f5devcentral/bigip-kubernetes-gateway/tree/v0.1.0/deploy){:target="_blank"} for installation and deployment.
* Uses github action for the [integration with Slack](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/v0.1.0/.github/workflows/release-slack-notice.yml){:target="_blank"}.
#### Feature Support
* Supports using configmap as the [controller's parameter](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/v0.1.0/deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml#L15){:target="_blank"} format
* Supports the ability to deliver and configure resources to BIG-IP, refer to [gateway api compatibility](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/v0.1.0/docs/gateway-api-compatibility.md){:target="_blank"}, also, refer to [all usecases](https://gateway-api.f5se.io/guides/simple-gateway/){:target="_blank"}.
* Supports [integration with Prometheus](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/v0.1.0/deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml#L103){:target="_blank"} to capture controller performance data.
* Supports service types: NodePort ClusterIP.
* Supports for CNI flannel and calico networks.
* Supports log tracing with request id.
* Supports [automated configuration](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/v0.1.0/deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml#L20){:target="_blank"} of flannel and calico CNI modes on the BIG-IP side.
#### Documentation Support
* Documentation on https://gateway-api.f5se.io/.

## Release v0.0.4

### Docker Image:

[f5devcentral/bigip-kubernetes-gateway:v0.0.4-20221219](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags)

### Reference:

https://gateway-api.f5se.io/deploy/installation/

### Release Notes:

* Improve the automated configuration of the BIG-IP side in flannel and calico modes, see [here](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/95cafb5decceda6963926e49beb6bf4aa164029f){:target="_blank"}
* Determine the [parameters ](../deploy/parameters.md) passed in by the user, see [here](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/fe12db6fdabd1e62d5c10103fc0d80e7abb204c9){:target="_blank"}
* Fixed the partition deletion issue when the gatewayclass was deleted, see [here](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/0fee4c1ea6f9c9bead45a1e993ff51bf994b83b7){:target="_blank"}

**Full Changelog**: https://github.com/f5devcentral/bigip-kubernetes-gateway/compare/v0.0.3...v0.0.4

## Release v0.0.3

### Docker Image: 

[f5devcentral/bigip-kubernetes-gateway:v0.0.3-20221213](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags)

### Reference: 

https://gateway-api.f5se.io/deploy/installation/

### Release Notes:

* Documentation on https://gateway-api.f5se.io/, including:
  * Update usage of [deploy/install controller](../deploy/installation.md)
  * Add gateway api compatibility [reference](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/docs/gateway-api-compatibility.md){:target="_blank"}
  * Add [LICENSE](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/af0b432c9c634adeb116165bf4e3aedc1555c370){:target="_blank"} declaration
* Use github action for the [integration](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/.github/workflows/release-slack-notice.yml){:target="_blank"} with Slack.
* Redesign gatewayclass and controller [relationship](https://gateway-api.f5se.io/Design/gatewayclassrefer/){:target="_blank"} that moves BIG-IP configuration to controller level.
* Add controller parameter form of [configmap](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/cbff23770abed5dec7a6c63eaa98a01f19fce64f){:target="_blank"}.
* Add BIG-IP configuration ability for flannel and calico CNIs([here](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/819d938551ec92928596519fe96f5f7025c9042f){:target="_blank"}).
* Enable [log tracing](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/226a13f3bf584cab356a4d240471c426c04007ea){:target="_blank"} with request id.
* Implement httproute traffic splitting feature ([here](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/1f813e92ee43c0195f3c62151c42bad15e15509f){:target="_blank"}).

**Full Changelog**: https://github.com/f5devcentral/bigip-kubernetes-gateway/compare/v0.0.2...v0.0.3

## Release v0.0.2

### Docker Image: 

[f5devcentral/bigip-kubernetes-gateway:v0.0.2-20221129](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags){:target="_blank"}

### Reference: 

https://gateway-api.f5se.io/deploy/installation/

### Release Notes:

* Add support for gatewayclass resources, see [gatewayclass design](../Design/#framework-and-workflow-design).
* Implement the ability of handling disorder events for kinds of resources.
* Improve the design and usage documentation: https://gateway-api.f5se.io/
* Optimize the resource synchronization mode at startup time for deployment integrity.
* Update the [f5-bigip-rest](https://gitee.com/zongzw/f5-bigip-rest){:target="_blank"} dependency for better deployments.

**Full Changelog**: https://github.com/f5devcentral/bigip-kubernetes-gateway/compare/v0.0.1...v0.0.2  

## Release v0.0.1

### Docker Image: 

[f5devcentral/bigip-kubernetes-gateway:v0.0.1-20221115](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags){:target="_blank"}

### Deployment Reference: 

https://gateway-api.f5se.io/deploy/installation/

### Release Notes:

#### DevOps and Monitoring Support

* Supports full DevOps capabilities, providing all YAML files for installation and deployment.
* Supports integration with Prometheus to capture controller performance data
* Basic automated testing capabilities.

#### Feature Support

* Supports the ability to deliver and configure resources: gateway httproute and service deployment, to BIG-IP.
* Supports resource specs:
  * gatewayclass: not support
  * gateway：
    * listeners：name port protocol
    * addresses：value
  * httproute
    * parentRefs
    * hostnames
    * rules
      * matches: method header path queryParams
      * filters: RequestHeaderModifier RequestRedirect ExtensionRef
    * backendRefs: partial, only one backend supported
* Supports service types: NodePort ClusterIP.
* Supports for CNI flannel and calico networks.