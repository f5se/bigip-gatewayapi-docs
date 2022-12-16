# Installation

In the code [repository](https://github.com/f5devcentral/bigip-kubernetes-gateway/tree/master/deploy){:target="_blank"}, we provide the Gateway API deployment yaml files for installtion.

The deployment yaml files are located at: https://github.com/f5devcentral/bigip-kubernetes-gateway/tree/master/deploy.

The files are numbered and can complete the deployment process in order, where:

|file name | functionality | notes |
| --- | --- | -- |
| [1.clusterrole-and-binding.yaml](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/deploy/1.clusterrole-and-binding.yaml){:target="_blank"} | Create a user and role with corresponding operation permissions in the k8S cluster | No further input is required |
| [2.install-bigip-kubernetes-gatewayapi-CRDs.yaml](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/deploy/2.install-bigip-kubernetes-gatewayapi-CRDs.yaml){:target="_blank"} | Install the gateway API CRD and admission deployments | No further input is required |
| [3.deploy-bigip-kubernetes-gateway-controller.yaml](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml){:target="_blank"} | Deploy bigip-kubernetes-gateway controller | Required inputs by user to change BIGIP `password: `, BIGIP Configurations in `bigips: ` and image version in `image: ` |

Execute the `kubectl` command separately in order:

```shell
$ kubectl apply -f 1.clusterrole-and-binding.yaml
$ kubectl apply -f 2.install-kubernetes-gatewayapi-CRDs.yaml
$ kubectl apply -f 3.deploy-bigip-kubernetes-gateway-controller.yaml
```


After doing them, the bigip-kubernetes-gateway controller runs as a pod in `kube-system` namespace of the kubernetes cluster watching CRUD events for gateway-related resources.

* View deployment results via `kubectl get deployment -n kube-system`.
* View the run log via `kubectl logs -f deployment/bigip-kubernetes-gateway -c bigip-kubernetes-gateway-pod -n kube-system`

For configuration defails in mentioned yaml files, see [Controller Parameters](./parameters.md).