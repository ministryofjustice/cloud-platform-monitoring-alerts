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

## How it works

Cloud Platform user access to the alerts endpoints is managed via 'chained' `NetworkPolicy` configurations.

The `cloud-platform-monitoring-alerts` namespace is granted locked-down pod access to Prometheus and Alertmanager endpoints in `monitoring` namespace, via this [NetworkPolicy resource](https://github.com/ministryofjustice/cloud-platform-terraform-monitoring/blob/main/main.tf#L162) and it's [associated namespace label](https://github.com/ministryofjustice/cloud-platform-environments/blob/main/namespaces/live.cloud-platform.service.justice.gov.uk/cloud-platform-monitoring-alerts/00-namespace.yaml#L9).

User namespace access to the service itself is controlled via [this NetworkPolicy](https://github.com/ministryofjustice/cloud-platform-environments/blob/main/namespaces/live.cloud-platform.service.justice.gov.uk/cloud-platform-monitoring-alerts/04-networkpolicy.yaml#L29).

## Healthchecks (livenessProbe config)

This service has a custom livenessProbe in the form of a bash script that periodically checks the health of the Prometheus and Alertmanager endpoints. Its not possible to define multiple endpoint checks for livenessProbe config so this gets around that issue by signalling a failure if either of the endpoints become unreachable.

Note that logs from the livenessProbe are not available in the pod logs. If you need to debug the healthcheck script, you'll want to exec into the pod and run the script manually.

## How to access

Raise a PR against your Cloud Platform environment, adding the following to your `namespace` `labels`:

```
component: monitoring-alerts-client
```

Following merge and apply, you'll be able to make GET requests to the Prometheus `api/v1/` and Alertmanager `api/v2/` endpoints from your namespace workloads with a `curl` to either:

```
monitoring-alerts-service.cloud-platform-monitoring-alerts:8080/prometheus
monitoring-alerts-service.cloud-platform-monitoring-alerts:8080/alertmanager
```
