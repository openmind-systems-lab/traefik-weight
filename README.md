<p align="center">
  <img src="https://raw.githubusercontent.com/openmind-systems-lab/.github/main/profile/logo.png" width="200">
</p>

<h1 align="center">Traefik Weighted Load Balancing</h1>

<p align="center">
An Open Source Proof of Concept demonstrating weighted traffic routing with Traefik on Kubernetes.
</p>

<p align="center">

![License](https://img.shields.io/badge/license-MIT-blue)
![Open Source](https://img.shields.io/badge/Open%20Source-Yes-brightgreen)
![Status](https://img.shields.io/badge/Status-Lab%20%2F%20PoC-orange)
![Kubernetes](https://img.shields.io/badge/Kubernetes-Supported-blue)
![Traefik](https://img.shields.io/badge/Traefik-Yes-purple)
![Association](https://img.shields.io/badge/OpenMind%20Systems%20Lab-Loi%201901-blue)

</p>

---

# 📖 Overview

This Proof of Concept demonstrates how **Traefik v3** performs weighted load balancing between two versions of the same application using Kubernetes Custom Resources.

The example routes **80% of the traffic** to a stable version and **20%** to a canary version, making it ideal for canary deployments and A/B testing.

---

# 🏗️ Architecture

![Architecture](media/schema.png)

---

# 🎯 Objective

This Proof of Concept demonstrates how to:

- Deploy Traefik using Helm.
- Deploy two application versions.
- Configure weighted traffic routing.
- Route 80% of requests to the stable version.
- Route 20% of requests to the canary version.
- Monitor traffic distribution using Prometheus metrics.

---

# ⚙️ Prerequisites

- Docker Desktop (Kubernetes enabled)
- kubectl
- Helm 3

---

# 📦 Install Traefik

Add the Helm repository:

```bash
helm repo add traefik https://traefik.github.io/charts
helm repo update
```

Install Traefik:

```bash
helm install traefik traefik/traefik \
  --namespace traefik \
  --create-namespace \
  --set ports.web.exposedPort=8081 \
  --set "additionalArguments={--metrics.prometheus=true,--metrics.prometheus.entryPoint=web,--api.dashboard=true,--api.insecure=true}"
```

---

# 🚀 Deploy the Demo

Deploy the applications:

```bash
kubectl apply -f apps.yaml
```

Deploy the weighted routing:

```bash
kubectl apply -f split-test.yaml
```

Expose the Traefik Dashboard:

```bash
kubectl apply -f traefik-dashboard.yaml
```

---

# 🔍 Verification

Verify that Traefik is running:

```bash
kubectl get pods -n traefik
```

Verify that both applications are running:

```bash
kubectl get pods
```

Verify the Traefik resources:

```bash
kubectl get ingressroute
kubectl get traefikservice
```

---

# 🧪 Testing

Send multiple requests:

```bash
for i in $(seq 1 20); do
  curl -s \
    -H "Host: split.localhost" \
    http://localhost:8081
done
```

Expected behaviour:

- Approximately 80% of responses come from **Stable-V1**
- Approximately 20% of responses come from **Experimental-V2**

Display Traefik metrics:

```bash
curl http://localhost:8081/metrics | grep traefik_service_requests_total
```

Expected output:

```text
default-app-v1-service-80    16
default-app-v2-service-80     4
```

Open the Traefik dashboard:

```
http://traefik.localhost:8081/dashboard/
```

Navigate to:

```
HTTP → Services
```

You should observe the weighted service configuration.

---

# 📚 What You Will Learn

After completing this Proof of Concept, you will understand how to:

- Install Traefik using Helm.
- Deploy Kubernetes applications behind Traefik.
- Configure weighted routing using Traefik CRDs.
- Perform canary deployments.
- Implement A/B testing.
- Inspect Traefik metrics.
- Visualize routing through the Traefik Dashboard.

---

# 🧹 Cleanup

Delete the dashboard route:

```bash
kubectl delete -f traefik-dashboard.yaml
```

Delete the weighted routing:

```bash
kubectl delete -f split-test.yaml
```

Delete the applications:

```bash
kubectl delete -f apps.yaml
```

Uninstall Traefik:

```bash
helm uninstall traefik -n traefik

kubectl delete namespace traefik
```

---

# 📚 References

- https://doc.traefik.io/traefik/
- https://doc.traefik.io/traefik/routing/providers/kubernetes-crd/
- https://doc.traefik.io/traefik/routing/services/

---

# 🏛 About OpenMind Systems Lab

OpenMind Systems Lab is an independent French non-profit association dedicated to research, experimental development and technical benchmarking in Cloud Native technologies.

Our mission is to produce practical, reproducible and educational Open Source Proofs of Concept covering Kubernetes, Platform Engineering, Distributed Messaging, Infrastructure Security and Artificial Intelligence.

GitHub Organization:

https://github.com/openmind-systems-lab

---

<p align="center">
Made with ❤️ by OpenMind Systems Lab
</p>
