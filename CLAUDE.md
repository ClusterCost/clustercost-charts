# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Helm charts for deploying the Project Helena ecosystem — a Kubernetes cost visibility and FinOps platform. Three charts live under `charts/`:

- **recon**: Web dashboard that aggregates cost metrics from distributed agents. Includes optional VictoriaMetrics StatefulSet for time-series storage.
- **recon-agent**: DaemonSet agent that collects cluster cost data via eBPF and reports to the dashboard over gRPC.
- **warden**: Policy engine for enforcing cost and operational policies.

## Commands

```bash
helm lint charts/<chart-name>                          # Validate chart (run before every PR)
helm template charts/<chart-name>                      # Render manifests locally
helm template charts/<chart-name> | kubectl apply --dry-run=client -f -  # Smoke test
helm install <release> charts/<chart-name> --namespace <ns> --create-namespace
```

No npm, Makefile, or test framework — validation is `helm lint` + `helm template` only.

## Architecture

```
Agent (DaemonSet, per cluster) --gRPC:9091--> Recon (Deployment) --> VictoriaMetrics (StatefulSet)
```

- **Recon** exposes HTTP (:9090) and gRPC (:9091). Config is rendered into a Secret with a checksum annotation to trigger rollouts on change.
- **Agent** runs privileged (`hostNetwork`, `hostPID`, eBPF caps: `SYS_ADMIN`, `BPF`, `PERFMON`). Configured entirely via environment variables — no config files.
- **VictoriaMetrics** is an optional sub-component of the recon chart with configurable PVC storage (default 10Gi).
- **Warden** runs as a standard Deployment with a single HTTP port (:8080).

## Conventions

- YAML: 2-space indentation, lowercase keys
- Helm helpers in `_helpers.tpl`; parameterize values with sane defaults; no hardcoded namespaces
- All charts include `values.schema.json` (JSON Schema Draft 7) for validation
- Commits: conventional imperative style (`feat: add dashboard values`, `fix: correct agent RBAC`)
- Default images from `ghcr.io/projecthelena/...`

## CI/CD

GitHub Actions (`.github/workflows/build-and-deploy.yaml`):
1. **Build** (all PRs + main): `helm lint` → `helm template` → `helm package` → generate repo index
2. **Deploy** (main only): publish to Cloudflare Pages at `charts.projecthelena.com`
