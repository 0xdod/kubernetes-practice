# 📊 Prometheus Monitoring on Kubernetes

This document explains how to set up and extend Prometheus-based monitoring in a Kubernetes cluster using the **Prometheus Operator** (via the `kube-prometheus-stack` Helm chart).

---

## 🚀 Why Prometheus Operator?
- Simplifies running Prometheus in Kubernetes.
- Provides **Custom Resource Definitions (CRDs)** like:
  - `ServiceMonitor` → define scrape targets for your apps.
  - `PodMonitor` → scrape metrics from specific pods.
  - `PrometheusRule` → define alerting rules.
- Bundled with:
  - Prometheus
  - Alertmanager
  - Node Exporter
  - kube-state-metrics
  - Grafana dashboards

Installed via Helm:
```sh
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring --create-namespace
```

---

## 📡 Exposing Your App for Monitoring
1. **Expose metrics in your app** (e.g., `/metrics` endpoint).
2. **Create a Kubernetes Service** for the app.
3. **Add a ServiceMonitor** so Prometheus can scrape metrics.

Example `ServiceMonitor`:
```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: myapp-monitor
  labels:
    release: monitoring   # match Prometheus Helm release
spec:
  selector:
    matchLabels:
      app: myapp
  endpoints:
  - port: http
    path: /metrics
    interval: 30s
```

---

## ⚠️ Alerting with Prometheus
Prometheus alerts are defined in **PrometheusRule** CRDs. Alerts are evaluated and then forwarded to **Alertmanager** for routing.

### Example PrometheusRule
```yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: myapp-rules
  labels:
    release: monitoring
spec:
  groups:
  - name: myapp.rules
    rules:
    - alert: HighErrorRate
      expr: rate(http_requests_total{app="myapp",status=~"5.."}[5m]) > 5
      for: 1m
      labels:
        severity: critical
      annotations:
        summary: "High error rate in myapp"
        description: "More than 5 errors/sec for the last 1m in myapp service."
```

---

## 📬 Alert Routing with Alertmanager
Alertmanager decides **where alerts go** (Slack, email, PagerDuty, etc.).

Example config:
```yaml
global:
  resolve_timeout: 5m

route:
  receiver: slack-notifications

receivers:
- name: slack-notifications
  slack_configs:
  - api_url: https://hooks.slack.com/services/XXX/YYY/ZZZ
    channel: '#alerts'
    send_resolved: true
```

---

## 🛠️ Development Tips
- Use `kubectl port-forward` to access ClusterIP services locally:
  ```sh
  kubectl port-forward svc/myapp 8080:3000 -n default
  ```
- Check active alerts in the Prometheus UI → **Alerts** tab.
- Explore metrics and dashboards in Grafana (bundled in the stack).

---

## ✅ Summary
- **Helm** → package manager for Kubernetes, used to deploy Prometheus stack.  
- **Operator** → extends Kubernetes with CRDs to manage apps like Prometheus.  
- **ServiceMonitor** → tells Prometheus which services to scrape.  
- **PrometheusRule** → defines when alerts should fire.  
- **Alertmanager** → routes alerts to external systems (Slack, email, etc.).  

With kube-prometheus-stack, you get **out-of-the-box Kubernetes monitoring**, plus the ability to add **custom app metrics and alerts**.

