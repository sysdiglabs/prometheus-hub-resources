# Installing the Apache exporter
Apache provides metics in its own format via its [ServerStatus module](https://httpd.apache.org/docs/2.4/mod/mod_status.html).
To enable this module, include (or uncomment) the following line in your apache configuration file:
```xml
LoadModule status_module modules/mod_status.so
<Location "/server-status">
  SetHandler server-status
</Location>
```
The native statistics page of your Apache server will be available on http://your-ip/server-status/?auto.
This is a basic configuration. You may configure access control to this endpoint as explained in [apache documentation for this module](https://httpd.apache.org/docs/2.4/mod/mod_status.html).

The metrics available on the Apache statistics page need to be translated to Prometheus format. To do so, you will use the [Apache Prometheus Exporter](https://github.com/Lusitaniae/apache_exporter). In the deployment, the exporter is used as a sidecar of the Apache server instance and the endpoint to scrape is passed as the  _--scrape_uri_ parameter.

# Installing the Grok exporter
You will use the [Grok exporter](https://hub.docker.com/r/palobo/grok_exporter) to parse the logs of Apache and obtain metrics of errors and requests codes.
The configuration used is based on the one explained in the [blog post of Robust Perception](https://www.robustperception.io/getting-metrics-from-apache-logs-using-the-grok-exporter).
This configuration relies on Apache to produce common standard logs. Other log formats might require different Grok configuration.

To configure Apache server to produce common logs, include (or uncomment) the following in your Apache configuration file:
```xml
<IfModule log_config_module>
       LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
       CustomLog /usr/local/apache2/logs/accesss.log common
</IfModule>
 ```

Add the following configuration to the ConfigMap and pass this to the Grok exporter:
```yaml
global:
    config_version: 2
input:
    type: file
    path: /tmp/logs/accesss.log
    readall: true
    fail_on_missing_logfile: false
grok:
    patterns_dir: ./patterns
metrics:
    - type: counter
      name: apache_http_response_codes_total
      help: HTTP requests to Apache
      match: '%{COMMONAPACHELOG}'
      labels:
          method: '{{.verb}}'
          code: '{{.response}}'
    - type: gauge
      name: apache_http_response_bytes_total
      help: Size of HTTP responses
      match: '%{COMMONAPACHELOG}'
      cumulative: true
      value: '{{.bytes}}'
      labels:
          method: '{{.verb}}'
          code: '{{.response}}'
    - type: gauge
      name: apache_http_last_request_seconds
      help: Timestamp of the last HTTP request
      match: '%{COMMONAPACHELOG}'
      value: '{{timestamp "02/Jan/2006:15:04:05 -0700" .timestamp}}'
      labels:
          method: '{{.verb}}'
          code: '{{.response}}'
server:
    port: 9144
```

Next, deploy the Grok exporter as a sidecar of the Apache server. Create a shared volume to store the access log so the Grok exporter can parse it and generate metrics.

# Merging the two exporters
Because it is not possible to annotate two ports to be scraped by Prometheus, use another sidecar to merge both exporters into a single endpoint that will be exposed and annotated. To do so, use the [exporter-merger](https://github.com/rebuy-de/exporter-merger). You will pass
the URL address of both the exporters in the _MERGER_URLS_ environment variable.

# Deploying in Kubernetes
To deploy the configuration example in Kubernetes, download the configuration file and run:
```
kubectl apply -f apache-deploy.yaml
```

# Configuring Sysdig Agent

Download the sample [Sysdig Agent configuration file](include/sysdig-agent-config.yaml). By default the job will be in the agent so you don't need to change anything but if you don't have it here you have the way to do that

Apply the changes:
```bash
kubectl apply -f sysdig-agent-config.yaml
```
