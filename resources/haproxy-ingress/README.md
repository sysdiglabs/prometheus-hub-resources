# HAProxy ingress controller for Kubernetes
HAProxy ingress controller is a Kubernetes resource that routes traffic from outside your cluster to services within the cluster. 

As this ingress controller uses the HAProxy load balancer, all resources (configuration files, dashboards, alerts...) are also valid for a standalone HAProxy.

# Metrics
The HAProxy ingress instruments Prometheus metrics on the following scopes:
- Global
- Frontend
- Backend
- Server (disabled in the configuration provided)

For a complete list of the metrics exported, see the [HAProxy exporter documentation](https://github.com/haproxy/haproxy/blob/master/addons/promex/README)

# Number of time series generated
Usually, for the configuration provided, the number of time series is approximately:
- 150 x number of ingress pods
- 50 x number of ingress pods x ingress resources

For further information, see [HAProxy Kubernetes ingress controller](https://github.com/haproxytech/kubernetes-ingress).

# Attributions
Configuration files and dashboards are maintained by [Sysdig team](https://sysdig.com/).

Using [HAProxy Kubernetes ingress controller](https://github.com/haproxytech/kubernetes-ingress) with Apache 2.0 license.
