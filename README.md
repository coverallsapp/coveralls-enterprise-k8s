# Coveralls Enterprise — Kubernetes Install Path

Self-hosted code coverage tracking for your organization, deployed on Kubernetes with a Helm chart.

This repository is the home for the **Kubernetes install path** for Coveralls Enterprise — install documentation and operational guidance for running Coveralls Enterprise on a Kubernetes cluster you operate.

> **Access:** This is a private repository, and the Coveralls Enterprise container image is distributed privately. If you've reached this page, your organization has an active Coveralls Enterprise agreement. To add a teammate, contact your Coveralls account representative.

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
- **PostgreSQL 16.x** — a managed database service (RDS, Azure Database for PostgreSQL, Cloud SQL) is strongly recommended for production

---

## Support

Coveralls Enterprise includes a one-command diagnostic bundle that collects pod logs and cluster resource state — **never secret values**. When you open a support request, attach a bundle (instructions are included with the chart).

- **Support:** support@coveralls.io
- **Documentation:** https://docs.coveralls.io/enterprise
- **Website:** https://coveralls.io

---

© Coveralls. Coveralls Enterprise is licensed software, provided under your organization's agreement with Coveralls. Not for redistribution.
