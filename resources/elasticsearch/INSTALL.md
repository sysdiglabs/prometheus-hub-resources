# Installing the exporter
To install the exporter, do the following:
1. Verify whether your elastic is secured or not
2. If it is not secured, download the `exporter_no_credentials.yaml` file and execute:
```
kubectl apply -f exporter_no_credentials.yaml
```
The exporter will try to connect to the service named `elasticsearch` on the port `9200`. If this is not the port or the service, change these lines:
```yaml
- name: ES_URI
  value: https://elasticsearch:9200
```
Located here
```yaml
...
spec:
  template:
    spec:
      containers:
      - command:
      ...
        env:
          - name: ES_URI
            value: https://elasticsearch:9200
      ...
```
3. If the elasticsearch is secured similar to the elasticseach deployed in the Sysdig on-prem environments, download the `exporter_with_credentials.yaml` file.

4. Apply by configuration changes by executing the following:
```
kubectl apply -f exporter_with_credentials.yaml
```
This configuration will use the user and the password saved as secrets, and will mount a volume with the ca.

5. If you are using the exporter with credentials but you are not using sysdig, then edit the secrets name, the service, and the port.

```yaml
...
spec:
  template:
    spec:
      containers:
      - command:
      ...
        env:
        - name: ELASTIC_USER
          valueFrom:
            configMapKeyRef:
              key: elasticsearch.adminuser
              name: sysdigcloud-config
        - name: ES_URI
          value: https://$(ELASTIC_USER):$(ELASTICSEARCH_ADMIN_PASSWORD)@sysdigcloud-elasticsearch:9200
      envFrom:
      - secretRef:
          name: elasticsearch-tls
      ...
```
# Sysdig Agent configuration
> Requires Sysdig Agent >= v11.3 

In the ElasticSearch exporter deployment, use the Sysdig annotations to configure the port of the exporter as scraping port. You can see an example in the `exporter_no_credentials.yaml` file.

Additionally, you can use these labels to add the namespace, workload type, and name of the database the exporter will retrieve the data from.

```yaml
spec:
  template:
    metadata:
      annotations:
        promcat.sysdig.com/port: "9108"
        # Add here the namespace, workload type (deployment, statefulset, replicaset, daemonset) 
        # and workload name of the instance that the exporter will take data from
        promcat.sysdig.com/target_ns: elastic-namespace
        promcat.sysdig.com/target_workload_type: statefulset
        promcat.sysdig.com/target_workload_name: elasticsearch
        promcat.sysdig.com/integration_type: elasticsearch
```
This way, on the Sysdig Monitor, you can view the associated metrics corresponding to the database pods and the exporter.

After you configure the Sysdig annotations, download the sample configuration file and apply it by:
```bash
kubectl apply -f sysdig-agent-config.yaml
```