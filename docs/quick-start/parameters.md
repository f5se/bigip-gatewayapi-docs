# Parameters

The examples of the parameters are available in the deployment yaml file placed on the code repository:

[deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml){:target="_blank"}.

There are 2 parts of BIG-IP configurations:

* The password of BIG-IP passed in via a Kubernetes Secret(Part 1).
* Some additional BIG-IP configuration information passed in via a separate Kubernetes Configmap(Part 2).

In the controller deployment yaml file(Part 3), the two parts of configurations are passed in the form of volume, and the controller reads the specific configuration content of the indicated paths.

## Part 1: bigip-login

```yaml
---

apiVersion: v1
kind: Secret
metadata:
  name: bigip-login
  namespace: kube-system
data:
  password: UEBzc3cwcmQxMjM=              # base64 password for admin
type: Opaque

```

BIG-IP's `password` for admin is stored in a separate `Secret` type resource for security concern.

## Part 2: bigip-kubernetes-gateway-configmap

```yaml
---

apiVersion: v1
kind: ConfigMap
metadata:
  name: bigip-kubernetes-gateway-configmap
  namespace: kube-system
data:
  bigip-kubernetes-gateway-config: |
    - management:
        username: admin
        ipAddress: 10.250.15.180
        Management IP for connection.
        port: 443
      flannel:
        tunnels:
          - name: fl-tunnel
            profileName: fl-vxlan
            port: 8472
            localAddress: 10.250.18.119
        selfIPs:
          - name: flannel-self
            ipMask: 10.42.20.1/16
            tunnelName: fl-tunnel
      calico:


```

Within the above configmap, it stores BIG-IP configuration for network setup and resource managements. 

The controller would configure the BIG-IPs as specified to make sure the data plane connection between BIG-IP and the kubernetes cluster is OK. 

The meaning of fields are:

```yaml

      # BIG-IP management info
    - management:
        # username, must be amdin currently
        username: admin
        # management IP address for iControl Rest
        ipAddress: 10.250.15.180
        # optional, management port, default to 443
        port: 443

      # optional, overlay network configuration for flannel CNI mode
      flannel:
        # tunnels configuration
        tunnels:
            # tunnel name
          - name: fl-tunnel
            # tunnel profile name for binding to the very tunnel
            profileName: fl-vxlan
            # tunnel profile port for binding to the very tunnel
            port: 8472
            # the local address for the tunnel(VTEP)
            localAddress: 10.250.18.119
        # selfips configuration
        selfIPs:
            # the name of the self IP address definition
          - name: flannel-self
            # the IP address associated to the vxlan tunnel
            ipMask: 10.42.20.1/16
            # tunnel name, should match one of the tunnels
            tunnelName: fl-tunnel

      # optional, underlay network configuration for calico CNI mode
      calico:
```

Note that, 

* If we don't want to configure BIG-IP in `flannel` mode, just remove/comment the `flannel` parts. It's same to `calico` case.

* Futher, if we don't want to configure BIG-IP at all, remove/comment both of them, the controller will keep the CNI configurations on BIG-IP as user configure them manually in advance.

## Part 3: bigip-kubernetes-gateway deployment and service

```yaml

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: bigip-kubernetes-gateway
  namespace: kube-system
spec:
  replicas: 1
  
  #...

    spec:
      serviceAccountName: k8s-bigip-ctlr
      nodeSelector:
        node-role.kubernetes.io/control-plane: "true"
        # node-role.kubernetes.io/master: "true"
      containers:
        # use `kubectl logs -f deployment/bigip-kubernetes-gateway -c bigip-kubernetes-gateway-pod -n kube-system` for tracing.
        - name: bigip-kubernetes-gateway-pod
          image: f5devcentral/bigip-kubernetes-gateway:v0.0.4-20221219
          imagePullPolicy: IfNotPresent
          command: ["/bigip-kubernetes-gateway-controller-linux"]
          args: [
            "--controller-name=f5.io/gateway-controller-name",
            "--bigip-config-directory=/bigip-config",
            "--bigip-credential-directory=/bigip-credential",
          ]
          volumeMounts:
            - name: bigip-credential
              mountPath: "/bigip-credential"
              readOnly: true
            - name: bigip-config
              mountPath: /bigip-config
              readOnly: true
      volumes:
        - name: bigip-credential
          secret:
            secretName: bigip-login
        - name: bigip-config
          configMap:
            name: bigip-kubernetes-gateway-configmap

```
For the yaml file above, let's pay attention to `image`, `args`, `volumeMounts` and `volumes` parts.

For details about the usage of the file, See [installation](../quick-start/installation.md).
