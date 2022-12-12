# Releases 

The bigip-kubernetes-gateway versions are released on dockerhub as [Docker images](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags){:target="_blank"}. 

The code repository is [f5devcentral/bigip-kubernetes-gateway](https://github.com/f5devcentral/bigip-kubernetes-gateway){:target="_blank"}.


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