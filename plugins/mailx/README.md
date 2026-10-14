# mailX for Claude

mailX checks why emails land in spam and how to fix it. Ask Claude to audit a domain and it checks SPF, DKIM (it finds the selector on its own), DMARC, MX and major blacklists in one call, plus BIMI on request, explains each result in plain language, and gives you the exact DNS records to publish. It can also generate SPF and DMARC records, look up MX, TXT, CNAME, PTR and other DNS records, and find the SMTP or IMAP settings of common email providers.

## What's included

- **mailX server**: installing the plugin connects Claude to `https://themailx.com/mcp/claude`, the same server as the mailX connector in Claude's directory. Its tools are read-only: they read public DNS and blocklists. They never change your DNS, never send email and never ask for passwords.
- **Email deliverability skill**: teaches Claude when to use each tool, how to read combined results, and how to present fixes.
- **Commands**:
  - `/mailx:audit example.com` runs a full deliverability audit.
  - `/mailx:setup-dns example.com google` generates SPF and DMARC records for a domain and its email provider.

## Data and privacy

The plugin only talks to `https://themailx.com/mcp/claude`. It sends the domain names, hostnames, IP addresses or provider names you ask about, and nothing else. It sends no data anywhere else and runs no local code. See the [mailX privacy policy](https://themailx.com/privacy-policy).

## Install

In Claude Code:

```
/plugin marketplace add Mailwarm/mailx-skills
/plugin install mailx@mailx-skills
```

Documentation: [themailx.com/docs/mcp](https://themailx.com/docs/mcp). Support: [themailx.com/support](https://themailx.com/support).

## License

MIT, by Mailwarm Inc.
