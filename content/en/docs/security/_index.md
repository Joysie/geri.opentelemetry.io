---
title: Security
cascade:
  collector_vers: 0.162.0
weight: 970
---

In this section, learn how the OpenTelemetry project discloses vulnerabilities
and responds to incidents and discover what you can do to securely collect and
transmit your observability data.

## Common Vulnerabilities and Exposures (CVEs)

For CVEs across all repositories, see
[Common Vulnerabilities and Exposures](cve/).

## Incident Response

To learn how to report a vulnerability or how incident responses are
handled, see
[Community incident response guidelines](security-response/).

## Collector Security

When setting up the OpenTelemetry Collector, consider implementing security best
practices in both your hosting infrastructure and your Collector configuration.
Running a secure Collector helps you to:

- Protect telemetry that might contain sensitive information, such
  as personally identifiable information (PII), application-specific data, or
  network traffic patterns.
- Prevent data tampering that makes telemetry unreliable and disrupts incident
  responses.
- Comply with data privacy and security regulations.
- Defend against denial of service (DoS) attacks.

To learn how to secure your Collector's infrastructure, see [Hosting best practices](hosting-best-practices/) 

To learn how to securely configure your Collector, see [Configuration best practices](config-best-practices/) 

If you're a Collector component developer, see [Security best practices](https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/security-best-practices.md).
