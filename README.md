# k8s-gitops-platform

A small Go service taken from commit to a Kubernetes cluster the way a platform team would do it:
CI with **GitHub Actions**, container scanning with **Trivy**, packaging with **Helm** and continuous
delivery with **Argo CD** (GitOps). Later, the same pipeline in **Jenkins** for comparison.

> Learning project, built in public. Progress and decisions are logged in [docs/LOG.md](docs/LOG.md).

## Flow (target)

```
git push ──► GitHub Actions ──► test + lint ──► build image ──► Trivy scan ──► push to GHCR
                                                                                   │
                                     PR: bump image tag in gitops/ (Helm values) ◄─┘
                                                     │ merge
                                                     ▼
                                Argo CD (in cluster) ──► sync ──► Kubernetes (kind / k3d)
```

Git is the source of truth: the cluster only changes when `gitops/` changes, and a `git revert` is a rollback.

## Status

| Step | What | Status |
|---|---|---|
| 1 | Go API (`/healthz`, `/version`, `/metrics`), tests, multi-stage Dockerfile | ⬜ |
| 2 | GitHub Actions: test + lint on PRs; build, Trivy scan and push to GHCR on `main` | ⬜ |
| 3 | Helm chart + `gitops/` values; CI opens a PR to bump the image tag | ⬜ |
| 4 | Argo CD on a local cluster: automated sync, self-heal, rollback with `git revert` | ⬜ |
| 5 | Two environments (dev / prod) with an ApplicationSet | ⬜ |
| 6 | Same pipeline as a declarative `Jenkinsfile`; comparison with GitHub Actions and GitLab CI | ⬜ |
| 7 | Go tool with `client-go`: explain pods that keep restarting (OOMKilled, CrashLoopBackOff) | ⬜ |

## Repository layout (target)

```
app/            Go service, tests, Dockerfile
chart/          Helm chart for the service
gitops/         Desired state per environment (values, Argo CD Applications)
.github/        GitHub Actions workflows
jenkins/        Jenkinsfile (step 6)
docs/           Plan, log, decisions, comparison of CI tools
```

## Run it locally

Instructions will be added as each step is finished.
