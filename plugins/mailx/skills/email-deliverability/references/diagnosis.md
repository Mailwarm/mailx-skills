# Diagnosis Guide

## Ideal State

- SPF: pass (one record, all senders included)
- DKIM: pass (valid public key for the selector)
- DMARC: pass, `p=quarantine` or `p=reject`, `rua=` present
- Blacklist: not listed
- BIMI: pass (optional)

## Common Failure Patterns

### Emails going to spam

Check in order — stop at the first hit:

1. **No SPF** → generate immediately
2. **No DMARC** → generate with `p=none`
3. **SPF pass, DMARC fail** → alignment issue. `From:` domain doesn't match envelope sender. Common with third-party senders.
4. **Everything passes, still spam** → check blacklists
5. **All clear** → content-based (spammy subject, too many links, low sender reputation)

### Emails not arriving

1. DMARC `p=reject` → unauthorized senders silently dropped
2. Blacklisted → some lists cause outright rejection
3. SMTP not sending → test with `mailx:smtp-check`

### Multiple SPF records

Always wrong. Two records = both invalid. Fix: merge all `include:` into a single record.

### DMARC policy is none

Not a failure, but no protection. Recommend:
- Keep `p=none` for 2-4 weeks while monitoring
- Move to `p=quarantine`
- Eventually `p=reject`

### DKIM not found

Confirm the correct selector before concluding it's missing. Call `dkim_check` without a selector to try common ones, or read the `s=` value from the DKIM-Signature header of an email the user sent. A miss on common selectors does not prove DKIM is absent. If truly absent, the user must enable DKIM in their email provider's admin panel.

## Provider Quick Reference

| Provider | SPF include | DKIM selectors | Notes |
|----------|------------|----------------|-------|
| Google Workspace | `include:_spf.google.com` | `google` | Enable in Admin Console > Gmail > Authenticate email |
| Microsoft 365 | `include:spf.protection.outlook.com` | `selector1`, `selector2` | Enable in Microsoft 365 Defender |
| SendGrid | None on the root domain | `s1`, `s2` | SPF is handled by the `em####` return-path CNAME created by domain authentication |
| Mailgun | `include:mailgun.org` | Varies per domain | Check Mailgun dashboard |
| Amazon SES | None on the root domain | 3 CNAME records | For DMARC alignment, set a custom MAIL FROM subdomain with `include:amazonses.com` (never at the root) |
| Postmark | None on the root domain | CNAME record | SPF is handled by the `pm-bounces` Return-Path CNAME; `spf.mtasv.net` no longer publishes an SPF record |
