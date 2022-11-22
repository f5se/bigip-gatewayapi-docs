# Uninstallation

The uninstall process is executed in reverse order to the installation process.

You can refer to the deployment file described in the [installation](./installation.md) section, and execute the following commands to complete the uninstallation:

```shell
$ kubectl apply -f 3.deploy-bigip-k8s-gtw-ctlr.yaml
$ kubectl apply -f 2.install-bigip-k8s-gtw-crds.yaml
$ kubectl apply -f 1.clusterrole-and-binding.yaml
$ kubectl apply -f 0.bigip-config.yaml
```