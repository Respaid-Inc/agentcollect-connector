# AgentCollect connector

AgentCollect is a managed accounts-receivable recovery service. This package
is a thin, portable Agent Plugins 1.0.0 connector that lets a signed-in
AgentCollect customer talk to their own AR analyst and open a read-only
Recovery Workspace from a compatible host (for example a ChatGPT-style
app directory or Cursor's plugin catalog).

The connector is a wrapper. It carries no credentials and runs no code of
its own. All answers come from the hosted MCP server at
`https://mcp.agentcollect.com/public-mcp`, scoped to the customer's
authenticated AgentCollect account.

## Setup

1. Install this connector into a compatible host that supports Agent
   Plugins 1.0.0 or the Cursor plugin format.
2. When prompted by the host, sign in to AgentCollect through the host's
   connector flow. The sign-in happens between the host and the hosted
   AgentCollect MCP server; no credentials pass through this package.
3. Ask an AR question in natural language, or ask the host to open the
   Recovery Workspace.

Nothing in this package stores tokens, cookies, or secrets.

## Tools exposed to the customer

This connector surfaces exactly two tools from the hosted MCP server. Both
are marked read-only and account-scoped for the signed-in customer.

| Tool | Arguments | What it returns |
| --- | --- | --- |
| `ask-ar-analyst` | `question` string (3 to 1000 characters) | An object whose `answer` string describes the account's AR state in plain English. |
| `open-recovery-workspace` | none | A static workspace descriptor with title, read-only description, suggested question shortcuts, and the analyst tool name. Returns no portfolio numbers. |

The connector reports what the server returns. It does not aggregate,
recompute, or re-rank. See `skills/ar-portfolio-review/SKILL.md` for the
grounding and refusal rules the connector follows.

## Scope and limits

- Account-scoped. The analyst only discusses the signed-in AgentCollect
  customer's own portfolio.
- Read-only from this connector's point of view. The two tools listed
  above do not send messages, issue refunds, trigger payments, modify
  debtor records, or reach other AgentCollect clients. This describes
  what the connector will do; it does not characterize the full
  AgentCollect platform, which includes additional non-public surfaces
  outside this package.
- No admin, staff, or cross-tenant surfaces are referenced or encouraged
  by this package.

## Compatibility (as intended, not host-tested)

This package is intended to work with hosts that accept the Agent
Plugins 1.0.0 portable manifest and MCP configuration, including hosts
that read a `.cursor-plugin/` overlay. Compatibility with any specific
host has not been tested as part of this release. Acceptance by any
particular marketplace or app directory is at that host's discretion.

## Files

- `plugin.json`. Portable Agent Plugins root manifest, with an
  `extensions.com.openai` interface block for hosts that use it.
- `mcp.json`. Streamable HTTP MCP server definition pointing at the
  canonical AgentCollect customer endpoint.
- `.cursor-plugin/plugin.json`. Optional Cursor compatibility overlay.
  Its `logo` and `mcpServers` paths are root-relative (resolved against
  the plugin root, as the Cursor plugins reference specifies) and contain
  no `..` segment.
- `skills/ar-portfolio-review/SKILL.md`. Onboarding skill with narrow
  when-to-use scope, call patterns, grounding rules, and refusals.
- `assets/logo.png`, `assets/composer-icon.png`. Brand assets.
- `LICENSE`. MIT license covering only this thin connector wrapper.

## Privacy, support, terms

- Privacy policy: https://www.agentcollect.com/privacy
- Terms of service: https://www.agentcollect.com/terms
- Support and MCP docs: https://www.agentcollect.com/docs/mcp
- Support email: support@agentcollect.com

## License

MIT. The license file in this package covers only this thin connector
wrapper (manifests, overlay, onboarding skill, and bundled brand assets).
It does not grant rights to the hosted AgentCollect MCP server, the
AgentCollect backend, or any other AgentCollect service.
