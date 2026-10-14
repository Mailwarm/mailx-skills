---
description: Run a full email deliverability audit of a domain (SPF, DKIM, DMARC, blacklists) with fixes
argument-hint: <domain> [dkim-selector]
---

Audit email deliverability for this domain: $ARGUMENTS

The first argument is the domain. A second argument, if present, is the DKIM selector. If no domain was given, ask for it.

Run these mailX checks in parallel:
- `spf_check` for the domain
- `dmarc_check` for the domain
- `dkim_check` for the domain, with the selector if one was given. Without one, mailX tries common selectors. If none match, say the selector was not found (not that DKIM is missing) and ask for the `s=` value from the DKIM-Signature header of an email the user sent.
- `blacklist_check` for the domain

Then:
1. Report pass or fail for each check as a compact summary.
2. For every failure or missing record, give the fix:
   - Missing or invalid SPF: use `spf_generate`.
   - Missing or invalid DMARC: use `dmarc_generate` with policy none, and recommend moving to quarantine, then reject, after monitoring.
   - Missing DKIM: DKIM must be enabled in the email provider's admin console. Give provider-specific steps.
3. Give every DNS fix in copy-pasteable form: Type | Name | Value.
4. For each blacklist hit, name the list and point to its delisting page.
