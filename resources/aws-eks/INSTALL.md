# Prerequisites
KSM and cAdvisor generates a high number of metrics. As the Sysdig Agent has a limit of time series that can send to Sysdig Monitor, you have to deploy a Prometheus server and create the recording rules that we provide. This way, you will filter only the metrics that you need.

To deploy a Prometheus server you will need:
* [helm](https://helm.sh/docs/intro/install/)  
* [helmfile](https://github.com/roboll/helmfile)

# Installing and configuring Prometheus server
## Installing a new Prometheus server with Helm
This section helps you install and configure a new Prometheus server with the recording rules.  

1. Download the following files:
- helmfile.yaml
- recording_rules.yaml
- prometheus.yaml
- prometheus.yml.gotmpl

2. Execute:

```
helmfile sync
```

## Configuring an existing Prometheus server
This section explains how to configure an existing Prometheus server with the recording rules.

To do this, you can do one of the following:

* Annotate the StatefulSet:

```
kubectl -n monitoring patch sts prometheus-server -p '{"spec":{"template":{"metadata":{"annotations":{"prometheus.io/scrape": "true", "prometheus.io/port": "9090"}}}}}'
```

* Download the file `prometheus-deploy.yaml` and apply it:

```
kubectl -n monitoring apply -f prometheus-deploy.yaml
```

# Configuring the Sysdig Agent
To install the Sysdig Agent, ensure that you have at least one EC2 instance in your EKS cluster.

1. Install with helm Sysdig Agent in your cluster:

```
kubectl create ns sysdig-agent
helm install -n sysdig-agent \
     --set sysdig.accessKey=<YOUR-ACCESS-KEY> \
     --set sysdig.settings.k8s_cluster_name=<YOUR-CLUSTER-NAME> \
     sysdig-agent sysdiglabs/sysdig
```

2. Download the sample [Sysdig Agent configuration file](include/sysdig-agent-config.yaml) and apply it:

```
kubectl apply -f sysdig-agent-config.yaml
```
