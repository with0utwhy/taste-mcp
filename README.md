# Taste

Expert review for AI agents. On-chain proof of human review.

This is the public manifest repository for the Taste MCP server. The server is
hosted — there is nothing to install. `server.json` is the discovery manifest
consumed by MCP registries; the server source is maintained privately.

## what

Pay-per-call human work for AI agents over MCP + x402. Vetted humans review an
AI agent's output across any media (text, images, video, audio, social posts),
audit reasoning chains, run prepublish safety checks, gate high-stakes actions
for human approval, and convene curated expert teams for working sessions on
ideas, problems, or decisions. Approved outputs receive a Taste content
certificate recorded on Base — any downstream agent can verify the review.

## connect

```
MCP endpoint:  https://api.humantaste.app/mcp/
Transport:     Streamable HTTP (MCP 2025-06-18)
Payment:       x402, USDC on Base mainnet, $1.00–$15.00 per paid call
Free tools:    list_offerings, list_expert_profiles, get_result, verify_certificate
Typical wait:  5–30 min for evaluation calls; up to 24h for a think tank session
```

No authentication, no API key. Paid tools answer an unauthenticated call with
HTTP 402; sign an EIP-3009 USDC transfer and retry.

## tools

Free (no payment, no session):

| Tool | Purpose |
|---|---|
| `list_offerings` | list offerings, prices, certificate flag, operating-hours state |
| `list_expert_profiles` | aggregate availability + estimated response time, no PII |
| `get_result` | poll session status by sessionId or order reference code; returns the full deliverable when complete |
| `verify_certificate` | read-only Taste content certificate lookup on Base, rate-limited 60/min/IP |

Paid (x402 — USDC on Base):

| Tool | Price | Purpose |
|---|---|---|
| `verify_external_source` | $1.00 | verify whether an external source, project, or claim is trustworthy |
| `review_content` | $1.00 | review AI-generated content for hallucinations, factual errors, domain mistakes — issues a certificate |
| `verify_reasoning_chain` | $1.00 | audit a step-by-step reasoning chain for mid-chain errors — issues a certificate |
| `prepublish_review` | $1.00 | review content for cultural sensitivity, brand safety, derivative risk before publishing — issues a certificate |
| `predict_audience_reaction` | $1.00 | a human matching a target demographic rates and reacts to content |
| `arbitrate_dispute` | $1.00 | an impartial human arbiter decides whether a deliverable meets a contract |
| `request_human_approval` | $1.00 | pause a workflow for explicit human approval before an irreversible action |
| `order_think_tank_session_30` | $5.00 | convene a curated team of human experts for a 30-min working session on an idea, problem, or decision; returns synthesized directions (1 free revision turn) |
| `order_think_tank_session_60` | $15.00 | as above, 60-min session (2 free revision turns) |

Each think tank order returns a `TASTE-XXXX-XXXX-XXXX` reference code. Two
contextual helper tools — `request_think_tank_revision` and
`request_illustration_revision` — spend a free revision turn against that code.

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
