# Parameters

By running the following command, we can get the full list of startup parameters:

*Note: Update the release version v0.2.1-20230411 to your own case.*

```shell
$ docker run f5devcentral/bigip-kubernetes-gateway:v0.2.1-20230411 /bigip-kubernetes-gateway-controller-linux --help

Usage of /bigip-kubernetes-gateway-controller-linux:
  -bigip-config-directory string
    	Directory of bigip-k8s-gw-conf.yaml file. (default "/bigip-config")
  -bigip-credential-directory string
    	Directory that contains the BIG-IP password file. To be used instead of bigip-password arguments. (default "/bigip-credential")
  -certificate-directory string
    	Directory that contains tls.crt and tls.key for webook https server. (default "/certificate-directory")
  -controller-name string
    	This controller name. (default "f5.io/gateway-controller-name")
  -health-probe-bind-address string
    	The address the probe endpoint binds to. (default ":8081")
  -kubeconfig string
    	Paths to a kubeconfig. Only required if out-of-cluster.
  -leader-elect
    	Enable leader election for controller manager. Enabling this will ensure there is only one active controller manager.
  -log-level string
    	The log level, valid values: trace, debug, info, warn, error (default "info")
  -metrics-bind-address string
    	The address the metric endpoint binds to. (default ":8080")
  -validates string
    	The items to validate synchronizingly, on operations concating multiple values with ',', valid values: gateway.listeners.tls.certificateRefs,httproute.parentRefs,httproute.rules.backendRefs,gateway.gatewayClassName
```

Some of those parameters require our attentions:

* `--bigip-config-directory`

  Additional BIG-IP configuration is passed in via a separate Kubernetes ConfigMap.
  
  In the installation YAML, the ConfigMap is mounted as Controller deployment's `volumeMounts`.

  See [BIG-IP Configuration](#big-ip-configuration) for explaination of the ConfigMap content.

* `--bigip-credential-directory`

  The password of BIG-IP is passed in via a Kubernetes Secret.
  
  In the installation YAML, the Secret is mounted as Controller deployment's `volumeMounts`.

* `--certificate-directory`

  When started, the Controller also works as a webhook server, we need to configure the certificates(CA/cert/key stored as a Secret) since K8S interacts with the webhook server through HTTPS.

  In the installation YAML, the Secret is mounted as Controller deployment's `volumeMounts`.

* `--validates`(>=v0.2.1)

  In some cases, users may want to perform checks on their defined YAML files before making actual changes to Kubernetes.

  If the YAML content does not meet the relevant conditions, the controller can provide feedback so that the YAML content can be adjusted accordingly.

  In controller, we use [Webhook](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) mechanism to achieve this. 

  When '--validates' is used at controller starts, the controller will check the referred resources' situation. 

  More details about its usage, see [example](https://github.com/f5devcentral/bigip-kubernetes-gateway/tree/master/examples/beforehand-validation){:target="_blank"}.

In the installation YAML file [deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml){:target="_blank"}, there is a sample of startup parameters:

```shell
"--controller-name=f5.io/gateway-controller-name",
"--bigip-config-directory=/bigip-config",
"--bigip-credential-directory=/bigip-credential",
"--certificate-directory=/tmp/k8s-webhook-server/serving-certs"
```

## BIG-IP Configuration

The mentioned ConfigMap above contains BIG-IP connection information and configuration items for network setup. 

The Controller would configure the BIG-IPs as specified to make sure the data plane connection between BIG-IP and the kubernetes cluster is OK. 

The meaning of fields are commented:

```yaml
  # BIG-IP management information
- management:
    # username, must be admin
    username: admin
    # management IP address for iControl Rest
    ipAddress: 10.250.2.219
    # optional, management port, default to 443
    port: 443

  # optional, overlay network configuration for flannel CNI mode
  # if it is commented (# flannel level), 
  # there will be no flannel configuration to k8s or bigip
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
        # this will be referred in nodeConfigs part.
        localAddress: 10.250.17.219
    # selfips configuration
    selfIPs:
        # the name of the self IP address definition
      - name: flannel-self
        # the IP address associated to the vxlan tunnel
        ipMask: 10.42.20.1/16
        # vlan or tunnel name, should match one of the tunnels
        vlanOrTunnelName: fl-tunnel
      - name: self-17
        ipMask: 10.250.17.219/24
        vlanOrTunnelName: vlan-17
    # configuration for bigip virtual node on k8s side
    nodeConfigs:
        # the public ip for vxlan tunnel connection
        # it will report error if it is not found in tunnels array
      - publicIP: 10.250.17.219
        # the pod CIDR, should match that in selfIPs' 'ipMask'
        # note that, the mask is different
        podCIDR: 10.42.20.0/24
  # optional, underlay network configuration for calico CNI mode
  # if it is commented, 'calico' should also be commented: # calico
  # there will be no calico configuration to k8s or bigip
  calico:
    # AS num on BIG-IP side
    localAS: &as 64512
    # AS num on K8S side, generally, it's same as localAS
    remoteAS: *as
    # self ips for bgp endpoint
    selfIPs:
        # it is as same as that in flannel port.
      - name: self-17
        ipMask: 10.250.17.220/24
        vlanOrTunnelName: vlan-17
    # the self ip used as the peer to interconnect with k8s.
    peerIPs:
      - 10.250.17.220
```

Note:

If we don't want to configure BIG-IP in `flannel` or `calico` mode, just remove/comment the `flannel` and `calico` parts(The keyword `flannel` and `calico` should be commented as well). Thus the Controller will keep the CNI configurations on BIG-IP as user configured them manually in advance.
