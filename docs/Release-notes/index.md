# Releases 

The bigip-kubernetes-gateway versions are released on dockerhub as [Docker images](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags). 

The code repository is [f5devcentral/bigip-kubernetes-gateway](https://github.com/f5devcentral/bigip-kubernetes-gateway).

## Release v0.0.1

### Docker Image: 

[f5devcentral/bigip-kubernetes-gateway:v0.0.1-20221115](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags)

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