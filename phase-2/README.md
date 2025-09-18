# Hello World Flask App (Phase 2 of the project — Kubernetes)

This phase deploys the Dockerized Flask app to a Kubernetes cluster with yaml files for the deployment, service, Configmap, Secret, HPA, CronJob and readiness/liveness probes.

## Files Included in the .zip file

- flask-deployment.yaml – Deployment with 2 replicas, calls Configmap, Secrets and readiness/liveness probes.
- flask-svc.yaml – Kubernetes Service (NodePort) — exposes the app on a high node port and forwards to container at :5000
- flask-hpa.yaml – Horizontal Pod Autoscaler - Min 2 replicas, max 6 replicas triggered by 70% CPU usage.
- flask-config.yaml – ConfigMap adds 2 environment variables.
- flask-secret.yaml – Adds a Secret env variable
- flask-cron.yaml – CronJob (prints a timestamped test line every minute between 8AM to 5PM from Sunday to Friday)

## Prerequisites

- k3s (or any Kubernetes) running and `kubectl` configured
- Docker image: `razi94/hello-world-flask:latest`
- A metrics-server(Installed with k3s by default) - used by HPA for its CPU metrics.

## How to Deploy on Kubernetes

1. (Optional) Create and use a new namespace for the project:

   ```bash
   kubectl create namespace flask-project
   kubectl config set-context --current --namespace=flask-project
   ```
2. Apply config and secret:

   ```bash
   kubectl apply -f flask-config.yaml
   kubectl apply -f flask-secret.yaml
   ```
3. Deploy the app:

   ```bash
   kubectl apply -f flask-deployment.yaml
   kubectl rollout status deploy/hello-flask
   kubectl get pods
   ```
4. Expose the app (NodePort Service):

   ```bash
   kubectl apply -f flask-svc.yaml
   kubectl get svc hello-flask
   ```

   Access from your host using the node’s IP and the assigned NodePort (see the `PORT(S)` column):

   ```
   http://<NODE_IP>:<NODEPORT>
   ```
   For example: `curl 192.168.122.1:32687`
5. Enable autoscaling (HPA):

   ```bash
   kubectl apply -f flask-hpa.yaml
   kubectl get hpa
   ```
6. Run the CronJob:

   ```bash
   kubectl apply -f flask-cron.yaml
   kubectl get cronjobs
   kubectl get jobs
   # latest run logs:
   kubectl logs job/$(kubectl get jobs --sort-by=.metadata.creationTimestamp -o jsonpath='{.items[-1].metadata.name}')
   ```

## Notes

- The Deployment reads env vars from `flask-config` (ConfigMap) and `flask-secret` (Secret).
- Health probes are already configured in `flask-deployment.yaml`.
- The Service is type **NodePort** so you can reach the app from the host (VM IP + NodePort).
- feature/readme branch changes.

## Cleanup

```bash
kubectl delete -f flask-cron.yaml
kubectl delete -f flask-hpa.yaml
kubectl delete -f flask-svc.yaml
kubectl delete -f flask-deployment.yaml
kubectl delete -f flask-secret.yaml
kubectl delete -f flask-config.yaml
# optional:
# kubectl delete namespace flask-project
```
