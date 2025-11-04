# QuakeWatch - Earthquake Monitoring Application

DevOps 0701 Project

QuakeWatch is a Flask app that monitors earthquake data. It's deployed on Kubernetes with:
- ArgoCD for GitOps deployment
- Prometheus for monitoring
- Grafana for visualization

## Tech Stack

- **App**: Flask earthquake dashboard
- **Image**: `razi94/quakewatch:v1.1.1` on Docker Hub
- **Cluster**: k3s
- **Deployment**: ArgoCD
- **Monitoring**: Prometheus + Grafana

## Repo Structure
```
.
├── README.md                          # Main README with installation instructions and screenshots
├── infrastructure/
│   ├── argocd/
│   │   ├── install.sh
│   │   └── quakewatch-application.yaml
│   ├── grafana/
│   │   └── quakewatch-dashboard.json  
│   ├── install-monitoring.sh          # Install Grafana & Prometheus
│   └── prometheus/
│       └── alerts.yaml
├── phase-1/                           # Docker setup
│   └── ...
├── phase-2/                           # K8s manifests  
│   └── ...
├── phase-3/                           # Initial Helm
│   └── ...
└── quakewatch-chart/                  # Final Helm chart
    ├── Chart.yaml
    ├── templates/
    │   ├── deployment.yaml
    │   ├── servicemonitor.yaml
    │   └── service.yaml
    └── values.yaml
```

## Setup

### 1. Install k3s
```bash
curl -sfL https://get.k3s.io | sh -
```

### 2. Install ArgoCD
```bash
cd infrastructure/argocd
chmod +x install.sh && ./install.sh
```

Get ArgoCD admin password:
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Access UI at `https://<ARGOCD_POD_IP>:8080` (user: admin)

### 3. Deploy App using ArgoCD
```bash
kubectl apply -f infrastructure/argocd/quakewatch-application.yaml
```

### 4. Install Monitoring (Grafana & Prometheus)
```bash
cd infrastructure/monitoring
chmod +x install.sh && ./install.sh
kubectl apply -f alerts.yaml
```

Get Grafana password:
```bash
kubectl get secret --namespace monitoring prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 -d
```

Access at `http://<GRAFANA_POD_IP>:3000` (user: admin)

Import dashboard: Dashboards → Import → upload `quakewatch-dashboard.json`

## GitOps

Any changes pushed to Git are automatically deployed by ArgoCD with:
- **Auto-sync**: Changes are detected and applied automatically
- **Self-heal**: Manual changes are reverted
- **Prune**: Deleted resources removed


## Monitoring

### Prometheus Alerts
The following alerts are configured for QuakeWatch:

- **QuakeWatchHighCPU**: Fires when pod CPU usage > 80% (0.8 cores) for 2 minutes
  - Severity: Warning
  - Monitors actual CPU core usage per pod

- **QuakeWatchPodDown**: Fires when any pod is not running for 30 seconds
  - Severity: Critical  
  - Tracks pods in Pending/Unknown/Failed states

**View alerts:**
```bash
# Prometheus UI
kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090

# Then navigate to http://localhost:9090/alerts
```

**Deploy alerts:**
```bash
kubectl apply -f infrastructure/prometheus/alerts.yaml
```
### Dashboard

  Panels tracking: 
- Node and pod CPU usage
- Pod memory usage
- Pod health
- Pod count

## Access App address
```bash
kubectl get svc quakewatch -n quakewatch-helm
```

Open `http://localhost:<NODEPORT>`

## Screenshots
<img width="1749" height="579" alt="image" src="https://github.com/user-attachments/assets/411ccbb1-c96f-4088-829c-d7632756cb15" />
<img width="1865" height="754" alt="image" src="https://github.com/user-attachments/assets/10fa3c57-5094-43bc-a406-69b33dde81ea" />
<img width="1879" height="630" alt="image" src="https://github.com/user-attachments/assets/e23d2693-c8a3-4c44-9d0c-c87659802e68" />

