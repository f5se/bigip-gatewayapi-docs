# Get Started

You’ll need a Kubernetes cluster to run against. You can use [KIND](https://sigs.k8s.io/kind) to get a local cluster for testing, or run against a remote cluster.

**Note:** bigip-kubernetes-gateway controller will automatically use the current context in your kubeconfig file (i.e. whatever cluster `kubectl cluster-info` shows). If the controller runs in In-Cluster mode, it will depends on the serviceaccount and role/role-binding described in [installation](./installation.md).


