# Enterprise-observability-stack

Self-service observability stack for monitoring Confluent Cloud Kafka, deployed on Amazon EKS and fully automated using Kubernetes manifests and GitOps via ArgoCD.

This repository contains all the manifests required to deploy:

- Prometheus (scraping Confluent Cloud metrics through Metrics API)

- Alertmanager (delivering alerts to Slack)

- PrometheusRule files (recording + alerting rules)

- Grafana dashboards (as ConfigMaps)

- Scrape configuration


## Purpose

This stack allows you to:

- Scrape Confluent Cloud Kafka metrics

- Record custom metrics for better dashboard aggregation

- Trigger alerts on Kafka throughput, consumer lag, and availability

- Visualize everything in Grafana

- Automate deployment through ArgoCD with GitOps


## Prerequisites

Before deploying this stack, ensure you have:

- A running EKS cluster

- kubectl configured to connect to the cluster

- ArgoCD installed in the cluster

- A Slack Webhook URL for receiving alerts

- Confluent Metrics API credentials (MetricsViewer SA)



## Setup
1. Clone the repository
```bash
git clone https://github.com/<your-org>/enterprise-observability-stack.git
cd enterprise-observability-stack

```

2. Update the secret API key pair with the confluent metrics API credentials


3. Create your Slack secret
```bash
kubectl create secret generic alertmanager-slack-webhook \
  --from-literal=SLACK_WEBHOOK_URL="https://hooks.slack.com/services/XXX/YYY/ZZZ" \
  -n monitoring

```

4. Apply the manifests
- If deploying manually
```bash
kubectl apply -f prometheus/
kubectl apply -f grafana/
kubectl apply -f alertmanager/

```

- If deploying through ArgoCD
```bash
kubectl apply -f argocd/application.yaml

``` 
ArgoCD will sync the rest automatically.


5. Grafana Dashboard
The dashboard is already packaged as a ConfigMap:
```bash
grafana/dashboards/confluent-kafka-dashboard-cm-.yaml

```

Once Grafana is deployed, port-forward to access it locally:
```bash
kubectl port-forward svc/grafana 3000:3000 -n monitoring

```

