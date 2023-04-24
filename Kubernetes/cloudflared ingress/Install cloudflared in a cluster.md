```toc style: number```


Instructions taken from https://developers.cloudflare.com/cloudflare-one/tutorials/many-cfd-one-tunnel/

### Login to Cloudflare
```bash
cloudflared tunnel login
```

### Create a new tunnel

```bash

cloudflared tunnel create example-tunnel
```

Expected output
```console
Tunnel credentials written to /Users/cf000197/.cloudflared/ef824aef-7557-4b41-a398-4684585177ad.json. cloudflared chose this file based on where your origin certificate was found. Keep this file secret. To revoke these credentials, delete the tunnel.

Created tunnel example-tunnel with id ef824aef-7557-4b41-a398-4684585177ad

```

### Create the tunnel secret
```bash
kubectl create secret generic tunnel-credentials --from-file=credentials.json=/Users/cf000197/.cloudflared/ef824aef-7557-4b41-a398-4684585177ad.json
```


### Associate the tunnel with a DNS record

```bash
cloudflared tunnel route dns ef824aef-7557-4b41-a398-4684585177ad your-hostname
```


### Deploy cloudflared in the cluster

Create a file with the following contents. This was taken from https://github.com/cloudflare/argo-tunnel-examples/blob/master/named-tunnel-k8s/cloudflared.yaml

Update the Ingress section of the ConfigMap with the ingress routes you want to register

```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cloudflared
spec:
  selector:
    matchLabels:
      app: cloudflared
  replicas: 2 # You could also consider elastic scaling for this deployment
  template:
    metadata:
      labels:
        app: cloudflared
    spec:
      containers:
      - name: cloudflared
        image: cloudflare/cloudflared:2022.3.0
        args:
        - tunnel
        # Points cloudflared to the config file, which configures what
        # cloudflared will actually do. This file is created by a ConfigMap
        # below.
        - --config
        - /etc/cloudflared/config/config.yaml
        - run
        livenessProbe:
          httpGet:
            # Cloudflared has a /ready endpoint which returns 200 if and only if
            # it has an active connection to the edge.
            path: /ready
            port: 2000
          failureThreshold: 1
          initialDelaySeconds: 10
          periodSeconds: 10
        volumeMounts:
        - name: config
          mountPath: /etc/cloudflared/config
          readOnly: true
        # Each tunnel has an associated "credentials file" which authorizes machines
        # to run the tunnel. cloudflared will read this file from its local filesystem,
        # and it'll be stored in a k8s secret.
        - name: creds
          mountPath: /etc/cloudflared/creds
          readOnly: true
      volumes:
      - name: creds
        secret:
          # By default, the credentials file will be created under ~/.cloudflared/<tunnel ID>.json
          # when you run `cloudflared tunnel create`. You can move it into a secret by using:
          # ```sh
          # kubectl create secret generic tunnel-credentials \
          # --from-file=credentials.json=/Users/yourusername/.cloudflared/<tunnel ID>.json
          # ```
          secretName: tunnel-credentials
      # Create a config.yaml file from the ConfigMap below.
      - name: config
        configMap:
          name: cloudflared
          items:
          - key: config.yaml
            path: config.yaml
---
# This ConfigMap is just a way to define the cloudflared config.yaml file in k8s.
# It's useful to define it in k8s, rather than as a stand-alone .yaml file, because
# this lets you use various k8s templating solutions (e.g. Helm charts) to
# parameterize your config, instead of just using string literals.
apiVersion: v1
kind: ConfigMap
metadata:
  name: cloudflared
data:
  config.yaml: |
    # Name of the tunnel you want to run
    tunnel: example-tunnel
    credentials-file: /etc/cloudflared/creds/credentials.json
    # Serves the metrics server under /metrics and the readiness server under /ready
    metrics: 0.0.0.0:2000
    # Autoupdates applied in a k8s pod will be lost when the pod is removed or restarted, so
    # autoupdate doesn't make sense in Kubernetes. However, outside of Kubernetes, we strongly
    # recommend using autoupdate.
    no-autoupdate: true
    # The `ingress` block tells cloudflared which local service to route incoming
    # requests to. For more about ingress rules, see
    # https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/configuration/ingress
    #
    # Remember, these rules route traffic from cloudflared to a local service. To route traffic
    # from the internet to cloudflared, run `cloudflared tunnel route dns <tunnel> <hostname>`.
    # E.g. `cloudflared tunnel route dns example-tunnel tunnel.example.com`.
    ingress:
    # The first rule proxies traffic to the httpbin sample Service defined in app.yaml
    - hostname: tunnel.example.com
      service: http://web-service:80
    # This rule sends traffic to the built-in hello-world HTTP server. This can help debug connectivity
    # issues. If hello.example.com resolves and tunnel.example.com does not, then the problem is
    # in the connection from cloudflared to your local service, not from the internet to cloudflared.
    - hostname: hello.example.com
      service: hello_world
    # This rule matches any traffic which didn't match a previous rule, and responds with HTTP 404.
    - service: http_status:404
```

You can add and change the Ingress section once deployed, but you must restart the cloudflared deployment for the changes to take effect.


#### HTTPS and noTLSVerify (ignore TLS)
If your adding an ingress route for a HTTPS endpoint with a self signed certificate, you can ignore TLS errors by with `originRequest.noTLSVerify: true`

```yaml
- hostname: argocd.soxprox.com
      service: https://argocd-server.argocd.svc.cluster.local
      originRequest:
        noTLSVerify: true
```

