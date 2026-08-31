## GBLIN Protocol

**Proof you can check, instead of claims you have to trust.**

Everything on this page answers on the open internet. No key, no wallet, no account.

### Check us in ten seconds

```bash
curl -s https://gblin-mcp.gblin-mcp-worker.workers.dev/log/checkpoint
```

That is a signed checkpoint of an append-only log of AI actions ([RFC 6962](https://datatracker.ietf.org/doc/html/rfc6962)).
It carries **two** signature lines. The second one is not ours — an independent witness cosigns it, which is
what makes "append-only" something other than our word for it. The tree root is anchored once a day on Base
as an EAS attestation.

Rebuild the tree yourself and check we did not rewrite anything:

```bash
B=https://gblin-mcp.gblin-mcp-worker.workers.dev
curl -s "$B/log/leaves?start=0&end=20"        # the raw records
curl -s "$B/log/consistency?old=20&new=51"    # append-only proof between two sizes
curl -s "$B/log/proof/17"                     # inclusion proof for one leaf
```

There is a zero-dependency offline verifier in
[`verify-receipt.mjs`](https://github.com/gblinproject/gblin-treasury-risk-regime/blob/main/verify-receipt.mjs).
It re-derives everything from the receipt JSON and never calls our server.

### What we run

| | |
|---|---|
| [gblin-treasury-risk-regime](https://github.com/gblinproject/gblin-treasury-risk-regime) | Market risk regime for AI agents — calm / elevated / crash, free to read. Plus the receipts log, the witness, the MCP server and the edge worker. |
| [GBLIN-Protocol](https://github.com/gblinproject/GBLIN-Protocol) | Reserve-backed cbBTC + WETH + USDC index token on Base, minted and redeemed at NAV. Owner is a 48-hour timelock. |
| [GBLIN_WEBAPP](https://github.com/gblinproject/GBLIN_WEBAPP) | [gblin.digital](https://gblin.digital) and the x402 paid endpoints. |
| [GBLIN_PLUGIN](https://github.com/gblinproject/GBLIN_PLUGIN) | ElizaOS plugin, published on npm as `plugin-gblin`. |
| [base-heartbeat-bo](https://github.com/gblinproject/base-heartbeat-bo) | The keeper bot that pokes the contract so weights refresh. |
| [Whitepaper](https://github.com/gblinproject/Whitepaper) | Design papers. Historical by declaration — the chain wins over all of them. |

### For agents

```bash
npx -y @gblin-protocol/mcp-server     # 13 tools over stdio
```

Hosted, streamable HTTP — 8 tools, 4 resources, 2 prompts:
`https://gblin-mcp.gblin-mcp-worker.workers.dev/mcp`

Machine-readable descriptions: [llms.txt](https://gblin.digital/api/x402/llms.txt) ·
[.well-known/x402](https://gblin.digital/.well-known/x402) ·
[agent-card.json](https://gblin.digital/.well-known/agent-card.json)

Reading is free and stays free. Payment buys a **signed** answer you can hand to someone else.

### Things we will not say

We do not say **audited**. There is a Slither run on the deployed contract with its result published
(0 critical, 0 high) and no manual review, and we say so in the same sentence.

We do not say **immutable**. A 48-hour timelock owns the contract and can change parameters. Every one
of those changes is scheduled in public before it executes.

A receipt does not prove an AI action happened. It proves a record existed in this log at that position
and time, and that the log has not been rewritten since. The sealer supplies the hashes, and the receipt
says so in its own payload.

We publish a public uptime promise and attest it on-chain. On 22 August we broke it ourselves for three
minutes with a bad routing rule. The counter recorded the violation, the incident note is public, and
neither has been quietly cleaned up. That is the whole point.
