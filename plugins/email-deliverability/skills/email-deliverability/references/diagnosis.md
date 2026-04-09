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

Confirm the correct selector before concluding it's missing. Try: `google`, `default`, `selector1`, `selector2`, `k1`, `s1`, `dkim`. If truly absent, the user must enable DKIM in their email provider's admin panel.

## Provider Quick Reference

| Provider | SPF include | DKIM selectors | Notes |
|----------|------------|----------------|-------|
| Google Workspace | `include:_spf.google.com` | `google` | Enable in Admin Console > Gmail > Authenticate email |
| Microsoft 365 | `include:spf.protection.outlook.com` | `selector1`, `selector2` | Enable in Microsoft 365 Defender |
| SendGrid | `include:sendgrid.net` | `s1`, `s2` | |
| Mailgun | `include:mailgun.org` | Varies per domain | Check Mailgun dashboard |
| Amazon SES | `include:amazonses.com` | 3 CNAME records | Provided in SES console |
| Postmark | `include:spf.mtasv.net` | CNAME record | Provided in Postmark dashboard |
