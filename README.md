# Cloud Platform Monitoring Alerts

## About

Cloud Platform Monitoring Alerts is a lightweight service for providing in-cluster readonly access to our Prometheus and Alertmanager alerts APIs.

It houses a simple nginx configuration for routing requests to the following Cloud Platform monitoring endpoints:

```
http://prometheus-operated.monitoring.svc.cluster.local:9090/api/v1/alerts
```

```
http://alertmanager-operated.monitoring.svc.cluster.local:9093/api/v2/alerts
```

## How to access

Raise a PR against your Cloud Platform environment, adding the following to your `namespace` `labels`:

```
component: monitoring-alerts-client
```

Following merge and apply, you'll be able to hit the alerts endpoints from your namespace workloads with a `curl` to either:

```
monitoring-alerts-service.cloud-platform-monitoring-alerts:8080/alertmanager
monitoring-alerts-service.cloud-platform-monitoring-alerts:8080/prometheus
```
