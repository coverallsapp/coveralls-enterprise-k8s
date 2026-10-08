# Coveralls Enterprise — Kubernetes Install Path

Self-hosted code coverage tracking for your organization, deployed on Kubernetes with a Helm chart.

This repository is the home for the **Kubernetes install path** for Coveralls Enterprise — install documentation and operational guidance for running Coveralls Enterprise on a Kubernetes cluster you operate.

> **Access:** If you've reached this page, your organization probably has an active Coveralls Enterprise agreement. This repo is documentation-only. The Coveralls Enterprise container image and Helm chart are distributed privately. You will be issued a pull token for both CHCR.io resources before your install date. Reach out to your Coveralls representative for any questions.

---

## What you need to install

| Component | Where it comes from |
|---|---|
| **Container image** | `ghcr.io/coverallsapp/coveralls-enterprise` (private — pull token required) |
| **Helm chart** | Provided by your Coveralls account contact |

The container image is the application; the Helm chart is the recipe that tells Kubernetes how to run it (web, workers, Redis, and an optional in-cluster database). You'll use both together.

---

## Who this is for

Existing **Coveralls Enterprise** customers who want to self-host on a Kubernetes cluster (1.24+) they operate — on-prem or in their own cloud account. Your coverage data is processed entirely within your own infrastructure.

---

## Getting started

### 1. Get your image pull token

The container image is private. Your Coveralls contact will issue your organization a pull token for `ghcr.io/coverallsapp/coveralls-enterprise`. Create it as a Kubernetes secret:

```bash
kubectl create namespace coveralls

kubectl create secret docker-registry coveralls-ghcr-pull \
  --docker-server=ghcr.io \
  --docker-username=coveralls-customer \
  --docker-password=YOUR_PULL_TOKEN \
  --namespace=coveralls
```

> **For production, we recommend mirroring the image into your own registry** and pointing the chart at it, so your cluster doesn't depend on an external pull token at runtime.

### 2. Get the Helm chart

Your Coveralls account contact will provide the Helm chart for your contracted version, along with an example `values.yaml`. (Future versions may distribute the chart directly — your contact will let you know.)

### 3. Configure and install

```bash
# Fill in the required values (hostname, admin email, license, database, OAuth)
cp values.example.yaml my-values.yaml

# Install
helm install coveralls ./coveralls-enterprise \
  --namespace coveralls \
  --values my-values.yaml
```

The chart bundle includes a `README.md` with the full list of required values, sizing guidance, and an architecture overview.

---

## Prerequisites (summary)

- **Kubernetes 1.24+** with an Ingress Controller
- **Helm 3.10+**
- A **pull token** for the image (issued by Coveralls)
- A **TLS certificate** for your hostname (or cert-manager)
- **PostgreSQL 16.x recommended** (15.x and 16.x validated) — a managed database service (RDS, Azure Database for PostgreSQL, Cloud SQL) is strongly recommended for production

---

## Support

Coveralls Enterprise includes a one-command diagnostic bundle that collects pod logs and cluster resource state — **never secret values**. When you open a support request, please attach a bundle.

```bash
# One-time: install the Troubleshoot.sh CLI
brew install replicatedhq/replicated/support-bundle
# (or: kubectl krew install support-bundle)

# Collect a bundle (support-bundle.yaml ships with the chart)
support-bundle support-bundle.yaml \
  --namespace coveralls \
  --output ./coveralls-support-bundle.tar.gz \
  --interactive=false
```

This produces a `.tar.gz` of pod logs and cluster resource state — Secret **names only, never values**. Email it to support@coveralls.io or your account contact. The chart's `SUPPORT_BUNDLE.md` documents exactly what is and isn't collected, and how to inspect the bundle before sharing.

- **Support:** support@coveralls.io
- **Documentation:** https://docs.coveralls.io/enterprise
- **Website:** https://coveralls.io

---

© Coveralls. Coveralls Enterprise is licensed software, provided under your organization's agreement with Coveralls. Not for redistribution.
