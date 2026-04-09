# MailX Skills

Free Agent Skills from [MailX](https://tools.themailx.com) that teach AI agents how to diagnose and fix email deliverability issues using the [MailX MCP server](https://tools.themailx.com/mcp).

> **What's a Skill?** A `SKILL.md` file with workflow instructions an AI agent loads on demand. The format is an open standard adopted by Anthropic (Claude) and OpenAI (Codex CLI, ChatGPT). Learn more at [agentskills.io](https://agentskills.io).

## Available Skills

### email-deliverability

Diagnoses and fixes email deliverability issues by checking SPF, DKIM, DMARC, BIMI records, blacklist status, and SMTP/IMAP server connectivity. Generates DNS records for email authentication.

**Activates when** the user asks about: email going to spam, domain reputation, DNS authentication setup, mail server configuration, sender verification, or email infrastructure troubleshooting.

## Installation

### Claude Code (recommended)

```shell
/plugin marketplace add Mailwarm/mailx-skills
/plugin install email-deliverability@mailx-skills
```

That's it. The skill activates automatically when relevant.

### Claude.ai

1. Download [`SKILL.md`](./plugins/email-deliverability/skills/email-deliverability/SKILL.md)
2. Go to **Claude.ai → Settings → Skills**
3. Upload the file and enable it

### Claude API

```bash
curl -X POST https://api.anthropic.com/v1/skills \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-beta: skills-2025-10-02" \
  -F "display_title=Email Deliverability" \
  -F "files[]=@plugins/email-deliverability/skills/email-deliverability/SKILL.md"
```

Then reference the returned `skill_id` when calling the Messages API.

## Requirements

The skill requires the [MailX MCP server](https://tools.themailx.com/mcp) for the actual tool execution. To install the MCP server in Claude Desktop / Cursor / Claude Code:

```json
{
  "mcpServers": {
    "mailx": {
      "type": "http",
      "url": "https://tools.themailx.com/mcp"
    }
  }
}
```

Full setup guide: [tools.themailx.com/mcp/docs](https://tools.themailx.com/mcp/docs)

## What the agent can do once installed

- **Audit a domain's email deliverability** — checks SPF, DKIM, DMARC, BIMI, and blacklists in one shot
- **Generate DNS records** — produces ready-to-paste SPF and DMARC records for any provider
- **Diagnose mail server issues** — tests SMTP and IMAP connections with credentials
- **Look up provider settings** — finds SMTP and IMAP configs for Gmail, Outlook, SendGrid, Mailgun, etc.

## Repository Structure

```
mailx-skills/
├── .claude-plugin/
│   └── marketplace.json                              ← Claude Code marketplace catalog
└── plugins/
    └── email-deliverability/
        ├── .claude-plugin/
        │   └── plugin.json                           ← Plugin manifest
        └── skills/
            └── email-deliverability/
                ├── SKILL.md                          ← The skill content
                └── references/
                    └── diagnosis.md                  ← Diagnosis decision tree
```

## Versioning

The current `SKILL.md` is dynamically generated from the live MailX tool catalog at [tools.themailx.com/skills/email-deliverability/SKILL.md](https://tools.themailx.com/skills/email-deliverability/SKILL.md). This repo is the static, versioned distribution snapshot used by Claude Code marketplace installs.

## License

MIT — see [LICENSE](./LICENSE)

## Links

- **MailX Tools**: [tools.themailx.com](https://tools.themailx.com)
- **MCP Integration Docs**: [tools.themailx.com/mcp/docs](https://tools.themailx.com/mcp/docs)
- **Live SKILL.md**: [tools.themailx.com/skills/email-deliverability/SKILL.md](https://tools.themailx.com/skills/email-deliverability/SKILL.md)
- **Skill discovery**: [tools.themailx.com/.well-known/skills.json](https://tools.themailx.com/.well-known/skills.json)
- **Agent Skills spec**: [agentskills.io](https://agentskills.io)
