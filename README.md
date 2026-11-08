# 8x8 communication workflows for AI assistants

Inspect messaging account balance/status and explicitly approve one SMS in the correct account region.

[Start locally](#get-started) · [Connect your assistant](#connect-your-assistant) · [Available tools](#available-tools) · [Hosted PipMCP setup](#use-pipmcp-for-production)

## Built for useful workflows

- **Build a 8x8 brief.** Eightx8 get balance. Make that provider response available to your assistant before it proposes a next action.
- **Investigate a chosen 8x8 result.** Eightx8 get sms. Use an account or record returned by your provider rather than guessing an identifier.
- **Add a reviewed 8x8 action.** Send one SMS to an explicitly authorized recipient. Default endpoint is Asia-Pacific; select the documented host for your account region. Review the input first; execution requires operator opt-in and per-call approval.

## Why use this package?

Get a focused 8x8 MCP integration without assembling request mappings and a stdio transport yourself. The package provides documented input schemas, provider authentication handling and an example CLI, with the same tools available to your assistant.

Reads are available independently of writes. Approved changes use operator opt-in and a per-call idempotency key; uncertain writes are not silently replayed.

## Get started

**Requirements:** Node.js 22 or newer, npm, and access to the provider account you intend to use.

```sh
git clone https://github.com/jannikco/8x8-mcp.git
cd 8x8-mcp
npm ci --ignore-scripts
npm test
npm start
```

`npm start` shows the tool inventory and workflow offline. It does not authenticate or call the provider. The tests use provider fixtures and stdio transport; they do not modify your account.

### Configure your provider account

The current release connects directly to the provider with **your own credentials**. It does not require a PipMCP key for local execution. Configure the environment of the process or MCP client that runs the server; `.env` files are not loaded automatically.

| Environment variable | What to supply |
| --- | --- |
| `EIGHTX8_API_KEY` | Your own provider API key or access token. |

In the 8x8 Connect dashboard go to API Keys (connect.8x8.com/messaging/api-keys) and click Create API Key, then copy it. Also enter your sub-account ID, and the SMS host for your region: sms.8x8.com (Asia-Pacific), sms.us.8x8.com (North America), sms.8x8.uk (Europe) or sms.8x8.id (Indonesia).

[Provider credential/reference documentation](https://developer.8x8.com/connect/docs/authentication). Keep credentials out of commits, issue reports and shared client configuration.

### Run your first provider read

After configuring the environment, run `eightx8_get_balance`:

```sh
npm start -- --call eightx8_get_balance '{"accountId":"REPLACE_WITH_AUTHORIZED_ACCOUNT_ID"}'
```

Replace sample account, record, file and project IDs with values from your authorized account. This command makes a real provider request and returns its result for you to inspect.

## Available tools

| Capability | MCP tool | Effect | Required inputs |
| --- | --- | --- | --- |
| Eightx8 get balance. | `eightx8_get_balance` | Read | `accountId` |
| Eightx8 get sms. | `eightx8_get_sms` | Read | `subAccountId`, `umid` |
| Send one SMS to an explicitly authorized recipient. Default endpoint is Asia-Pacific; select the documented host for your account region. | `eightx8_send_sms` | Approved write | `subAccountId`, `source`, `destination`, `text` |

Tool schemas and bindings are in [connector.json](./connector.json). These are the selected operations this package ships.

### A practical tool sequence

1. Use `eightx8_get_balance` — Eightx8 get balance.
2. Use `eightx8_get_sms` — Eightx8 get sms.
3. Use `eightx8_send_sms` — Send one SMS to an explicitly authorized recipient. Default endpoint is Asia-Pacific; select the documented host for your account region.

## Connect your assistant

Add this stdio server to an MCP-compatible client. Replace the absolute path and credential placeholders. Use your client’s secret/environment controls instead of committing this configuration.

```json
{
  "mcpServers": {
    "8x8-mcp": {
      "command": "node",
      "args": [
        "/absolute/path/8x8-mcp/server.mjs"
      ],
      "env": {
        "EIGHTX8_API_KEY": "YOUR_EIGHTX8_API_KEY"
      }
    }
  }
}
```

`node server.mjs` starts the MCP server; `npm start` runs the example CLI. The server reserves stdout for MCP messages. If the client does not inherit your shell environment, pass the required variables explicitly.

## Review changes before running writes

The write tool is disabled by default. Enable `CONNECTOR_ALLOW_WRITES=true` in the server process and include this approval object in the **specific** write call after reviewing its arguments:

```json
{
  "_approval": {
    "approved": true,
    "idempotencyKey": "unique-reviewed-request-id"
  }
}
```

This object is added alongside the tool’s own required fields; it is not a complete write request. Use a new idempotency key for a different action. If an outcome is uncertain, inspect the provider before trying again. Creating a record may trigger existing provider automations even when this package does not send a message itself.

## Use PipMCP for production

The local package is useful when you want to run these selected 8x8 operations in your own process. PipMCP provides a hosted place to configure provider connections, publish tools, issue endpoint access tokens, apply server-side policy and approvals, and inspect execution/usage records. Provider credentials and hosted execution are configured in that service.

**[Set up 8x8 in PipMCP](https://pipmcp.com/github/start?app=8x8&integration=8x8-mcp&utm_source=github&utm_campaign=github-8x8-mcp&utm_content=readme)**

A managed catalog template is listed, but that is not evidence that its tools match this repository or that your account is connected. Complete provider setup and inspect the endpoint’s actual tool inventory and permissions before use.

Local credentials are not uploaded by this repository. A hosted endpoint uses its own PipMCP endpoint access token and published tool inventory; the local and hosted credentials are separate.

## Scope and validation

- The installed package has HTTP request fixtures and MCP stdio `tools/call` tests.
- Fixture success checks client behavior; it does not establish provider authentication, paid-plan access or live account execution.
- No live provider execution is claimed for this package by these tests.
- Hosted tool names, schemas, permissions and availability can differ from this local package. A queued or accepted job/message is not proof of completion or delivery. Provider API plans, account rules and limits apply.
- See [integration.json](./integration.json) for operation-scoped evidence and the provider-specific limits above.

## FAQ

**Do I need a 8x8 account?**

You need the provider account, token and permissions required by these operations. Provider charges and API limits remain with that account.

**Does this version require a PipMCP API key?**

This package uses your provider credentials for local execution. To use the hosted service, configure the provider connection in PipMCP and use the endpoint access token issued there.

**Why is a tool missing or a request rejected?**

This repository exposes the selected tools above. Check the required environment variables, provider scopes, account/region requirements and input IDs first. Hosted endpoints have their own published inventory and may exclude tools by role or policy. Do not retry a failed write without checking its outcome.

**Is this an official provider SDK or a hosted-service guarantee?**

This is an independently maintained MCP integration. Its fixtures and source references do not imply provider endorsement, unrestricted API access, or identical hosted behavior.

## Guides and related workflows

- [8x8 integration guide](https://jannikco.github.io/pipmcp-connectors/integrations/8x8/)
- [8x8 workflow and tool sequence](https://jannikco.github.io/pipmcp-connectors/workflows/8x8/)
- [More integration workflows](https://jannikco.github.io/pipmcp-connectors/workflows/)
- [Typed LeadAdapter API client](https://github.com/jannikco/linkedin-typescript-sdk)
- [LeadAdapter Python API client](https://github.com/jannikco/linkedin-python-sdk)
- [LinkedIn API quickstart](https://github.com/jannikco/linkedin-api)
- [LinkedIn people and post search](https://github.com/jannikco/linkedin-search-api)
- [LinkedIn post engagers](https://github.com/jannikco/linkedin-post-engagers)
- [LeadAdapter LinkedIn MCP server](https://github.com/jannikco/linkedin-mcp)
- [OpenAPI to MCP import starter](https://github.com/jannikco/openapi-to-mcp)
- [Swagger to MCP import starter](https://github.com/jannikco/swagger-to-mcp)
- [8x8 API and credential reference](https://developer.8x8.com/connect/docs/authentication)
- [Additional official source 2](https://raw.githubusercontent.com/8x8Cloud/public-developer-docs/master/docs_oas/connect/sms_api.json)

Maintained by [jannikco](https://github.com/jannikco). MIT for this repository’s code; dependencies retain their licenses. [Report a reproducible issue](https://github.com/jannikco/8x8-mcp/issues) with redacted input and error details. For credentials or security issues, use [private contact](https://pipmcp.com/contact).
