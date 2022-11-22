# Installation

In the repository, we provide the corresponding Gateway API deployment configuration file, which can be used in the deployment process of the BIGIP-Kubernetes-Gateway controller in general.

The directory where the deployment files are located is: 
https://github.com/F5Networks/bigip-kubernetes-gateway/tree/master/deploy

The files are numbered and can complete the deployment process in order, where:

|file name | functionality | notes |
| --- | --- | -- |
| 0.bigip-config.yaml | BIG-IP connecting info | Required inputs by user |
| 1.clusterrole-and-binding.yaml | Create a user and role with corresponding operation permissions in the k8S cluster | No further input is required |
| 2.install-bigip-k8s-gtw-crds.yaml | Install the gateway API CRD and admission deployment | No further input is required |
| 3.deploy-bigip-k8s-gtw-ctlr.yaml | Deploy bigip-kubernetes-gateway controller | Required inputs by user to change `image: ` |

Each of the above files can be completed by executing the `kubectl` command separately in order:

```shell
$ kubectl apply -f 0.bigip-config.yaml
$ kubectl apply -f 1.clusterrole-and-binding.yaml
$ kubectl apply -f 2.install-bigip-k8s-gtw-crds.yaml
$ kubectl apply -f 3.deploy-bigip-k8s-gtw-ctlr.yaml
```


After the deployment is complete, the bigip-kubernetes-gateway controller listens for CRUD events for gateway-related resources.

* View deployment results via `kubectl get deployment -n kube-system`.
* View the run log via `kubectl logs -f deployment/bigip-kubernetes-gateway -c bigip-kubernetes-gateway-pod -n kube-system`