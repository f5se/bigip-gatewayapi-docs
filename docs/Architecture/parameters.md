# Parameters

Examples of the usecase of parameters are available in the deployment file:

[deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml){:target="_blank"}.

There are 2 parts of BIG-IP configurations:

* The password of BIG-IP passed in via a Kubernetes Secret(Part 1).
* Some additional BIG-IP configuration information passed in via a separate Kubernetes Configmap(Part 2).

In the controller deployment yaml, the two parts of the parameters are passed in the form of volume, and the controller reads the specific configuration content of indicated paths.

### Part 1: bigip-login

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

BIG-IP's `password` for admin is stored in a separate `Secret` type resource for secure consideration.

### Part 2: bigip-kubernetes-gateway-configmap

```yaml
---

apiVersion: v1
kind: ConfigMap
metadata:
  name: bigip-kubernetes-gateway-configmap
  namespace: kube-system
data:
  bigip-kubernetes-gateway-config.yaml: |      # configuration for BIG-IP setup
    bigips:
      - mgmtIpAddress: "10.250.17.104"
        vxlanProfileName: "fl-vxlan"
        vxlanPort: "8472"
        vxlanLocalAddress: "10.250.18.120"
        selfIpName: "flannel-self"
        selfIpAddress: "10.42.20.1/16"
        url: "https://10.250.17.104:8443"
        username: "admin"


```

Within the above configmap, it stores BIG-IP configuration for network setup and resource managements. 

The controller would configure the BIG-IPs as specified to make sure the data plane connection between BIG-IP and the kubernetes cluster is OK. 

The meaning of fields are:

* **mgmtIpAddress**: "10.250.17.104"

  Management IP for connection.

* **vxlanProfileName**: "fl-vxlan"

  Tunnel profile for binding to the very tunnel in flannel CNI mode.

* **vxlanPort**: "8472"

  Tunnel profile port for binding to the very tunnel in flannel CNI mode.

* **vxlanLocalAddress**: "10.250.18.120"

  The local address for the tunnel(VTEP) in flannel CNI mode.

* **selfIpName**: "flannel-self"

  The name of the self IP address definition.

* **selfIpAddress**: "10.42.20.1/16"

  The IP address associated to the vxlan tunnel.

* **url**: "https://10.250.17.104:8443"

  URL for BIG-IP connection and management.

* **username**: "admin"

  username for management, must be "admin" for now.

### Part 3: bigip-kubernetes-gateway deployment and service

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
          image: jeffreycoho/bigip-kubernetes-gateway:12060066-20221206-153736
          imagePullPolicy: IfNotPresent
          command: ["/bigip-kubernetes-gateway-controller-linux"]
          args: [
            "--controller-name=f5.io/gateway-controller-name",
            "--mode=flannel",
            "--vxlan-tunnel-name=fl-vxlan",
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

For details about the usage of the file, See [installation](../quick-start/installation.md).
