# Project Helena Helm Charts

This repository hosts Helm charts for deploying the Project Helena ecosystem. It currently includes:

- `charts/recon`: renders the web dashboard that aggregates agent data.
- `charts/recon-agent`: publishes metrics from Kubernetes clusters to the dashboard.
- `charts/warden`: deploys the Warden policy engine.

## Getting Started

1. Inspect chart values to understand defaults:
   ```sh
   helm show values charts/recon
   ```
2. Validate templates locally:
   ```sh
   helm lint charts/recon
   helm template charts/recon
   ```
3. Install into a test namespace:
   ```sh
   helm install recon charts/recon --namespace recon --create-namespace
   ```

Refer to `AGENTS.md` for contribution guidelines, release expectations, and security notes.
