## Traffic Splitting by HTTPRoute Usecase

In this usecase, we will understand the slightly more complex application method of `httproute`. 

You can implement the grayscale publishing of the application through `httproute`, and smoothly transition traffic to new services.

See [here](./simplehttp.md) for `gatwayclass` and `gateway` definitions.

```yaml

---

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: myhttproute
spec:
  parentRefs:
    - name: mygateway
      sectionName: http
  hostnames:
    - gateway.test.automation
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /test1
      backendRefs:
        - name: coffee
          port: 80
          weight: 1
        - name: tea
          port: 80
          weight: 9
    - matches:
        - path:
            type: PathPrefix
            value: /test2
      backendRefs:
        - name: coffee
          port: 80
          weight: 9
        - name: tea
          port: 80
          weight: 1

```

services.yaml
```yaml
---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: tea
spec:
  replicas: 2
  selector:
    matchLabels:
      app: tea
  template:
    metadata:
      labels:
        app: tea
    spec:
      containers:
        - name: tea
          image: nginx:latest
          ports:
            - containerPort: 80
          volumeMounts:
            - name: config-volume
              mountPath: /etc/nginx/nginx.conf
              subPath: nginx.conf
            - name: config-volume
              mountPath: /etc/nginx/njs/dumps.js
              subPath: dumps.js
      volumes:
        - name: config-volume
          configMap:
            name: nginx-config-tea

---

apiVersion: v1
kind: Service
metadata:
  name: tea
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    name: http
  selector:
    app: tea

---

apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config-tea
data:
  nginx.conf: |
    user  nginx;
    worker_processes  1;

    load_module modules/ngx_http_js_module.so;

    events {
        worker_connections  1024;
    }

    http {
        js_import njs/dumps.js;
        server {
            listen       80;
            server_name  localhost;

            location / {
                js_content dumps.hello;
            }
        }
    }
  dumps.js: |
    function hello(r) {
        let d = {
            'queries': r.args,
            'headers': r.headersIn,
            'version': r.httpVersion,
            'method': r.method,
            'remote-address': r.remoteAddress,
            'body': r.requestText,
            'uri': r.uri,
            'server_name': "TEA"
        }

        r.return(200, JSON.stringify(d)+"\n");
    }

    export default {hello};



---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: coffee
spec:
  replicas: 2
  selector:
    matchLabels:
      app: coffee
  template:
    metadata:
      labels:
        app: coffee
    spec:
      containers:
        - name: coffee
          image: nginx:latest
          ports:
            - containerPort: 80
          volumeMounts:
            - name: config-volume
              mountPath: /etc/nginx/nginx.conf
              subPath: nginx.conf
            - name: config-volume
              mountPath: /etc/nginx/njs/dumps.js
              subPath: dumps.js
      volumes:
        - name: config-volume
          configMap:
            name: nginx-config-coffee

---

apiVersion: v1
kind: Service
metadata:
  name: coffee
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    name: http
  selector:
    app: coffee

---

apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config-coffee
data:
  nginx.conf: |
    user  nginx;
    worker_processes  1;

    load_module modules/ngx_http_js_module.so;

    events {
        worker_connections  1024;
    }

    http {
        js_import njs/dumps.js;
        server {
            listen       80;
            server_name  localhost;

            location / {
                js_content dumps.hello;
            }
        }
    }
  dumps.js: |
    function hello(r) {
        let d = {
            'queries': r.args,
            'headers': r.headersIn,
            'version': r.httpVersion,
            'method': r.method,
            'remote-address': r.remoteAddress,
            'body': r.requestText,
            'uri': r.uri,
            'server_name': "COFFEE"
        }

        r.return(200, JSON.stringify(d)+"\n");
    }

    export default {hello};

```

In the above `httproute` definition, we see that there are two rules:

* when we access /test1, 90% of the traffic is forwarded to the tea service
* when we access /test2, 90% of the traffic is forwarded to the coffee service

You may change the two ratios, and run 'kubectl apply -f httproute.yaml' again to achieve a change in the traffic ratio.