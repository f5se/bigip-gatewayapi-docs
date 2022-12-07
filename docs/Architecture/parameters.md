# Parameters

Examples of the usecase of parameters are available in the deployment file:

deploy/3.deploy-bigip-k8s-gtw-ctlr.yaml

```yaml
...

      containers:
        # kubectl logs -f deployment/bigip-kubernetes-gateway -c bigip-kubernetes-gateway-pod -n kube-system
        - name: bigip-kubernetes-gateway-pod
          image: zongzw/bigip-kubernetes-gateway:latest-20221115-162758
          imagePullPolicy: IfNotPresent
          env:
            - name: BIGIP_USERNAME
              valueFrom:
                secretKeyRef:
                  name: bigip-login
                  key: username
            - name: BIGIP_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: bigip-login
                  key: password
            - name: BIGIP_URL
              valueFrom:
                secretKeyRef:
                  name: bigip-login
                  key: url
          command: ["/bigip-kubernetes-gateway-controller-linux"]
          args: [
            "--bigip-username=$(BIGIP_USERNAME)",
            "--bigip-password=$(BIGIP_PASSWORD)",
            "--bigip-url=$(BIGIP_URL)",
            "--gateway-class=bigip"
          ]
...
```

As shown above, you can see that we currently pass in the connection url, username and password of BIG-IP through parameters. In the future, this method will change after the implementation of the gatewayclass.

For details about the usage of the file, See [installation](../quick-start/installation.md)

The controller startup configuration can also be passed in through configmap, and the specific usage method is still under development.