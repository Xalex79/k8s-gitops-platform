# Plan

Goal: learn GitHub Actions, Argo CD / GitOps, Go and Jenkins by building one realistic delivery
flow end to end, and be able to explain every decision in an interview.

## Background

Experience I already have and want to build on:
- Kubernetes and Helm: observability stack (OpenTelemetry Collector, Fluent Bit, eBPF/Pixie),
  troubleshooting OOMKilled pods and failing Helm upgrades.
- GitLab CI: pipeline designed from scratch (GitFlow, Checkstyle, JaCoCo, Trivy with SARIF,
  semantic-release, Docker images).
- Terraform, Python, Bash, PowerShell.

New in this project: GitHub Actions, Argo CD, GitOps, Go, Jenkins.

## Steps

1. **Go service** (`app/`): `net/http` API with `/healthz`, `/version` (build info injected with
   `-ldflags`) and Prometheus metrics on `/metrics`. Unit tests with `go test`. Multi-stage Dockerfile
   producing a small non-root image.
2. **GitHub Actions** (`.github/workflows/`): on pull requests run `go vet`, `golangci-lint` and tests;
   on `main` build the image, scan it with Trivy (fail on HIGH/CRITICAL, upload SARIF to code
   scanning) and push it to GHCR tagged with the commit SHA.
3. **Helm + GitOps** (`chart/`, `gitops/`): chart with Deployment, Service, probes and resource limits;
   per-environment values. The CI opens a pull request that bumps the image tag in `gitops/`, so every
   deployment is a reviewed commit.
4. **Argo CD**: local cluster with kind or k3d; Argo CD installed with Helm; an `Application` pointing
   at `gitops/` with automated sync, prune and self-heal. Exercises: change a replica count by hand and
   watch self-heal undo it; roll back with `git revert`.
5. **Environments**: `dev` and `prod` from one ApplicationSet; promotion = PR from dev values to prod.
6. **Jenkins**: Jenkins in a container or in the cluster, declarative `Jenkinsfile` doing the same as
   step 2. Write `docs/CI_COMPARISON.md` (GitLab CI vs GitHub Actions vs Jenkins).
7. **Go + Kubernetes API**: CLI with `client-go` that lists pods with many restarts and explains the
   last termination reason.

Optional later: Crossplane (provider-kubernetes), sealed secrets / external secrets, policy with
Kyverno, an agentic helper that drafts the GitOps PR description from the diff.

## Definition of done for each step

- Works from a clean clone with the commands in the README.
- One entry in `docs/LOG.md`: what I did, what broke, what I learned.
- README status table updated.
