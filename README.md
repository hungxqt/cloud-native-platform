# Cloud-Native Platform

A production-style cloud-native stack for an online store: polyglot microservices, Kubernetes delivery with Helm and Argo CD, and AWS infrastructure managed with Terraform.

The repository is organized as three cooperating trees that cover the full path from application code to cluster and cloud foundation.

| Directory | Role |
|---|---|
| [`platform`](./platform/) | Application services, AI reviews, local compose, and image CI/CD to ECR |
| [`chart`](./chart/) | Helm chart, GitOps (Argo CD), runtime security, and operations docs |
| [`infra`](./infra/) | Terraform / AWS (VPC, EKS, ECR, OIDC, secrets, cost controls) |

---

## What this project includes

- **Polyglot microservices** — storefront, product catalog, cart, checkout, payment, shipping, recommendations, and related services, with gRPC/protobuf contracts
- **AI on the platform** — product-review / LLM path plus observability suited for AIOps-style operations
- **Kubernetes delivery** — Helm values per environment, Argo CD GitOps (app-of-apps), HPA, PDB, probes, and rollout safety
- **Security** — restricted pod security, network policy, runtime hardening, External Secrets / AWS Secrets Manager, OIDC-based GitHub Actions (no long-lived cloud keys)
- **Observability** — OpenTelemetry, Prometheus, Grafana, Jaeger
- **FinOps & reliability** — budgets, cost anomaly detection, right-sizing, maintenance without downtime
- **Infrastructure as code** — Terraform bootstrap and environment stacks for VPC, EKS, ECR, IAM, Karpenter, and related modules

---

## How the pieces fit together

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

## Repository layout

```text
.
├── README.md
├── CREDITS.md
├── platform/     ← app source + CI
├── chart/        ← Helm + GitOps
└── infra/        ← Terraform / AWS
```

### `platform` — application & CI/CD

Internal microservice platform for an online store: polyglot, Kubernetes-native services, AI product-review summarization, and a full observability stack.

**Highlights**

- `src/` microservices + AI review service
- Local run via Docker Compose / Make
- GitHub Actions: multi-arch build, push to AWS ECR (OIDC), chart promote (dev direct-push / prod PR)
- Proto contracts and image bake definitions (`docker-bake.hcl`)

**Entry points:** [README](./platform/README.md) · [docs/CICD.md](./platform/docs/CICD.md) · [docs/DEPLOYMENT.md](./platform/docs/DEPLOYMENT.md)

### `chart` — Helm + GitOps delivery

Helm chart that deploys the platform on Kubernetes: app workloads, AI/LLM components, observability, plus GitOps bootstrap for Argo CD.

**Highlights**

- Env overlays: `values-dev.yaml`, `values-prod.yaml`, public ALB overlay
- `gitops/` App-of-Apps style cluster manifests
- Runtime hardening (PSS-restricted, Gatekeeper, admission tests)
- Network policy, External Secrets chart, maintenance / HPA / probe ops docs

**Entry points:** [README](./chart/README.md) · [docs/DEPLOYMENT.md](./chart/docs/DEPLOYMENT.md) · [docs/operations/gitops-argocd.md](./chart/docs/operations/gitops-argocd.md)

### `infra` — AWS infrastructure as code

Terraform for bootstrap and environment stacks: remote state, GitHub OIDC roles, VPC, EKS, nested ECR, Load Balancer Controller IAM, secrets integration, Client VPN, cost budgets / anomaly detection, and related modules.

**Highlights**

- Bootstrap: S3 state, OIDC, platform ECR push roles, Terraform plan/apply roles
- Environments: development & production
- CI/CD for Terraform (plan on PR, promote / drift workflows)
- Modules for Karpenter, Argo CD, External Secrets, RDS/MSK patterns, cost controls

**Entry points:** [docs/DEPLOYMENT.md](./infra/docs/DEPLOYMENT.md) · [docs/SETUP.md](./infra/docs/SETUP.md) · [docs/CI_CD_GUIDE.md](./infra/docs/CI_CD_GUIDE.md)

---

## Technologies

| Area | Examples |
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

## Getting started

1. **Local app stack** — see [platform/docs/LOCAL_BUILD_AND_RUN.md](./platform/docs/LOCAL_BUILD_AND_RUN.md) and the platform [README](./platform/README.md).
2. **Cloud foundation** — follow [infra/docs/SETUP.md](./infra/docs/SETUP.md) and [infra/docs/DEPLOYMENT.md](./infra/docs/DEPLOYMENT.md) in an isolated AWS account.
3. **Cluster delivery** — deploy with the Helm chart and GitOps layout under [chart/](./chart/); see [chart/docs/DEPLOYMENT.md](./chart/docs/DEPLOYMENT.md).

Do not apply Terraform or cluster changes against shared production accounts without review and an intentional environment.

---

## Credits

Initial codebase contributors are listed in **[CREDITS.md](./CREDITS.md)**.
