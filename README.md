# 🔐 Trust Manager - CleanStart Container

This project provides a CleanStart container image for **Trust Manager**, a Kubernetes controller that manages trust bundles and certificates for cert-manager. Trust Manager enables distribution of trust bundles across Kubernetes clusters, allowing services to access trusted certificate authorities and root certificates. The CleanStart Trust-Manager image provides a production-ready, security-hardened container optimized for enterprise environments. Built on a minimal base OS with comprehensive security hardening, this image delivers reliable application execution with advanced security features.

**📌 CleanStart Foundation:** Security-hardened, minimal base OS designed for enterprise containerized environments.

**Image Path:** `ghcr.io/cleanstart-containers/trust-manager`

**Registry:** CleanStart Registry

---

## Overview

Trust Manager is a Kubernetes controller that manages trust bundles and certificates for cert-manager. It operates as a Kubernetes-native controller, continuously monitoring Bundle resources and distributing trust bundles across the cluster. This CleanStart Trust-Manager container is part of the CleanStart application suite, featuring enterprise-grade security hardening, automated vulnerability management, and compliance with industry standards.

---

## About CleanStart

CleanStart is a comprehensive container registry providing security-hardened, enterprise-ready container images. Our images are designed with security-first principles, featuring minimal attack surfaces, regular security updates, and compliance with industry standards.

### About CleanStart Images

CleanStart images are built on secure, minimal base operating systems and optimized for production environments. Each image undergoes rigorous security testing, vulnerability scanning, and compliance validation to ensure enterprise-grade security and reliability.

---

## Project Structure

The Trust Manager container project is organized into the following structure:

- **Root Directory**: Contains the main project README and container build configuration
- **`kubernetes/`**: Complete Kubernetes deployment manifests and documentation for testing the Trust Manager container in a Kubernetes environment

---

## Container Image

**Image:** `ghcr.io/cleanstart-containers/trust-manager:latest-dev`

The container image includes:

- Trust Manager binary located at `/usr/bin/trust-manager`
- Non-root user execution (`clnstrt` user, UID 1000)
- Pre-configured SSL certificates at `/etc/ssl/certs/ca-certificates.crt`
- Linux/amd64 architecture support
- Security-hardened configuration with minimal privileges

---

## Key Features

- **Security-First Design**: Built with minimal attack surfaces and security hardening
- **Enterprise Compliance**: Meets industry standards including FIPS, STIG, and CIS benchmarks
- **Regular Updates**: Automated security patches and vulnerability management
- **Multi-Architecture Support**: Available for AMD64 and ARM64 architectures
- **Production Ready**: Optimized for enterprise deployment and scaling
- **Comprehensive Documentation**: Detailed guides and best practices for each image
- **Controller Functionality**: Runs Trust Manager as a Kubernetes controller for managing trust bundles
- **Webhook Support**: Validating webhook server for Bundle resource validation
- **Metrics Exposure**: Prometheus metrics endpoint on port 9402
- **Health Checks**: Readiness probe on port 6060 at `/readyz` path
- **Security**: Runs as non-root user with dropped capabilities and no privilege escalation
- **Resource Management**: Configured with CPU and memory requests/limits
- **Leader Election**: Enabled by default to prevent multiple controller instances

---

## Use Cases

Typical scenarios where this container excels:

- **Image Validation**: Testing the CleanStart Trust Manager container image
- **Development Testing**: Validating Trust Manager functionality in Kubernetes
- **Bundle Management Testing**: Testing trust bundle distribution across clusters
- **Webhook Testing**: Validating webhook functionality for Bundle resources
- **CI/CD Integration**: Automated testing of the container image
- **Trust Bundle Distribution**: Enterprise-wide certificate authority management
- **Certificate Management**: Automated trust bundle updates across services

---

## Prerequisites

Before deploying, you must:

1. **Install CRDs**: Trust Manager requires Bundle CRDs (`bundles.trust.cert-manager.io`) to be installed
2. **Generate TLS Certificates**: Webhook server requires TLS certificates at `/tls/tls.crt` and `/tls/tls.key`
3. **Kubernetes Cluster**: Any Kubernetes cluster (Kind, minikube, k3s, GKE, EKS, AKS, etc.)
4. **kubectl**: Installed and configured to connect to your cluster

---

## Quick Start

### Pull Commands
```bash
docker pull ghcr.io/cleanstart-containers/trust-manager:latest
docker pull ghcr.io/cleanstart-containers/trust-manager:latest-dev
```

### Run Commands

Basic test:
```bash
docker run -it --name trust-manager-test ghcr.io/cleanstart-containers/trust-manager:latest-dev
```

Production deployment:
```bash
docker run -d --name trust-manager-prod \
  --read-only \
  --security-opt=no-new-privileges \
  --user 1000:1000 \
  ghcr.io/cleanstart-containers/trust-manager:latest
```

---

## Kubernetes Deployment

The `kubernetes/` directory contains a complete deployment setup for testing the Trust Manager container functionality in Kubernetes clusters.

### Deployment Components

The Kubernetes deployment includes:

- **Namespace**: Isolated `trust-manager-test` namespace for testing
- **ServiceAccount**: Dedicated service account with appropriate RBAC permissions
- **ClusterRole & ClusterRoleBinding**: Permissions for managing Bundle CRDs and core Kubernetes resources
- **Deployment**: Single-replica deployment running the Trust Manager controller
- **Service**: ClusterIP service exposing ports 9402 (metrics), 6443 (webhook), and 6060 (readiness probe)
- **Secret**: TLS certificates for webhook server (must be created before deployment)

### Current Configuration

The deployment is configured for **testing and validation purposes**. The controller runs with:

- Leader election enabled for high availability
- Metrics server on port 9402
- Webhook server on port 6443 (requires TLS certificates)
- Log level set to info

This setup is ideal for:

- Verifying the Trust Manager binary functionality
- Testing Bundle resource management
- Validating the container image in a Kubernetes environment
- Testing webhook validation
- CI/CD pipeline testing

For detailed deployment instructions, testing procedures refer to the `kubernetes/README.md` file in the `kubernetes/` directory.

---

## Architecture Support

CleanStart images support multiple architectures to ensure compatibility across different deployment environments:

- **AMD64**: Intel and AMD x86-64 processors
- **ARM64**: ARM-based processors including Apple Silicon and ARM servers

### Architecture-based Pull Commands
```bash
docker pull --platform linux/amd64 ghcr.io/cleanstart-containers/trust-manager:latest
docker pull --platform linux/arm64 ghcr.io/cleanstart-containers/trust-manager:latest
```

---

## Notes

The current Kubernetes deployment runs Trust Manager as a controller with webhook support. For production use, you would typically:

- Use properly signed TLS certificates (not self-signed)
- Configure Bundle resources to manage trust bundles
- Set up monitoring and alerting for the controller
- Configure appropriate resource limits based on workload
- Enable high availability with multiple replicas
- Integrate with cert-manager for certificate management
- Implement proper RBAC policies

---

## Resources

- **Official Documentation:** https://cert-manager.io/docs/trust/trust-manager/
- **Trust Manager GitHub Repository:** https://github.com/cert-manager/trust-manager
- **Provenance / SBOM / Signature:** https://images.cleanstart.com/images/trust-manager
- **Docker Hub:** https://hub.docker.com/r/cleanstart/trust-manager
- **CleanStart All Images:** https://images.cleanstart.com
- **CleanStart Community Images:** https://hub.docker.com/u/cleanstart

---

## Disclaimer & License

### Disclaimer

**Disclaimer:** This documentation is provided for informational purposes only. Users are responsible for ensuring compliance with applicable laws, regulations, and security requirements. CleanStart makes no warranties regarding the suitability of these images for specific use cases or environments.

### License

Apache-2.0

---

## Vulnerability Disclaimer

CleanStart offers Docker images that include third-party open-source libraries and packages maintained by independent contributors. While CleanStart maintains these images and applies industry-standard security practices, it cannot guarantee the security or integrity of upstream components beyond its control.

Users acknowledge and agree that open-source software may contain undiscovered vulnerabilities or introduce new risks through updates. CleanStart shall not be liable for security issues originating from third-party libraries, including but not limited to zero-day exploits, supply chain attacks, or contributor-introduced risks.

**Security remains a shared responsibility:** CleanStart provides updated images and guidance where possible, while users are responsible for evaluating deployments and implementing appropriate controls.
