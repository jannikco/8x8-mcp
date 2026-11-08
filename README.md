# 8x8 MCP setup and connection checker

Generate 8x8 MCP client configuration, inspect available tools and validate hosted endpoint access through PipMCP.

Maintainer: jannikco · License: MIT · Verification: **mock-only**

## Quick start

Requires Node.js 22 or later.

```sh
npm ci
npm test
npm start
```

The default example runs locally without credentials and makes no external changes. Verification status describes recorded evidence; local configuration tests do not establish that provider authentication or execution works.

## Connect 8x8

[Create or sign in to PipMCP](https://pipmcp.com/register?app=8x8&utm_source=github&utm_campaign=github-8x8-mcp) and select the integration. Connect your authorized provider account and copy its hosted MCP endpoint. Set PIPMCP_ENDPOINT and, if required, PIPMCP_TOKEN in your environment. Run npm start to produce client configuration; run npm start -- --live for a read-only connection check. Provider app presence is catalog evidence, not live verification.

## Managed service

[Continue with PipMCP](https://pipmcp.com/register?app=8x8&utm_source=github&utm_campaign=github-8x8-mcp). This repository is maintained by the integration team and does not imply endorsement by LinkedIn or other providers.

## Provenance and support

Source revision: cfe168309f7a0e2663209a182eb2059ec2e433105f676ff860966f92b62a9183. The complete manifest is in integration.json; SHA256SUMS covers the reviewed artifact. Open a repository issue without credentials or personal data. See SECURITY.md for sensitive reports. No telemetry is sent by this code; signup links include explicit campaign parameters.

## Provider setup inventory

Run `npm start -- --setup` to inspect this app's recorded authentication requirements and setup links offline. Credentials are entered in PipMCP; this repository lists required field names only.

```json
{
  "type": "bearer",
  "requiredCredentialFields": [
    "token"
  ],
  "requiredHeaderNames": [],
  "helpUrl": "https://developer.8x8.com/connect/docs/authentication",
  "tenantRequired": true,
  "accountParameterNames": [
    "sub_account_id",
    "account_id"
  ]
}
```

Provider documentation: https://raw.githubusercontent.com/8x8Cloud/public-developer-docs/master/docs_oas/connect/sms_api.json
Provider website: https://www.8x8.com/
