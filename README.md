# Taste

Expert review for AI agents. On-chain proof of human review.

This is the public manifest repository for the Taste MCP server. The server is
hosted — there is nothing to install. `server.json` is the discovery manifest
consumed by MCP registries; the server source is maintained privately.

## what

Pay-per-call human expert evaluation over MCP + x402. Vetted humans review an
AI agent's output across any media (text, images, video, audio, social posts),
audit reasoning chains, run prepublish safety checks, and gate high-stakes
actions for human approval. Approved outputs receive a Taste content
certificate recorded on Base — any downstream agent can verify the review.

## connect

```
MCP endpoint:  https://api.humantaste.app/mcp/
Transport:     Streamable HTTP (MCP 2025-06-18)
Payment:       x402, USDC on Base mainnet, $1.00 per paid call
Free tools:    list_offerings, list_expert_profiles, get_result, verify_certificate
Typical wait:  5–30 min after a paid call
```

No authentication, no API key. Paid tools answer an unauthenticated call with
HTTP 402; sign an EIP-3009 USDC transfer and retry.

## tools

Free (no payment, no session):

| Tool | Purpose |
|---|---|
| `list_offerings` | list offerings, prices, certificate flag, operating-hours state |
| `list_expert_profiles` | aggregate availability + estimated response time, no PII |
| `get_result` | poll session status, returns the full deliverable when complete |
| `verify_certificate` | read-only Taste content certificate lookup on Base, rate-limited 60/min/IP |

Paid ($1.00 each, x402):

| Tool | Purpose |
|---|---|
| `verify_external_source` | verify whether an external source, project, or claim is trustworthy |
| `review_content` | review AI-generated content for hallucinations, factual errors, domain mistakes — issues a certificate |
| `verify_reasoning_chain` | audit a step-by-step reasoning chain for mid-chain errors — issues a certificate |
| `prepublish_review` | review content for cultural sensitivity, brand safety, derivative risk before publishing — issues a certificate |
| `predict_audience_reaction` | a human matching a target demographic rates and reacts to content |
| `arbitrate_dispute` | an impartial human arbiter decides whether a deliverable meets a contract |
| `request_human_approval` | pause a workflow for explicit human approval before an irreversible action |

The live tool list is authoritative — `server.json` sets `tools: "dynamic"`,
so MCP clients call `tools/list` against the endpoint for the current surface.

## example

```bash
curl -X POST https://api.humantaste.app/mcp/ \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call",
       "params":{"name":"list_offerings","arguments":{}}}'
```

Paid calls return HTTP 402 with an `X-Payment-Requirements` header (base64
JSON). Sign EIP-3009 `transferWithAuthorization` for USDC on Base, then retry
the same request with an `X-Payment` header.

## compliance

EU AI Act Article 50(2) exempts AI-generated content from mandatory
AI-disclosure labels when it has undergone human review or editorial control.
Taste content certificates are the on-chain audit trail for that exemption.

## links

- One-pager for agents: https://api.humantaste.app/llms.txt
- Full integration reference: https://api.humantaste.app/llms-full.txt
- MCP Server Card: https://api.humantaste.app/.well-known/mcp.json
- Paid-resources catalog: https://api.humantaste.app/.well-known/x402
- Brand: https://humantaste.app
