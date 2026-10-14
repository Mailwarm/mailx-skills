---
name: email-deliverability
description: Diagnoses and fixes email deliverability problems with the mailX tools by checking SPF, DKIM, DMARC and BIMI records, blacklist status and DNS, and by generating SPF and DMARC records. Use when the user's emails go to spam or bounce, or when the task involves domain reputation, email authentication DNS setup, sender verification, or finding SMTP/IMAP settings for an email provider.
license: MIT
compatibility: "Requires the mailX MCP server (mailx) at https://themailx.com/mcp/claude, which this plugin connects automatically"
metadata:
  author: Mailwarm Inc.
  version: "2.0.0"
---

# Email Deliverability

Use the mailX tools (`mailx` server) to diagnose, audit and fix email authentication and delivery issues. All tools are read-only: they read public DNS, blocklists and mailX's provider catalog. mailX never changes DNS, never sends email and never asks for passwords. The user publishes generated records at their DNS provider.

## Tools

| Tool | Use it when |
|------|-------------|
| `audit_domain` | The user asks why their emails go to spam or wants a full deliverability check of a domain. Runs SPF, DKIM (finds the selector on its own), DMARC, blacklist and MX checks in one call and returns pass, warning or fail for each, an overall result, and the fixes in priority order with help links. Takes a few seconds. |
| `spf_check` | The user asks whether a domain's SPF record is valid, which servers may send email for a domain, or why SPF fails. Returns the live SPF record, a pass/fail status and a fix suggestion. |
| `dkim_check` | The user wants to verify DKIM for a domain. Pass the selector if known (for example `google` for Google Workspace, `selector1` or `selector2` for Microsoft 365). If it is unknown, omit it: mailX tries common selectors and returns the ones it finds. A miss on common selectors does not prove DKIM is absent. |
| `dmarc_check` | The user asks about DMARC, spoofing protection, or what happens to emails that fail authentication. Returns the live record, its policy and a fix suggestion. |
| `bimi_check` | The user asks about BIMI or showing a brand logo next to their emails. Returns the live BIMI record, the logo URL and a fix suggestion. |
| `blacklist_check` | The user suspects their domain is blacklisted or blocked. Returns the result for each major DNS blocklist. |
| `spf_generate` | The user needs a new SPF record for their email provider. Returns a TXT record ready to publish, or explains what to set up instead when the provider needs no root SPF include. |
| `dmarc_generate` | The user needs a DMARC record. Returns the record name, value and type ready to publish. |
| `smtp_finder` | The user needs outgoing mail (SMTP) settings for a provider or sending service, such as Gmail, Microsoft 365, Zoho, Amazon SES or SendGrid. Returns host, ports with encryption, what to log in with and the provider's setup guide. |
| `imap_finder` | The user needs incoming mail (IMAP) settings for a provider, such as Gmail, Outlook, Yahoo, iCloud or Zoho. Returns host, ports with encryption and the setup guide. |
| `mx_lookup` | The user wants to know which mail servers receive email for a domain. |
| `txt_lookup` | The user wants a domain's TXT records, for example verification tokens or published policies. |
| `cname_lookup` | The user wants to know whether a hostname is an alias, for example DKIM or tracking CNAMEs. |
| `ptr_lookup` | The user wants the reverse DNS (PTR) of a public IP address, for example a sending server. |
| `dns_lookup` | The user wants every DNS record for a domain in one view (A, AAAA, CNAME, MX, NS, TXT, SOA). |

Ask for the domain if it is missing. Each check result carries a fix suggestion and, when available, a link to a help article: pass them on.

## Workflow: full deliverability audit

When emails go to spam or the user wants a domain checked:

1. **Run** `audit_domain` with the domain (and the DKIM selector, only if the user gave one). It runs SPF, DKIM, DMARC, blacklist and MX checks in one call. Use the single-check tools only to re-check one item or dig deeper.
2. **DKIM not found**: say the selector was not found, not that DKIM is missing. Ask for the `s=` value from the DKIM-Signature header of an email they sent, then call `dkim_check` with it.
3. **Analyze**: read [references/diagnosis.md](references/diagnosis.md) to interpret the combined results.
4. **Fix**: follow the `fixes` list in the order returned. Generate missing or broken records with `spf_generate` or `dmarc_generate`.
5. **Report**: lead with the overall result and a pass/warning/fail summary. For each failure, give the exact DNS record to add as Type | Name | Value.

The `/mailx:audit` command runs this workflow.

## Workflow: DNS record setup

When setting up email authentication from scratch:

1. Ask which email provider they send with.
2. Generate SPF with `spf_generate` for that provider.
3. Generate DMARC with `dmarc_generate`: start with policy `none`, then recommend `quarantine` and `reject` after monitoring.
4. DKIM must be enabled in the provider's admin console. It cannot be generated externally.
5. Give all records in copy-pasteable form (Type | Name | Value).

The `/mailx:setup-dns` command runs this workflow.

## Workflow: mail server settings

When the user cannot send or receive email from an app or device:

1. Look up the correct settings with `smtp_finder` or `imap_finder`.
2. Compare them with what the user configured: host, port and encryption must match.
3. Common causes:
   - **Authentication fails**: wrong credentials, or the provider requires an app password or OAuth.
   - **Connection fails**: wrong host or port, or a firewall blocks the port.
   - **TLS errors**: the encryption setting does not match the port.

## Key rules

- **SPF**: exactly one record per domain. Two records make both invalid: merge every `include:` into one. Stay under 10 DNS lookups.
- **DKIM**: selector-specific. A domain can have many DKIM records.
- **DMARC**: start `p=none`, monitor, then `p=quarantine`, then `p=reject`. Always include `rua=`.
- **Blacklists**: each list has its own delisting process. Point the user to that list's website.
- **Ports**: SMTP 587 (STARTTLS), 465 (SSL/TLS), 25 (server relay). IMAP 993 (SSL/TLS), 143 (STARTTLS).
