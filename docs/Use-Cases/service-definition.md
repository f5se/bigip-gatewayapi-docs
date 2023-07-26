
*The servcie definitions for references only*

Note that:

`tea` servcie is defined as `NodePort` and `coffee` service is `ClusterIP`. Change it as necessary.

## tea.yaml
```yaml

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: tea
spec:
  replicas: 1
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


```

## coffee.yaml

```yaml
---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: coffee
spec:
  replicas: 1
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