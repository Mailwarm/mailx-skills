---
description: Run a full email deliverability audit of a domain (SPF, DKIM, DMARC, blacklists, MX) with fixes
argument-hint: <domain> [dkim-selector]
---

Audit email deliverability for this domain: $ARGUMENTS

The first argument is the domain. A second argument, if present, is the DKIM selector. If no domain was given, ask for it.

Call the mailX `audit_domain` tool with the domain, and with `dkim_selector` only if a selector was given. It runs SPF, DKIM, DMARC, blacklist and MX checks in one call. If DKIM comes back as not found, say the selector was not found (not that DKIM is missing) and ask for the `s=` value from the DKIM-Signature header of an email the user sent, then run `dkim_check` with it.

Then:
1. Report the overall result and pass, warning or fail for each check as a compact summary.
2. Walk through the returned fixes in order:
   - Missing or invalid SPF: use `spf_generate`.
   - Missing or invalid DMARC: use `dmarc_generate` with policy none, and recommend moving to quarantine, then reject, after monitoring.
   - Missing DKIM: DKIM must be enabled in the email provider's admin console. Give provider-specific steps.
3. Give every DNS fix in copy-pasteable form: Type | Name | Value.
4. For each blacklist hit, name the list and point to its delisting page.
