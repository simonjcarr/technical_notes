First make sure you have installed following
[[OLM Installation]]
[[Postgres Operator Installation]]

## Installation

### Create a namespace
```bash
kubectl create ns keycloak
```

These instructions are taken from https://www.keycloak.org/operator/installation
#### Apply the CRD's

```bash
kubectl apply -f https://raw.githubusercontent.com/keycloak/keycloak-k8s-resources/21.0.2/kubernetes/keycloaks.k8s.keycloak.org-v1.yml -n yournamespace
kubectl apply -f https://raw.githubusercontent.com/keycloak/keycloak-k8s-resources/21.0.2/kubernetes/keycloakrealmimports.k8s.keycloak.org-v1.yml -n yournamespace
```

#### Install the Operator
Currently the operator must be installed in the same namespace that you want to install Keycloak

```bash
kubectl apply -f https://raw.githubusercontent.com/keycloak/keycloak-k8s-resources/21.0.2/kubernetes/kubernetes.yml -n yournamespace
```

## Deploy Keycloak
### Create a TLS Certificate secret.
If you don't have a cert and key file, you can create a self signed cert using the following command.

```bash
openssl req -subj '/CN=test.keycloak.org/O=Test Keycloak./C=US' -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem
```

### Deploy the certificate
```bash
kubectl create secret tls example-tls-secret --cert certificate.pem --key key.pem -n yournamespace
```

#### Create a Database Credentials Secret.
if you have already deployed Postgres from an operator, database secrets should have already been created in the namespace it was deployed in. if not, or if the database is external refer to [[Create a Generic Secret]]

### Create the Keycloak instance
Use the following YAML to create a Keycloak instance

**You must create this instance in the same namespace that you installed the Keycloak Operator**

```yaml
apiVersion: k8s.keycloak.org/v2alpha1
kind: Keycloak
metadata:
  name: example-kc
spec:
  instances: 1
  ingress:
    enabled: true
  db:
    vendor: postgres
    host: keycloak-postgres-cluster
    usernameSecret:
      name: postgres.keycloak-postgres-cluster.credentials.postgresql.acid.zalan.do
      key: username
    passwordSecret:
      name: postgres.keycloak-postgres-cluster.credentials.postgresql.acid.zalan.do
      key: password
  http:
    tlsSecret: keycloak-tls-secret
  hostname:
    hostname: keycloak.soxprox.com
  unsupported:
    podTemplate:
      spec:
        containers:
          - name: keycloak
            env:
              - name: PROXY_ADDRESS_FORWARDING
                value: "true"
              - name: KC_HOSTNAME
                value: "keycloak.soxprox.com"
              - name: KC_PROXY
                value: "passthrough"
              - name: KEYCLOAK_ADMIN
                value: "admin"
              - name: KEYCLOAK_ADMIN_PASSWORD
                value: "password"

```

Then apply the yaml with 
```bash
kubectl apply -f name-of-your-file.yaml -n yournamespace
```

### Get the Initial admin secret
The Keycloak operator will create a secret in the same namespace as the Keycloak instance.
Fetch and decode the secret using [[Get a secret using kubectl]]
