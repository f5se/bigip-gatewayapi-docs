# Uninstallation

The uninstall process is executed in reverse order to the installation process.

You can refer to the deployment file described in the [installation](./installation.md) section, and execute the following commands to complete the uninstallation:

```shell
$ kubectl delete -f 3.deploy-bigip-kubernetes-gateway-controller.yaml
$ kubectl delete -f 2.install-bigip-kubernetes-gatewayapi-CRDs.yaml
$ kubectl delete -f 1.clusterrole-and-binding.yaml
```