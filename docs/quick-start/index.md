# Get Started

You’ll need a Kubernetes cluster to run against. You can use [KIND](https://sigs.k8s.io/kind){:target="_blank"} to get a local cluster for testing, or run against a remote cluster.

**Note:** bigip-kubernetes-gateway controller will automatically use the current context in your kubeconfig file (i.e. whatever cluster `kubectl cluster-info` shows). If the controller runs in In-Cluster mode, it will depends on the serviceaccount and role/role-binding described in [installation](./installation.md).

## Kubernetes Setup For Gateway API Integration

After you have a K8s cluster, we need to configure it for different CNI types to make sure connection between BIG-IP and kubernetes cluster is OK.

**Note:** 

*To enable Gateway API integration via BIG-IP, actually, we need to configure both sides of BIG-IPs and the Kubernetes cluster, however, the BIG-IP side is configured by controller itself automatically when the controller is started.*

*Here, we only need to configure Kubernetes side manually. For different CNIs, we have different configuration steps as following.*

### In Flannel mode

In flannel network mode, we need to create a BIG-IP virtual node to connect the BIG-IP node to the Kubernetes.

Use the following yaml configuration file bigip1.yaml:

bigip1.yaml:
```yaml
apiVersion: v1
kind: Node
metadata:
  name: bigip1
annotations:
  # Replace IP with Self-IP for your deployment
  flannel.alpha.coreos.com/public-ip: "10.250.18.105"
  # uncomment the following line if using v6 tunnel and modify bigip v6 address
  # flannel.alpha.coreos.com/public-ipv6: "2021:15::125"
  # Replace MAC with your BIGIP Flannel VXLAN Tunnel MAC
  flannel.alpha.coreos.com/backend-data: '{"VtepMAC":"fa:16:3e:d5:28:07"}'
  # uncomment the following line if using v6 tunnel and modify mac accordingly
  # flannel.alpha.coreos.com/backend-v6-data: '{"VtepMAC":"fa:16:3e:d5:28:07"}'
  flannel.alpha.coreos.com/backend-type: "vxlan"
  flannel.alpha.coreos.com/kube-subnet-manager: "true"
spec:
  # Replace Subnet with your BIGIP Flannel Subnet
  podCIDR: "10.42.20.0/24"
  # uncomment the following 3 lines if using v6 tunnel and modify CIDRs using real data
  #podCIDRs:
  #- "10.42.20.0/24"
  #- "2021:118:2:2::/64"
```

The mac address can be obtained using the TMSH command on BIG-IP:

`$ show net tunnels tunnel fl-tunnel all-properties`

`$ show net tunnels tunnel fl-tunnel6 all-properties`

Execute `kubectl apply -f bigip1.yaml` command to create the above virtual node.

### In Calico mode

In calico mode, we need to peer BIG-IP(s) as the BGP neighbors of Kubernetes nodes. 

So, on master node, run the command to get `calicoctl` command line:

```shell
$ curl -O -L https://github.com/projectcalico/calicoctl/releases/download/v3.10.0/calicoctl`
$ chmod +x calicoctl
$ sudo mv calicoctl /usr/local/bin
```

Edit `/etc/calico/calico.ctl.cfg` file

```shell
$ sudo mkdir /etc/calico
$ vim /etc/calico/calicoctl.cfg
```

calicoctl.cfg
```yaml
apiVersion: projectcalico.org/v3
kind: CalicoAPIConfig
metadata:
spec:
  datastoreType: "kubernetes"
  kubeconfig: "/root/.kube/config"    # change to actual kubeconfig path
```

Run `calicoctl get nodes` to verify calicoctl runtime works OK.

Run the following command to create BGP Group:

```shell

cat << EOF | calicoctl create -f -
apiVersion: projectcalico.org/v3
kind: BGPConfiguration
metadata:
  name: default
spec:
  logSeverityScreen: Info
  nodeToNodeMeshEnabled: true
  asNumber: 64512
EOF
```

Run the following command to create BIG-IP peer for the kubernetes cluster.

**Notes**: *Change the peerIP to actual BIG-IP traffic IP(the selfIP for data traffic).*

```shell
cat << EOF | calicoctl create -f -
apiVersion: projectcalico.org/v3
kind: BGPPeer
metadata:
    name: bgppeer-bigip1
spec:
  peerIP: 10.250.17.111
  asNumber: 64512
EOF
```

After the configuration, we can use `calicoctl node status` command to check the BIG-IP peer status:

```shell
# calicoctl node status
Calico process is running.

IPv4 BGP status
+---------------+-------------------+-------+----------+-------------+
| PEER ADDRESS  |     PEER TYPE     | STATE |  SINCE   |    INFO     |
+---------------+-------------------+-------+----------+-------------+
| 10.250.17.182 | node-to-node mesh | up    | 03:07:33 | Established |
| 10.250.17.111 | global            | up    | 06:18:28 | Established |
+---------------+-------------------+-------+----------+-------------+
```

More references, see https://f5-k8s-istio-lab.readthedocs.io/en/latest/BGP/introduction.html