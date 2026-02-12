# Warden Chart

Helm chart for deploying the Warden policy engine as part of the Project Helena ecosystem.

## Installation

```sh
helm install warden charts/warden --namespace warden --create-namespace
```

Use `-f my-values.yaml` or `--set key=value` to override defaults.

## Key Values

| Value | Description | Default |
| --- | --- | --- |
| `replicaCount` | Number of warden pods | `1` |
| `image.repository` / `image.tag` | Container image reference | `ghcr.io/projecthelena/warden:latest` |
| `service.type` | Kubernetes Service type | `ClusterIP` |
| `service.port` | Service port | `8080` |
| `commonLabels` | Extra labels applied to all resources | `{}` |
| `env` | Environment variables for the container | `{}` |
| `resources` | Pod resource requests/limits | See `values.yaml` |

See `values.yaml` for the full list.

## Validation

Run the following before opening a PR:

```sh
helm lint charts/warden
helm template charts/warden | kubectl apply --dry-run=client -f -
```
