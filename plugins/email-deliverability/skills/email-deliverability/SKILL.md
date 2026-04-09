---
name: email-deliverability
description: Diagnoses and fixes email deliverability issues by checking SPF, DKIM, DMARC, BIMI records, blacklist status, and SMTP/IMAP server connectivity. Generates DNS records for email authentication. Activates when the task involves email going to spam, domain reputation, DNS authentication setup, mail server configuration, sender verification, or email infrastructure troubleshooting.
license: MIT
compatibility: "Requires network access to the MailX MCP server (mailx) at https://tools.themailx.com/mcp"
metadata:
  author: mailx
  version: 1.2
  source: dynamic
  source_url: "https://tools.themailx.com/skills/email-deliverability/SKILL.md"
---

# Email Deliverability

Use the MailX MCP tools (`mailx` server) to diagnose, audit, and fix email authentication and delivery issues.

## Tools

All tools are on the `mailx` MCP server. Use fully qualified names (`mailx:tool-name`):

| Tool | Purpose |
|------|---------|
| `mailx:bimi-check` | Check if a domain has a valid BIMI (Brand Indicators for Message Identification) DNS record. BIMI allows brands to display their logo next to authenticated emails in supporting email clients. |
| `mailx:bimi-host` | Host and serve your BIMI SVG file for email authentication |
| `mailx:blacklist-check` | Check if a domain or IP address is listed in popular email blacklists (DNSBLs). Being blacklisted can severely impact email deliverability. |
| `mailx:dkim-check` | Check if a domain has a valid DKIM (DomainKeys Identified Mail) DNS record for a given selector. DKIM allows the receiver to verify that an email was sent by the domain owner. |
| `mailx:dmarc-check` | Check if a domain has a valid DMARC (Domain-based Message Authentication, Reporting & Conformance) DNS record. DMARC tells receiving servers what to do with emails that fail SPF or DKIM checks. |
| `mailx:dmarc-generate` | Generate a DMARC DNS record for a domain. Returns the record name, value, and type ready to be added to DNS. |
| `mailx:imap-check` | Test an IMAP server connection by attempting to connect and authenticate. Use this to verify email receiving configuration. |
| `mailx:imap-finder` | Look up IMAP server settings (host, port, encryption) for a given email provider. Use this to find the correct IMAP configuration for services like Gmail, Outlook, Yahoo, etc. |
| `mailx:smtp-check` | Test an SMTP server connection by attempting to connect and authenticate. Optionally sends a test email to verify full sending capability. |
| `mailx:smtp-finder` | Look up SMTP server settings (host, port, encryption) for a given email provider. Use this to find the correct SMTP configuration for services like Gmail, Outlook, SendGrid, etc. |
| `mailx:spf-check` | Check if a domain has a valid SPF (Sender Policy Framework) DNS record. SPF specifies which mail servers are authorized to send email on behalf of a domain. |
| `mailx:spf-generate` | Generate an SPF DNS record for a domain based on the email provider being used. Returns the record name, value, and type ready to be added to DNS. |

## Workflow: Full Deliverability Audit

When diagnosing why emails go to spam or auditing a domain's setup:

```
Audit Progress:
- [ ] Run DNS checks (SPF, DMARC, DKIM)
- [ ] Check blacklist status
- [ ] Analyze combined results
- [ ] Generate missing records
- [ ] Report findings with fixes
```

1. **DNS checks**: Run `mailx:spf-check`, `mailx:dmarc-check`, and `mailx:dkim-check` for the domain. For DKIM, ask for the selector if unknown — try common ones: `google`, `default`, `selector1`, `selector2`, `k1`.
2. **Blacklist**: Run `mailx:blacklist-check`.
3. **Analyze**: See [references/diagnosis.md](https://tools.themailx.com/skills/email-deliverability/references/diagnosis.md) for interpreting combined results.
4. **Fix**: Generate missing records with `mailx:spf-generate` or `mailx:dmarc-generate`.
5. **Report**: Lead with a pass/fail summary. For each failure, give the exact DNS record to add in copy-pasteable format.

## Workflow: DNS Record Setup

When setting up email authentication from scratch:

1. Ask which email provider they use.
2. Generate SPF with `mailx:spf-generate` using their provider.
3. Generate DMARC with `mailx:dmarc-generate` — default to `none` policy, recommend upgrading to `quarantine`/`reject` after monitoring.
4. DKIM must be enabled in the provider's admin dashboard — it cannot be generated externally.
5. Give all DNS records in copy-pasteable format (name, type, value).

## Workflow: Mail Server Troubleshooting

When a user has issues sending or receiving email:

1. Look up correct settings with `mailx:smtp-finder` or `mailx:imap-finder`.
2. Test with `mailx:smtp-check` or `mailx:imap-check` using their credentials.
3. Interpret errors:
   - **Auth failed** → wrong credentials or needs app-specific password
   - **Connection failed** → wrong host/port or firewall blocking
   - **SSL failed** → wrong encryption setting for the port

## Key Rules

- **SPF**: Exactly one record per domain. Multiple = both invalid. Merge all `include:` into one.
- **DKIM**: Selector-specific. One domain can have many DKIM records. Common selectors: `google`, `selector1`/`selector2`, `k1`, `default`.
- **DMARC**: Start `p=none` → monitor → `p=quarantine` → `p=reject`. Always include `rua=`.
- **Blacklists**: Each blacklist has its own delisting process. Direct the user to the specific blacklist's website.
- **Ports**: SMTP 587 (TLS), 465 (SSL), 25 (relay). IMAP 993 (SSL), 143 (STARTTLS).
