# 🔐 Trust Manager - Kubernetes Deployment Guide

Complete Kubernetes deployment guide for testing the CleanStart Trust Manager container. Trust Manager is a Kubernetes controller that manages trust bundles and certificates for cert-manager.

## Files

- `deployment.yaml` - Complete deployment manifest (Namespace, ServiceAccount, ClusterRole, ClusterRoleBinding, Deployment, Service)
- `README.md` - This documentation

## Image Details

**Image:** `cleanstart/trust-manager:latest-dev`

**Key Features:**
- **Binary Location:** `/usr/bin/trust-manager`
- **User:** `clnstrt` (non-root, UID 1000)
- **Architecture:** `amd64`
- **OS:** `linux`

## Deployment Steps

### Prerequisites

- Kubernetes cluster (Kind, minikube, k3s, GKE, EKS, AKS, or any other)
- `kubectl` installed and configured
- `openssl` for generating TLS certificates

### Step 1: Install Trust Manager CRDs (Required)

Trust Manager requires CRDs before deployment. Without these, the pod will crash.

```bash
# Clone and install CRDs
git clone https://github.com/cert-manager/trust-manager.git
cd trust-manager
kubectl apply -f deploy/crds/

# Verify CRDs are installed
kubectl get crd bundles.trust.cert-manager.io
```

**Note:** If you see annotation errors, use: `kubectl apply --server-side --force-conflicts -f deploy/crds/`

### Step 2: Generate TLS Certificates for Webhook (Required)

Trust Manager requires TLS certificates. The pod will crash without them.

```bash
# Navigate to deployment directory
cd containers/trust-manager/kubernetes

# Create namespace first (if it doesn't exist)
kubectl create namespace trust-manager-test --dry-run=client -o yaml | kubectl apply -f -

# Generate self-signed certificates
openssl req -new -x509 -days 365 -nodes \
  -out tls.crt \
  -keyout tls.key \
  -subj "/C=US/ST=State/L=City/O=Cleanstart/CN=trust-manager-webhook"

# Create the secret
kubectl create secret generic trust-manager-webhook-certs \
  --from-file=tls.crt=tls.crt \
  --from-file=tls.key=tls.key \
  -n trust-manager-test

# Verify secret
kubectl get secret trust-manager-webhook-certs -n trust-manager-test
```

**Note:** For production, use properly signed certificates or cert-manager.

### Step 3: Deploy Trust Manager

```bash
# Apply the deployment
kubectl apply -f deployment.yaml

# Watch pod status
kubectl get pods -n trust-manager-test -w
```

**Expected Output:**
```
namespace/trust-manager-test created
serviceaccount/trust-manager created
clusterrole.rbac.authorization.k8s.io/trust-manager created
clusterrolebinding.rbac.authorization.k8s.io/trust-manager created
deployment.apps/trust-manager created
service/trust-manager created
```

### Step 4: Verify Deployment

```bash
# Check pod status
kubectl get pods -n trust-manager-test

# View logs
kubectl logs -n trust-manager-test -l app=trust-manager -f
```

**Expected Log Output:**
```
time=... level=INFO msg="Starting metrics server" bindAddress=0.0.0.0:9402
time=... level=INFO msg="Serving webhook server" host=0.0.0.0 port=6443
time=... level=INFO msg="Starting Controller" controller=bundles
```

## 🧪 Testing & Access

### Access Metrics in Web Browser

```bash
# Port-forward to access metrics
kubectl port-forward -n trust-manager-test svc/trust-manager 9402:9402
```

Then open in your browser: **`http://localhost:9402/metrics`**

### Access Metrics via Command Line

```bash
# With port-forward running, get metrics
curl http://localhost:9402/metrics
```

### Verify Service

```bash
# Check service endpoints
kubectl get endpoints -n trust-manager-test trust-manager

# Get pod name and check status
POD_NAME=$(kubectl get pods -n trust-manager-test -l app=trust-manager -o jsonpath='{.items[0].metadata.name}')
kubectl get pod $POD_NAME -n trust-manager-test
```

## 🔧 Configuration

### Command Arguments

- `--leader-elect` - Leader election (default: true)
- `--metrics-port=9402` - Metrics server port
- `--log-level=2` - Logging level (1-5, where 2=info)

### Ports

- **9402:** Metrics endpoint (HTTP/TCP) - Prometheus metrics
- **6443:** Webhook endpoint (HTTPS/TCP) - Kubernetes webhook server
- **6060:** Readiness probe endpoint (HTTP/TCP) - Health check at `/readyz`

### Resource Limits

- **Requests:** CPU: 100m, Memory: 128Mi
- **Limits:** CPU: 500m, Memory: 512Mi

## 🧹 Cleanup

```bash
# Delete all resources
kubectl delete -f deployment.yaml

# Delete secret
kubectl delete secret trust-manager-webhook-certs -n trust-manager-test
```

## 📝 Notes

- **CRDs Required:** Bundle CRDs (`bundles.trust.cert-manager.io`) must be installed before deployment
- **TLS Certificates Required:** Webhook requires TLS certificates at `/tls/tls.crt` and `/tls/tls.key`
- **Leader Election:** Enabled by default to prevent multiple controllers
- **Production:** Use properly signed TLS certificates and configure Bundle resources

## ✅ Verification Checklist

- [ ] Pod is running (`kubectl get pods -n trust-manager-test`)
- [ ] Pod logs show "Starting metrics server" and "Starting Controller"
- [ ] Service endpoints configured (`kubectl get endpoints -n trust-manager-test trust-manager`)
- [ ] Port-forward works (`kubectl port-forward -n trust-manager-test svc/trust-manager 9402:9402`)
- [ ] Metrics accessible (`curl http://localhost:9402/metrics`)
