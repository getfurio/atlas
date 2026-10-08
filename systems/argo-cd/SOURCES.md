# Argo CD: sources

Unofficial map, reconstructed from public sources. Not affiliated with the Argo project or the
CNCF.

- Upstream: https://github.com/argoproj/argo-cd
- Release: `v3.5.4` (commit `d6d5b248ce00e1a2c512068002a93d3319767087`, published 2026-10-06)
- Read on: 2026-10-08

## Scope

The standard install, `manifests/install.yaml`, built from `manifests/base`: seven workloads
(API server, repository server, application controller, ApplicationSet controller,
notifications controller, Dex, Redis) and the four things outside the cluster they talk to.

Left out: the HA variant (`manifests/ha`, Redis with Sentinel and HAProxy), the hydrator
variants with the commit server, the Helm chart (`argo-helm`), and the CMP sidecars.

## Files read

| What | File |
| --- | --- |
| Workloads of the install | `manifests/install.yaml`, `manifests/base/kustomization.yaml` |
| Who may call whom | `manifests/base/*/argocd-*-network-policy.yaml` (ingress rules name the callers) |
| Deployments and ports | `manifests/base/*/argocd-*-deployment.yaml`, `application-controller/argocd-application-controller-statefulset.yaml` |
| What each component does and what it reaches outside | `docs/operator-manual/architecture.md` |

## Choices

- The relations between components come from the network policies: a policy that lets the API
  server into the repository server is a `server → repo-server` relation. The relations to the
  outside (Kubernetes API, Git, identity provider, notification services) come from the
  architecture page, not from code.
- The Kubernetes API is one component for both the cluster Argo CD runs in and the clusters it
  deploys to.
- The commit server exists in the base manifests but is not in `install.yaml`: it is left out.
