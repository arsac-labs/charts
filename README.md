# arsac-labs/charts

Helm chart wrappers for Kubernetes manifests that don't have official Helm charts.

## Charts

| Chart | Description | Upstream |
|-------|-------------|----------|
| [agent-sandbox](./charts/agent-sandbox) | Kubernetes sandbox controller for AI agents | [kubernetes-sigs/agent-sandbox](https://github.com/kubernetes-sigs/agent-sandbox) |

## Usage

Charts are published to GitHub Container Registry (GHCR) as OCI artifacts.

### With Flux

```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: OCIRepository
metadata:
  name: agent-sandbox
spec:
  interval: 5m
  layerSelector:
    mediaType: application/vnd.cncf.helm.chart.content.v1.tar+gzip
    operation: copy
  ref:
    tag: "0.1.0"
  url: oci://ghcr.io/arsac-labs/charts/agent-sandbox
---
apiVersion: helm.toolkit.fluxcd.io/v2
kind: HelmRelease
metadata:
  name: agent-sandbox
spec:
  interval: 1h
  chartRef:
    kind: OCIRepository
    name: agent-sandbox
  values:
    extensions:
      enabled: true  # Enable SandboxTemplate, SandboxClaim, SandboxWarmPool
```

### With Helm CLI

```bash
# Core only
helm install agent-sandbox oci://ghcr.io/arsac-labs/charts/agent-sandbox --version 0.1.0

# With extensions
helm install agent-sandbox oci://ghcr.io/arsac-labs/charts/agent-sandbox --version 0.1.0 --set extensions.enabled=true
```

## Configuration

### agent-sandbox

| Parameter | Description | Default |
|-----------|-------------|---------|
| `extensions.enabled` | Enable extensions (SandboxTemplate, SandboxClaim, SandboxWarmPool) | `false` |

#### Extensions

When `extensions.enabled: true`, the following additional CRDs and controllers are installed:

| Resource | Purpose |
|----------|---------|
| **SandboxTemplate** | Reusable templates for creating Sandboxes |
| **SandboxClaim** | Create Sandboxes from templates (simplified UX) |
| **SandboxWarmPool** | Pre-warmed pool for faster Sandbox allocation |

## Updating Charts

Charts can be updated via:

1. **Manual workflow dispatch** - Trigger the update workflow with the new version
2. **Renovate** - Configure renovate to watch upstream releases and trigger updates

## License

Apache 2.0
