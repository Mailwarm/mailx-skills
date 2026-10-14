---
description: Generate SPF and DMARC DNS records for a domain and its email provider
argument-hint: <domain> <provider>
---

Set up email authentication DNS for: $ARGUMENTS

The first argument is the domain and the second is the email provider (for example google, mailgun, sendgrid, postmark, amazon-ses). Ask for anything missing.

1. Call `spf_generate` with the domain and provider, without hard fail.
2. Call `dmarc_generate` with the domain, policy none and the report address dmarc@<domain> (or one the user prefers).

Deliver the records in copy-pasteable form (Type | Name | Value):
- The SPF TXT record, or what to set up instead when the provider does not need a root SPF include.
- The DMARC TXT record.
- DKIM: explain that it must be enabled in the provider's admin console, with provider-specific steps.

mailX does not publish anything: the user adds the records at their DNS provider. Recommend moving DMARC from p=none to p=quarantine, then p=reject, after 2–4 weeks of monitoring the aggregate reports.
