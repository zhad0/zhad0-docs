# Integration Guide

How to integrate ZHAD0 privacy into your AI agent.

---

## Overview

ZHAD0 slots between your agent framework and Base L2. Your agent constructs intents as normal — ZHAD0 encrypts, proves, and relays them invisibly.

```
Your Agent
    │  intent payload
    ▼
@zhad0/sdk  ──  AES-256-GCM encryption
                RISC Zero ZK proof (simulated)
                Ghost Relay submission (simulated)
    │
    ▼
Base L2 (planned)
```

---

## Quick Start

```bash
npm install @zhad0/sdk
```

```ts
import { Zhad0Client } from "@zhad0/sdk";

const client = new Zhad0Client();

const receipt = await client.submitIntent({
  action:      "SWAP",
  tokenIn:     "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  tokenOut:    "0x4200000000000000000000000000000000000006",
  amountIn:    "1000000000",
  slippageBps: 50,
  deadline:    Math.floor(Date.now() / 1000) + 300,
});

console.log("Intent hash:", receipt.intentHash);
```

---

## Wrapping an Existing Agent

If your agent already has a `submitIntent` method, use `wrapAgent` to add privacy with zero refactoring:

```ts
import { wrapAgent } from "@zhad0/sdk";

// Before: plain agent
const agent = new MyAgentKitAgent({ ... });

// After: privacy-wrapped
const privateAgent = wrapAgent(agent);

// All calls are now encrypted automatically
await privateAgent.submitIntent({ action: "SWAP", ... });
```

---

## Virtuals Protocol

```ts
// planned — SDK v1 target (Q4 2026)
import { wrapAgent } from "@zhad0/sdk";
import { VirtualsAgent } from "@virtuals-protocol/agent";

const agent = new VirtualsAgent({ ... });
const privateAgent = wrapAgent(agent);
```

---

## Eliza

```ts
// planned — SDK v1 target (Q4 2026)
import { wrapAgent } from "@zhad0/sdk";
import { ElizaAgent } from "@elizaos/eliza";

const agent = new ElizaAgent({ ... });
const privateAgent = wrapAgent(agent);
```

---

## Coinbase AgentKit

```ts
// planned — SDK v1 target (Q4 2026)
import { wrapAgent } from "@zhad0/sdk";
import { AgentKit } from "@coinbase/agentkit";

const agent = new AgentKit({ ... });
const privateAgent = wrapAgent(agent);
```

---

## Low-Level: Encrypt Only

If you want to handle submission yourself:

```ts
import { encryptIntent, canonicalIntentHash } from "@zhad0/sdk";

const intent = {
  action:      "LP_ADD",
  tokenIn:     "0x...",
  tokenOut:    "0x...",
  amountIn:    "5000000000",
};

// Get the canonical hash for on-chain reference
const hash = await canonicalIntentHash(intent);

// Encrypt for relay
const encrypted = await encryptIntent(intent);
// encrypted.ciphertext — hex AES-256-GCM ciphertext
// encrypted.iv         — hex 12-byte GCM nonce
// encrypted.intentHash — matches hash above
```

---

## CLI Testing

```bash
npm install -g @zhad0/cli

# Check protocol status
zhad0 status

# Encrypt a local intent file
echo '{"action":"SWAP","tokenIn":"0x83...","tokenOut":"0x42...","amountIn":"1000000000"}' > intent.json
zhad0 encrypt intent.json --pretty

# Full client-side simulation
zhad0 simulate intent.json --pretty
```

---

> **Live Note:** `submitIntent` currently returns a simulated receipt.
> Ghost Relay submission and Base execution are not yet live.
> Track progress at [zhad0.io](https://zhad0.io).
