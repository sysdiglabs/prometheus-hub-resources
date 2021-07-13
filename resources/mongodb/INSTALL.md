# Configuring MongoDB for the exporter
To install MongoDB, you can download the _mongo-deploy.yaml_ file and run:
```bash
kubectl apply -f mongo-deploy.yaml
```

If you want to use a non-admin user for the exporter, you will have to create a user and grant the roles to be able to scrape statistics.

In the mongo shell:
```sql
use admin
db.auth("your-admin-user", "your-admin-password")
db.createUser(
   {
      user: "exporter-user",
      pwd: "exporter-pass",
      roles: [
        { role: "clusterMonitor", db: "admin" },
        { role: "read", db: "admin" },
        { role: "read", db: "local" }
      ]
   }
)
```

# Installing the exporter
To install the MongoDB exporter, use the [Helm chart](https://github.com/helm/charts/tree/master/stable/prometheus-mongodb-exporter) available.

1. Create a _values.yaml_ file with the following parameters:
```yaml
fullnameOverride: "mongodb-exporter"
podAnnotations:
  prometheus.io/scrape: "true"
  prometheus.io/port: "9216"
serviceMonitor:
  enabled: false
mongodb:
  uri: mongodb://exporter-user:exporter-pass@mongodb:27017
```

Note that the _mongodb.uri_ parameter is a valid [MongoDB URI](https://github.com/prometheus-community/helm-charts/blob/main/charts/prometheus-mongodb-exporter/README.md).

2. In the [MongoDB URI](https://github.com/prometheus-community/helm-charts/blob/main/charts/prometheus-mongodb-exporter/README.md), include the user and password of the exporter.
The Helm chart will create a Kubernetes secret with the URI so it will not be visible.

3. To install the exporter, add the helm repository and run the helm install command:
```
# Add repository
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# Helm 3
helm install  mongodb-exporter prometheus-community/prometheus-mongodb-exporter -f values.yaml

# Helm 2
helm install --name mongodb-exporter prometheus-community/prometheus-mongodb-exporter -f values.yaml
```

The metrics will be available in port 9216 of the exporter pod.

## Using TLS or SSL Authentication
To use TLS or SSL authentication, you can use the `mongodb-exporter-auth-deploy.yaml` file to deploy the exporter because the helm chart does not support authentication.

To configure authentication, do the following:

1. Create a text file with the connection string for your MongoDB by using these examples:
```
# TLS
mongodb://mongodb-exporter-user:mongodb-exporter-pass@<YOUR-MONGODB-HOST>:<PORT>/admin?tls=true&tlsCertificateKeyFile=/etc/mongodb/mongodb-exporter-key.pem&tlsAllowInvalidCertificates=true&tlsCAFile=/etc/mongodb/mongodb-exporter-ca.pem

# SSL
mongodb://mongodb-exporter-user:mongodb-exporter-pass@<YOUR-MONGODB-HOST>:<PORT>/admin?ssl=true&sslclientcertificatekeyfile=/etc/mongodb/mongodb-exporter-key.pem&sslinsecure=true&sslcertificateauthorityfile=/etc/mongodb/mongodb-exporter-ca.pem
```
2. Create the secret for the connection string:
```
kubectl create secret generic mongodb-exporter \
  --from-file=mongodb-uri=<route-to-file-with-connection-uri.txt>
```

3. Create the secret with the private key and the certificate authority (CA). If you do not have a CA file, you can use an empty file instead:
```
kubectl create secret generic mongodb-exporter-auth \
  --from-file=mongodb-key=<route-to-your-private-key.pem> \
  --from-file=mongodb-ca=<route-to-your-ca.pem>
```

1. Download the `mongodb-exporter-auth-deploy.yaml` file and apply the configuration:
```
kubectl apply -f mongodb-exporter-auth-deploy.yaml
```

# SYSDIG AGENT CONFIGURATION
In the _values.yaml_ of the Helm chart, include the Prometheus annotations to configure the port of the exporter as the scraping port.    

You can download the sample configuration file and apply it by:
```bash
kubectl apply -f sysdig-agent-config.yaml
```
