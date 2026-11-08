# 8x8 MCP server

Inspect messaging account balance/status and explicitly approve one SMS in the correct account region.

A direct-provider MCP server with useful local execution. No PipMCP account is required for local use. Provider API access and any provider charges are your responsibility.

## Quick start

Requires Node.js22+. Clone this repository, then run:

```sh
npm ci --ignore-scripts
npm test
npm start
```

The default command inspects the tools offline. Configure your own credentials in your environment:

- `EIGHTX8_API_KEY`



Credential help: https://developer.8x8.com/connect/docs/authentication

## Execute a real read

Replace example record IDs with records you are authorized to access.

```sh
npm start -- --call eightx8_get_balance '{"accountId":"REPLACE_WITH_AUTHORIZED_ACCOUNT_ID"}'
```

## Use with an MCP client

Launch `node /absolute/path/8x8-mcp/server.mjs` using stdio transport; pass credentials through the client environment. Stdout is reserved for JSON-RPC.

## Tools

| Tool | Effect | Purpose |
| --- | --- | --- |
| eightx8_get_balance | Read | eightx8 get balance |
| eightx8_get_sms | Read | eightx8 get sms |
| eightx8_send_sms | Explicit approval required | Send one SMS to an explicitly authorized recipient. Default endpoint is Asia-Pacific; select the documented host for your account region. |

## Concrete workflow

Inspect messaging account balance/status and explicitly approve one SMS in the correct account region.

1. eightx8_get_balance
2. eightx8_get_sms
3. eightx8_send_sms

## Optional writes

Writes are disabled by default. Set CONNECTOR_ALLOW_WRITES=true and supply `_approval: {approved:true,idempotencyKey:"unique-request-id"}` on the specific call. Do not reuse a key for a different request. Uncertain outcomes are never automatically replayed; inspect the provider before another attempt.

## Capability evidence

Local: **Tested — HTTP fixtures and real stdio tools/call**. Managed: **Documented**. Provider authentication and live operations have not been established by these fixture tests. Evidence applies per operation and execution mode; setup discovery does not imply provider execution.

## Managed production

[Activate with PipMCP](https://pipmcp.com/github/start?app=8x8&integration=8x8-mcp&utm_source=github&utm_campaign=github-8x8-mcp&utm_content=readme) when you want the hosted service.  Local credentials are not uploaded by this code. Review provider permissions in the managed onboarding. [Directory and related projects](https://jannikco.github.io/pipmcp-connectors/integrations/8x8/).

## Provenance and maintenance

- https://raw.githubusercontent.com/8x8Cloud/public-developer-docs/master/docs_oas/connect/sms_api.json

Maintainer:jannikco. MIT license for this code. See connector.json for selected operations and integration.json for evidence. Runtime dependencies retain their own licenses. Report sensitive issues through https://pipmcp.com/contact; do not post credentials or customer data in public issues.
