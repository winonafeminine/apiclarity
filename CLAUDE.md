# CLAUDE.md

## Project Overview

APIClarity — reconstructs OpenAPI Specifications from real-time Kubernetes workload traffic. Consists of a Go backend, React UI, and a Helm chart for deployment.

## Repository Structure

```
backend/          Go backend service
ui/               React frontend
charts/apiclarity/ Helm chart for Kubernetes deployment
  templates/      Kubernetes manifest templates
  Chart.yaml      Chart metadata and version
  values.yaml     Default Helm values
plugins/          Traffic source plugins (Kong, Tyk, etc.)
wasm-filters/     Envoy WASM filters
```

## Important Rules

### Helm Chart Versioning

**Every time any file under `charts/apiclarity/` is modified, the `version` field in `charts/apiclarity/Chart.yaml` must be bumped.**

Follow [Semantic Versioning](https://semver.org/):
- `patch` (e.g. `0.1.0` → `0.1.1`) — bug fixes, template tweaks, value adjustments
- `minor` (e.g. `0.1.0` → `0.2.0`) — new features, new configurable values, new templates
- `major` (e.g. `0.1.0` → `1.0.0`) — breaking changes to the chart API

Example:
```yaml
# charts/apiclarity/Chart.yaml
version: 0.1.1  # bumped from 0.1.0 after modifying postgresql-statefulset.yaml
```

## Common Tasks

### Build

```bash
make ui          # Build React UI
make backend     # Build Go backend
make docker      # Build Docker image
```

### Helm Chart

```bash
# Lint the chart
helm lint charts/apiclarity

# Render templates locally for inspection
helm template apiclarity charts/apiclarity --values values-custom.yaml

# Deploy / upgrade
helm upgrade --install apiclarity charts/apiclarity \
  --namespace apiclarity \
  --create-namespace \
  --values values-custom.yaml \
  --wait --timeout 5m
```

### PostgreSQL PVC Reset (when re-deploying from scratch)

```bash
kubectl delete statefulset -n apiclarity apiclarity-apiclarity-postgresql
kubectl delete pvc -n apiclarity --all
```

## Branch Convention

Feature branches follow the pattern `claude/<description>-<session-id>`.
