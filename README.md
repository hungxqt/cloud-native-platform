# Cloud-Native Platform (Phase 3) — CV Portfolio Bundle

This folder packages three delivery repositories from the **TF2 Phase 3** capstone for inclusion in a personal CV or portfolio (`mycv`). Together they demonstrate end-to-end ownership of a production-style cloud-native platform: application code, Kubernetes delivery, and AWS infrastructure.

| Repository | Role | Upstream (reference) |
|---|---|---|
| [`platform`](./platform/) | Polyglot microservices, AI reviews, CI/CD → ECR | [tf2-team/tf2-corp-platform](https://github.com/tf2-team/tf2-corp-platform) |
| [`chart`](./chart/) | Helm chart, GitOps (Argo CD), runtime security & ops | [tf2-team/tf2-corp-chart](https://github.com/tf2-team/tf2-corp-chart) |
| [`infra`](./infra/) | Terraform / AWS (VPC, EKS, ECR, OIDC, cost, secrets) | [tf2-team/tf2-corp-infra](https://github.com/tf2-team/tf2-corp-infra) |

These copies were cloned from local `phase3/` workspaces (same commit history as `main` at clone time). Folders are renamed for portfolio clarity; remotes still point at the GitHub origins above for reference.

---

## What this stack shows on a CV

- **Full-stack platform engineering** — not a single service demo, but a multi-repo production layout (app → image → chart → cluster → cloud).
- **Cloud & Kubernetes** — EKS, Helm, Argo CD GitOps, ALB/CloudFront patterns, Karpenter / autoscaling, External Secrets, Gatekeeper / policy.
- **Secure delivery** — restricted pod security, network policy, runtime hardening, OIDC-based GitHub Actions (no long-lived cloud keys), image promote via chart PRs.
- **FinOps & reliability** — budgets, cost anomaly detection, SLO-aware operations, maintenance without downtime, rollout safety.
- **AI on the platform** — product-review / LLM path plus AIOps-oriented observability (metrics, logs, traces, dashboards).

Use this bundle as **evidence code** behind resume bullets (architecture, security, GitOps, Terraform, multi-service CI/CD). Prefer linking to public forks or sanitized highlights if upstream repos are private.

---

## Repository summaries

### 1. `platform` — application & CI/CD

Internal microservice platform for an online store: polyglot, Kubernetes-native services (storefront, product, cart, checkout, payment, async messaging), AI product-review summarization, and a full observability stack.

**Highlights**

- `src/` microservices + AI review service
- Local run via Docker Compose / Make
- GitHub Actions: multi-arch build, push to AWS ECR (OIDC), chart promote (dev direct-push / prod PR)
- Proto contracts and image bake definitions (`docker-bake.hcl`)

**Entry points:** [README](./platform/README.md) · [docs/CICD.md](./platform/docs/CICD.md) · [docs/DEPLOYMENT.md](./platform/docs/DEPLOYMENT.md)

### 2. `chart` — Helm + GitOps delivery

Helm chart that deploys the platform on Kubernetes: app workloads, AI/LLM components, observability (OpenTelemetry, Prometheus, Grafana, Jaeger, etc.), plus GitOps bootstrap for Argo CD.

**Highlights**

- Env overlays: `values-dev.yaml`, `values-prod.yaml`, public ALB overlay
- `gitops/` App-of-Apps style cluster manifests
- Runtime hardening (PSS-restricted, Gatekeeper, admission tests)
- Network policy, External Secrets chart, maintenance / HPA / probe ops docs

**Entry points:** [README](./chart/README.md) · [docs/DEPLOYMENT.md](./chart/docs/DEPLOYMENT.md) · [docs/operations/gitops-argocd.md](./chart/docs/operations/gitops-argocd.md)

### 3. `infra` — AWS infrastructure as code

Terraform for bootstrap and environment stacks: remote state, GitHub OIDC roles, VPC, EKS, nested ECR, Load Balancer Controller IAM, secrets integration, Client VPN, cost budgets / anomaly detection, and related modules.

**Highlights**

- Bootstrap: S3 state, OIDC, platform ECR push roles, Terraform plan/apply roles
- Environments: development & production
- CI/CD for Terraform (plan on PR, promote / drift workflows)
- Modules for Karpenter, Argo CD, External Secrets, RDS/MSK patterns, cost controls

**Entry points:** [docs/DEPLOYMENT.md](./infra/docs/DEPLOYMENT.md) · [docs/SETUP.md](./infra/docs/SETUP.md) · [docs/CI_CD_GUIDE.md](./infra/docs/CI_CD_GUIDE.md)

---

## How the three repos fit together

```text
  Developer / CI
        │
        ▼
 platform   ── build & push images ──►  AWS ECR
        │                                    │
        │  (chart promote: image tags)       │
        ▼                                    │
 chart      ── Argo CD / Helm ───────────────┼──►  EKS workloads
                                             │
 infra      ── Terraform ────────────────────┘
            VPC · EKS · ECR · IAM · OIDC · cost · secrets
```

| Concern | Owned by |
|---|---|
| Application code & container images | `platform` |
| Desired cluster state (Helm values, GitOps) | `chart` |
| Cloud foundation & cluster control plane | `infra` |

---

## Layout

```text
cv-repo/
├── README.md     ← this file
├── platform/     ← app source + CI
├── chart/        ← Helm + GitOps
└── infra/        ← Terraform / AWS
```

Each subdirectory is an independent Git repository with its own history.

---

## Suggested CV / portfolio usage

1. **Resume bullets** — map claims to concrete paths (e.g. “GitOps with Argo CD” → `chart/gitops/`; “Terraform EKS + OIDC CI” → `infra/modules/`).
2. **Case study** — describe problem → architecture → security/cost constraints → evidence in `docs/changes/` and `docs/adr/` inside each repo.
3. **Demo notes** — local platform: Compose in `platform`; cluster path requires `infra` + `chart` (do not apply cloud resources from a CV machine without an isolated AWS account).
4. **Privacy** — strip or avoid publishing secrets, real account IDs, VPN PKI, `*.tfstate`, client keys, or production URLs if you publish this tree publicly.

---

## Skills & technologies (at a glance)

| Area | Examples in this bundle |
|---|---|
| Languages / services | Polyglot microservices (Go, Python, PHP, Elixir, …), gRPC/protobuf |
| Containers | Docker, multi-arch bake, ECR |
| Kubernetes | EKS, Helm, HPA, PDB, NetworkPolicy, PSS / Gatekeeper |
| GitOps | Argo CD, app-of-apps, env overlays |
| CI/CD | GitHub Actions, OIDC to AWS, chart promote PR flow |
| IaC | Terraform modules, bootstrap remote state |
| Observability | OTel collector, Prometheus, Grafana, Jaeger |
| Security | IRSA, External Secrets / ASM, runtime hardening, policy-as-code |
| Cost | Budgets, Cost Anomaly Detection, right-sizing docs |

---

## Credits

Initial codebase contributors are listed in **[CREDITS.md](./CREDITS.md)** (kai-techx, toanletechx, namhongta, khanhnn1412).

## Source note

Cloned from local Phase 3 workspaces under `phase3/` for portfolio packaging. History matches the `main` branch at clone time. Directory names in this bundle are portfolio-friendly (`platform`, `chart`, `infra`); upstream GitHub remotes keep their original repository names for reference only.
