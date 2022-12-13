# Releases 

The bigip-kubernetes-gateway versions are released on dockerhub as [Docker images](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags){:target="_blank"}. 

The code repository is [f5devcentral/bigip-kubernetes-gateway](https://github.com/f5devcentral/bigip-kubernetes-gateway){:target="_blank"}.


## Release v0.0.3

### Docker Image: 

[f5devcentral/bigip-kubernetes-gateway:v0.0.3-20221213](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags)

### Reference: 

https://gateway-api.f5se.io/quick-start/installation/{:target="_blank"}

### Release Notes:

* Documentation on https://gateway-api.f5se.io/, including:
   * Update usage of [deploy/install controller](../quick-start/installation.md)
   * Add [gateway api compatibility reference](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/docs/gateway-api-compatibility.md){:target="_blank"}
   * Add [LICENSE](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/af0b432c9c634adeb116165bf4e3aedc1555c370){:target="_blank"} declaration
* Use github action for the [integration](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/.github/workflows/release-slack-notice.yml){:target="_blank"} with Slack.
* Redesign gatewayclass and controller [relationship](https://gateway-api.f5se.io/Architecture/gatewayclassrefer/){:target="_blank"} that moves BIG-IP configuration to controller level.
* Add controller parameter form of [configmap](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/cbff23770abed5dec7a6c63eaa98a01f19fce64f){:target="_blank"}.
* Add BIG-IP configuration ability for flannel and calico CNIs([here](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/819d938551ec92928596519fe96f5f7025c9042f){:target="_blank"}).
* Enable [log tracing](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/226a13f3bf584cab356a4d240471c426c04007ea){:target="_blank"} with request id.
* Implement httproute traffic splitting feature ([here](https://github.com/f5devcentral/bigip-kubernetes-gateway/commit/1f813e92ee43c0195f3c62151c42bad15e15509f){:target="_blank"}).

**Full Changelog**: https://github.com/f5devcentral/bigip-kubernetes-gateway/compare/v0.0.2...v0.0.3{:target="_blank"}

## Release v0.0.2

### Docker Image: 

[f5devcentral/bigip-kubernetes-gateway:v0.0.2-20221129](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags){:target="_blank"}

### Reference: 

https://gateway-api.f5se.io/quick-start/installation/

### Release Notes:

* Add support for gatewayclass resources, see [gatewayclass design](../Architecture/#framework-and-workflow-design).
* Implement the ability of handling disorder events for kinds of resources.
* Improve the design and usage documentation: https://gateway-api.f5se.io/
* Optimize the resource synchronization mode at startup time for deployment integrity.
* Update the [f5-bigip-rest](https://gitee.com/zongzw/f5-bigip-rest){:target="_blank"} dependency for better deployments.

**Full Changelog**: https://github.com/f5devcentral/bigip-kubernetes-gateway/compare/v0.0.1...v0.0.2{:target="_blank"}

## Release v0.0.1

### Docker Image: 

[f5devcentral/bigip-kubernetes-gateway:v0.0.1-20221115](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags){:target="_blank"}

### Deployment Reference: 

https://gateway-api.f5se.io/quick-start/installation/

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