# MultiMail

Email infrastructure for AI agents. Every address publishes its operator, oversight mode, and reputation.

## The problem

When your agent sends email through a shared API, the recipient has no way to know who is responsible. No operator name. No oversight policy. No reputation history. The email looks the same whether it comes from a supervised assistant or an unsupervised bot.

Recipients cannot distinguish trustworthy agents from untrustworthy ones. So they treat all agent email the same: with suspicion.

## What MultiMail does

MultiMail gives each agent a real email address on a subdomain you control. Every outbound message carries a cryptographically signed identity header that proves who operates the agent, what oversight mode it runs under, and whether the operator has verified their identity.

Recipients can verify any agent email in one click. No plugins. No accounts. Just a link in the footer.

```
agent@yourcompany.multimail.dev
```

## Trust ladder

Agents start supervised and earn autonomy. The operator sets the oversight mode. The agent can request upgrades. The operator approves or denies.

| Mode | What happens |
|------|-------------|
| `gated_all` | Human approves every inbound and outbound message |
| `gated_send` | Human approves outbound only (default) |
| `monitored` | Agent sends freely, human can review |
| `autonomous` | Agent operates independently |

The mode is visible to recipients. An agent running under `gated_send` signals more accountability than one running `autonomous`. This is the mechanism that builds trust over time.

## Quick start

```bash
npx @multimail/mcp-server
```

Or connect to the hosted server with zero install:

```json
{
  "mcpServers": {
    "multimail": {
      "type": "url",
      "url": "https://mcp.multimail.dev/mcp"
    }
  }
}
```

Works with Claude Code, Claude Desktop, Cursor, Windsurf, ChatGPT Desktop, and any MCP client.

## How agents use it

Agents write email in markdown. MultiMail converts to formatted HTML for delivery. Incoming email arrives as clean markdown. No HTML parsing. No MIME decoding. 15x fewer tokens than raw email.

```
send_email
  to: ["partner@example.com"]
  subject: "Q3 report attached"
  markdown: "Hi, here's the quarterly report. Key highlights..."
```

Threading is automatic. Replies carry correct headers. Attachments work. Scheduling works.

28 tools cover the full email lifecycle: send, receive, reply, schedule, cancel, tag, search contacts, manage webhooks, check reputation.

## What the operator controls

The operator is the human accountable for the agent's email behavior. They receive a confirmation code at signup, set the oversight mode, and see every pending message when oversight is active.

Operators also control:

- Mailbox creation and deletion
- API key scopes (read, send, admin, oversight)
- Webhook subscriptions for real-time events
- Custom domains with verified DKIM and return-path
- Suppression lists and contact management

## Reputation

Every mailbox builds a sending reputation over time. Bounce rates, complaint rates, and rejection rates are computed daily and published (in anonymized band form) at a stable URL derived from the mailbox address.

Recipients and mail servers can check reputation before deciding to trust an agent's messages. High complaint rates trigger graduated enforcement: warning, restriction, suspension, termination. This is automatic.

## Verified identity

Every outbound email includes two custom headers:

- `X-MultiMail-Identity`: ECDSA P-256 signed payload containing operator name, oversight mode, creation date, and verification status
- `X-MultiMail-Reputation`: HMAC hash linking to the mailbox's published reputation

The signature is verifiable against MultiMail's public signing key. No trust-on-first-use. No centralized lookup required after the first verification.

## Formal proofs

Three properties of the authorization model are mechanically verified using Lean 4 and checked in CI on every push:

1. **Tenant isolation**: every resource-accessing endpoint checks tenant ownership
2. **Scope enforcement**: every authenticated endpoint has a scope guard
3. **Read immutability**: read-scoped keys cannot trigger outbound actions

The proof model covers 89 API endpoints.

## Pricing

| Plan | Price | Mailboxes | Emails/month | Storage |
|------|-------|-----------|-------------|---------|
| Starter | Free | 2 | 200 | 100 MB |
| Builder | $9/mo | 5 | 5,000 | 10 GB |
| Pro | $29/mo | 25 | 30,000 | 50 GB |
| Scale | $99/mo | 100 | 150,000 | 150 GB |

## Links

- [Website](https://multimail.dev)
- [MCP Server](https://github.com/multimail-dev/mcp-server)
- [API Documentation](https://api.multimail.dev/v1/openapi.json)
- [Terms of Service](https://multimail.dev/terms)
- [Acceptable Use Policy](https://multimail.dev/acceptable-use)
