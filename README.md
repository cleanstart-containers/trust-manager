# 🔐 Trust Manager - CleanStart Container

## Overview

This project provides a CleanStart container image for **Trust Manager**, a Kubernetes controller that manages trust bundles and certificates for cert-manager. Trust Manager enables distribution of trust bundles across Kubernetes clusters, allowing services to access trusted certificate authorities and root certificates.

## Project Structure

The Trust Manager container project is organized into the following structure:

- **Root Directory**: Contains the main project README and container build configuration
- **`kubernetes/`**: Complete Kubernetes deployment manifests and documentation for testing the Trust Manager container in a Kubernetes environment

## Container Image

**Image:** `cleanstart/trust-manager:latest-dev`

The container image includes:
- Trust Manager binary located at `/usr/bin/trust-manager`
- Non-root user execution (`clnstrt` user, UID 1000)
- Pre-configured SSL certificates at `/etc/ssl/certs/ca-certificates.crt`
- Linux/amd64 architecture support
- Security-hardened configuration with minimal privileges

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

### Key Features

- **Controller Functionality**: Runs Trust Manager as a Kubernetes controller for managing trust bundles
- **Webhook Support**: Validating webhook server for Bundle resource validation
- **Metrics Exposure**: Prometheus metrics endpoint on port 9402
- **Health Checks**: Readiness probe on port 6060 at `/readyz` path
- **Security**: Runs as non-root user with dropped capabilities and no privilege escalation
- **Resource Management**: Configured with CPU and memory requests/limits
- **Leader Election**: Enabled by default to prevent multiple controller instances

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

### Prerequisites

Before deploying, you must:

1. **Install CRDs**: Trust Manager requires Bundle CRDs (`bundles.trust.cert-manager.io`) to be installed
2. **Generate TLS Certificates**: Webhook server requires TLS certificates at `/tls/tls.crt` and `/tls/tls.key`

### Use Cases

This deployment is designed for:

- **Image Validation**: Testing the CleanStart Trust Manager container image
- **Development Testing**: Validating Trust Manager functionality in Kubernetes
- **Bundle Management Testing**: Testing trust bundle distribution across clusters
- **Webhook Testing**: Validating webhook functionality for Bundle resources
- **CI/CD Integration**: Automated testing of the container image

## Notes

The current Kubernetes deployment runs Trust Manager as a controller with webhook support. For production use, you would typically:

- Use properly signed TLS certificates (not self-signed)
- Configure Bundle resources to manage trust bundles
- Set up monitoring and alerting for the controller
- Configure appropriate resource limits based on workload
- Enable high availability with multiple replicas

## Related Documentation

For detailed deployment instructions, testing procedures refer to the `kubernetes/README.md` file in the `kubernetes/` directory.

