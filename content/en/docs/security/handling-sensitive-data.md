---
title: Handling sensitive data
description:
  Best practices and guidance for handling sensitive data in OpenTelemetry
weight: 100
cSpell:ignore: anonymization
---

When implementing OpenTelemetry, it's crucial to be mindful of sensitive data
handling. The collection of telemetry data always carries the risk of
inadvertently capturing sensitive or personal information that may be subject to
various privacy regulations and compliance requirements.

## Your Responsibility

OpenTelemetry collects telemetry data, but it can’t tell which of that data is sensitive in your context. As the implementer, you are
responsible for:

- Ensuring compliance with applicable privacy laws and regulations.
- Protecting sensitive information in your telemetry data.
- Obtaining necessary consents for data collection.
- Implementing appropriate data handling and storage practices.

Additionally, you are responsible for understanding and reviewing the telemetry
data emitted by any instrumentation libraries you use, as these libraries may
collect and expose sensitive information as well.

## Sensitive Data Considerations

What counts as sensitive data depends on your situation. Examples include:

- Personally identifiable information (PII)
- Authentication credentials
- Session tokens
- Financial information
- Location data
- Health-related data
- User behavior data

## Data Minimization

When collecting potentially sensitive data through telemetry, follow the
principle of
[data minimization](https://en.wikipedia.org/wiki/Data_minimization). This
means:

- Only collect data that serves an observability purpose.
- Avoid collecting personal information unless absolutely necessary.
- Consider whether aggregated or anonymized data could serve the same purpose.
- Regularly review collected attributes to ensure they remain necessary.

## Protecting Sensitive Data

As outlined in the previous section, the best way to protect sensitive data is to avoid collecting it in the first place. However, you
might need to collect some of it, or you might not have a full control on what your instrumentation captures. In those cases, you can scrub the data during post-processing. 
For that purpose, the OpenTelemetry Collector provides several processors:

- [`attribute` processor](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/attributesprocessor):
  Removes or modifies specific attributes.
- [`filter` processor](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/filterprocessor):
  Filters out entire spans or metrics containing sensitive data.
- [`redaction` processor](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/redactionprocessor):
  Deletes span, log, and any metric datapoint attribute that doesn’t match an allowlist.
- [`transform` processor](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/transformprocessor):
  Transforms data using regular expressions.

### Deleting and Hashing User Information

The following `attributes` processor configuration hashes the
`user.email` attribute and deletes `user.full_name` from the sensitive
[`user`](/docs/specs/semconv/registry/attributes/user/#user-hash) information:

```yaml
processors:
  attributes/example:
    actions:
      - key: user.email
        action: hash
      - key: user.full_name
        action: delete
```

### Replacing `user.id` with `user.hash`

The following `transform` processor configuration removes
the `user.id` attribute and replace it with `user.hash`:

```yaml
transform:
  trace_statements:
    - context: span
      statements:
        - set(attributes["user.hash"], SHA256(attributes["user.id"]))
        - delete_key(attributes, "user.id")
```

> **[WARNING] Risks and Limitations of Hashing**
>
> Hashing a user ID or name may not anonymize it well enough.
> If the possible inputs are few or predictable (for example, numeric user IDs), the original values can be recovered by brute force.

### Truncating IP Addresses

As an alternative to hashing, you can truncate data, or group it by a common prefix or suffix. 
For example, you can apply this approach to:

- **Dates:** Keep only the year, or the year and the month, but drop the  day.
- **Email addresses:** Drop the local part and only keep the domain.
- **IP addresses:** Drop the last octet of IPv4 or the last 80 bits of IPv6.

The following `transform` processor configuration drops the last octet of the `client.address` attribute:

```yaml
transform:
  trace_statements:
    - context: span
      statements:
        - replace_pattern(attributes["client.address"], "\\.\\d+$", ".0")
```

### Deleting Attributes with Redaction Processor

To find an example on how the `redaction` processor deletes certain attributes, see [Scrub sensitive data](/docs/security/config-best-practices/#scrub-sensitive-data).


