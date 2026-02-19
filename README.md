# keda-knative

Practical Kubernetes autoscaling playground that compares and combines **Knative** and **KEDA** under a GitOps workflow.

## What this repository demonstrates

This repository shows two different autoscaling models for applications running on Kubernetes:

- **Knative autoscaling**: scales based on incoming HTTP request traffic.
- **KEDA autoscaling**: scales based on external metrics/events (for example Prometheus queries).

Both models are useful, but for different workloads. This repo gives a clear, side-by-side reference so you can understand the behavior and operate both through manifests.

## Knative in this project

Knative Serving is used for request-driven workloads (serverless-like behavior).

How it works:

1. A `Service` is defined in `knative/service.yaml`.
2. Knative routes traffic through its networking layer.
3. The Knative autoscaler monitors request concurrency / request rate.
4. Replica count increases under load and can scale back down when traffic drops.

Why this matters:

- Great for HTTP services with bursty traffic.
- Supports fast scale-up for sudden demand.
- Can reduce cost by scaling low (or to zero, depending on configuration and platform setup) when idle.

## KEDA in this project

KEDA (Kubernetes Event-driven Autoscaling) is used for metric-driven scaling from systems outside the app itself.

How it works:

1. Base workload resources are defined in `keda/deployment.yaml` and `keda/service.yaml`.
2. A `ScaledObject` in `keda/scaledobject.yaml` defines trigger rules.
3. KEDA evaluates trigger values (for example a Prometheus metric).
4. KEDA drives HPA behavior to scale replicas up/down based on threshold conditions.

Related manifests in this repo:

- `keda/servicemonitor.yaml` enables scraping/monitoring integration.
- `keda/ingress.yaml` exposes the service path used for testing traffic.

Why this matters:

- Ideal for async/event-based workloads.
- Scales from business or system signals (queue depth, lag, custom metrics, etc.).
- Decouples scaling decisions from direct HTTP traffic patterns.

## KEDA vs Knative: when to use which

- Use **Knative** when scaling should follow request traffic to a service endpoint and want serverless like behaviour.
- Use **KEDA** when scaling should follow external signals/metrics/events.


## GitOps workflow in this repository

This repo is the source of truth for cluster behavior.

1. Update manifests in Git.
2. Push changes.
3. Argo CD (`argocd/application.yaml`) reconciles desired state to the cluster.
4. Observe traffic handling, scaling response, health, and sync state.
5. Iterate by changing thresholds, policies, or service config.

## Goal

Provide a clean, realistic baseline to experiment with autoscaling strategies, compare behavior under load, and manage everything through GitOps.

## Deployment URL testing

Keda testing:

`curl https://keda-vpc.kristijanboshev.com/healthz`

Knative testing:

`curl https://knative-service.keda-knative.kristijanboshev.com/healthz`
