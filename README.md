# mailX Skills

The [mailX](https://themailx.com) plugin for Claude: it teaches Claude to diagnose and fix email deliverability issues (SPF, DKIM, DMARC, BIMI, blacklists, DNS) and connects the mailX server automatically.

## Plugins

### mailx

Audits a domain's email authentication, explains each result, and gives the exact DNS records to fix it. Includes the `email-deliverability` skill, the mailX server (`https://themailx.com/mcp/claude`), and two commands: `/mailx:audit` and `/mailx:setup-dns`. Details in [plugins/mailx/README.md](./plugins/mailx/README.md).

## Install

### Claude Code

```
/plugin marketplace add Mailwarm/mailx-skills
/plugin install mailx@mailx-skills
```

### Claude.ai

Add the mailX connector from Claude's directory, or add a custom connector with the URL `https://themailx.com/mcp/claude`. To add the skill on its own, download [`email-deliverability.zip`](https://themailx.com/skills/email-deliverability.zip) and upload it under **Settings → Capabilities → Skills**.

### Other MCP clients

Cursor, VS Code, Windsurf and other clients can connect to `https://themailx.com/mcp`, which also includes the SMTP/IMAP login tests. Setup guide: [themailx.com/docs/mcp](https://themailx.com/docs/mcp).

## Structure

```
mailx-skills/
├── .claude-plugin/
│   └── marketplace.json
└── plugins/
    └── mailx/
        ├── .claude-plugin/plugin.json
        ├── .mcp.json
        ├── README.md
        ├── LICENSE
        ├── commands/
        │   ├── audit.md
        │   └── setup-dns.md
        └── skills/
            └── email-deliverability/
                ├── SKILL.md
                └── references/diagnosis.md
```

## Links

- [mailX](https://themailx.com)
- [MCP documentation](https://themailx.com/docs/mcp)
- [Support](https://themailx.com/support)

## License

MIT, by Mailwarm Inc.
