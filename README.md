# kube-prometheus-grafana


This repository collects Kubernetes manifests, [Grafana](http://grafana.com/) dashboards, and [Prometheus rules](https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/) to provide easy to operate end-to-end Kubernetes cluster monitoring with [Prometheus](https://prometheus.io/) using the Prometheus Operator.



Components included in this package:

* The [Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator)
* Highly available [Prometheus](https://prometheus.io/)
* Highly available [Alertmanager](https://github.com/prometheus/alertmanager)
* [Prometheus node-exporter](https://github.com/prometheus/node_exporter)
* [Prometheus Adapter for Kubernetes Metrics APIs](https://github.com/kubernetes-sigs/prometheus-adapter)
* [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics)
* [Grafana](https://grafana.com/)


## Prerequisites

You will need a Kubernetes cluster, that's it! By default it is assumed, that the kubelet uses token authentication and authorization, as otherwise Prometheus needs a client certificate, which gives it full access to the kubelet, rather than just the metrics. Token authentication and authorization allows more fine grained and easier access control.

This means the kubelet configuration must contain these flags:

* `--authentication-token-webhook=true` This flag enables, that a `ServiceAccount` token can be used to authenticate against the kubelet(s). This can also be enabled by setting the kubelet configuration value `authentication.webhook.enabled` to `true`.
* `--authorization-mode=Webhook` This flag enables, that the kubelet will perform an RBAC request with the API to determine, whether the requesting entity (Prometheus in this case) is allowed to access a resource, in specific for this project the `/metrics` endpoint. This can also be enabled by setting the kubelet configuration value `authorization.mode` to `Webhook`.

This stack provides [resource metrics](https://github.com/kubernetes/metrics#resource-metrics-api) by deploying
the [Prometheus Adapter](https://github.com/kubernetes-sigs/prometheus-adapter).
This adapter is an Extension API Server and Kubernetes needs to be have this feature enabled, otherwise the adapter has
no effect, but is still deployed.


## Quickstart

> Note: This manifests is compatible with Kubernetes version v1.21.z and upper

* Create the monitoring stack using the config in the `manifests` directory:

```shell
# Create the namespace and CRDs, and then wait for them to be available before creating the remaining resources
kubectl apply --server-side -f manifests/setup
until kubectl get servicemonitors --all-namespaces ; do date; sleep 1; echo ""; done
kubectl apply -f manifests/
```


* And to teardown the stack:

```shell
kubectl delete --ignore-not-found=true -f manifests/ -f manifests/setup
```

