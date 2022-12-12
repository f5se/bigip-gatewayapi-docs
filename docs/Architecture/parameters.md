# Parameters

Examples of the usecase of parameters are available in the deployment file:

deploy/3.deploy-bigip-kubernetes-gateway-controller.yaml

```yaml
...

      containers:
        # kubectl logs -f deployment/bigip-kubernetes-gateway -c bigip-kubernetes-gateway-pod -n kube-system
        - name: bigip-kubernetes-gateway-pod
          image: zongzw/bigip-kubernetes-gateway:latest-20221115-162758
          imagePullPolicy: IfNotPresent
          command: ["/bigip-kubernetes-gateway-controller-linux"]
          args: [
            # "--bigip-password=$(BIGIP_PASSWORD)",
            "--bigip-url=$(BIGIP_URL)",
            "--mode=calico",
            "--controller-name=f5.io/gateway-controller-name",
            "--bigip-config-directory=/bigip-config",
            "--bigip-credential-directory=/bigip-credential"
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
...
```

As shown above, the password of BIG-IP is passed via a Kubernetes Secret and some additional BIG-IP configuration information is passed via a separate Kubernetes Configmap.

Secret example:
```yaml
...
apiVersion: v1
kind: Secret
metadata:
  name: bigip-login
  namespace: kube-system
data:
  password: UEBzc3cwcmQxMjM=              # base64 encoded password
type: Opaque
...
```

Configmap example:
```yaml
...

    bigips:
      - mgmtIpAddress: "10.50.17.104"
        url: "https://10.50.17.104:8443"
        username: "admin"
        # The following items are only required if the --mode argument of the controller equals to 'flannel',
        # in which they must be set accordingly based on the specific environment
        vxlanProfileName: "fl-vxlan"
        vxlanPort: "8472"
        vxlanLocalAddress: "4.4.4.4"
        selfIpName: "flannel-self"
        selfIpAddress: "5.5.5.5/28"

...
```

For details about the usage of the file, See [installation](../quick-start/installation.md)
