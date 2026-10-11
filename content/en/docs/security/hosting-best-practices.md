---
title: Collector Hosting Best Practices
linkTitle: Collector Hosting
weight: 115
---

When setting up hosting for OpenTelemetry (OTel) Collector, consider these best
practices to better secure your hosting instance.

## Store data securely

Your Collector configuration file might contain sensitive data, including
authentication tokens or TLS certificates. If you store telemetry for later processing, restrict access to the relevant directories to prevent tampering with raw data.

For more information, see [Collector configuration best practices](../config-best-practices/#create-secure-configurations).

## Keep your secrets safe

Kubernetes secrets are credentials that hold confidential data. They authenticate and authorize
privileged access. If you're using a Kubernetes deployment for your Collector, follow the recommended practices to improve the security for your clusters. For more information, see:

- [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)
- [Good practices for Kubernetes Secrets](https://kubernetes.io/docs/concepts/security/secrets-good-practices/)

## Apply the principle of least privilege

The Collector should not require privileged access unless the data it collects is in a privileged location. 

For example, in a Kubernetes deployment, system logs, application logs, and container runtime logs are often stored in a node volume that requires special permission to access. If you run the Collector as a DaemonSet on the node, grant it only the volume mounts and permissions it needs to read these logs, and nothing more. 

You can limit its privileges further with role-based access control (RBAC). To learn how, see
[RBAC good practices](https://kubernetes.io/docs/concepts/security/rbac-good-practices/).

## Control access to server-like components

Some Collector components, such as receivers and exporters, can function like servers. To limit access to authorized users, you should:

- Enable authentication. For example, use bearer token auth extensions or basic auth extensions.
- Restrict the IPs that your Collector runs on.

## Safeguard resource utilization
Use the internal telemetry of the Collector's own to monitor its
performance. 
1. Collect metrics from the Collector about its CPU, memory, and
throughput usage and set alerts for resource exhaustion. To learn how, see [Internal Telemetry](/docs/collector/internal-telemetry/).

2. If resource limits are reached, consider scaling the Collector horizontally by deploying multiple instances in a load-balanced configuration. Scaling your Collector distributes the resource demands and prevents bottlenecks. For more information, see [Scaling the Collector](/docs/collector/scaling/).

3. Once you secure the resource utilization in your deployment, make sure your Collector instance also uses safeguards in its configuration. For more information, see [Safeguard resource utilization](../config-best-practices/#safeguard-resource-utilization)
