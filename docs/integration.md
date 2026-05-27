# Integration Guide

How to integrate ZHAD0 privacy into your AI agent.

---

## Overview

ZHAD0 slots between your agent framework and Base L2. Your agent constructs intents as normal — ZHAD0 encrypts, proves, and relays them invisibly.

```
Your Agent
    │  intent payload
    ▼
@zhad0/sdk  ──  AES-256-GCM encryption   ← LIVE
                RISC Zero ZK proof        ← mainnet pending
                Ghost Relay submission    ← mainnet pending
    │
    ▼
Base L2
```

---

## Status

| Component | Package | Status |
|-----------|---------|--------|
| Core SDK | `@zhad0/sdk` | **Live** |
| ElizaOS plugin | `@zhad0/eliza-plugin` | **Live** |
| Virtuals Protocol adapter | `@zhad0/virtuals-adapter` | **Live** |
| ZK proofs (RISC Zero) | built into SDK | Coming — mainnet |
| Ghost Relay network | built into SDK | Coming — mainnet |

---

## Quick Start — Core SDK

```bash
npm install @zhad0/sdk
```

```ts
import { Zhad0Client } from "@zhad0/sdk";

const client = new Zhad0Client();

const receipt = await client.submitIntent({
  action:   "SWAP",
  tokenIn:  "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  tokenOut: "0x4200000000000000000000000000000000000006",
  amountIn: "1000000000",
});

console.log("Intent hash:", receipt.intentHash);
```

---

## ElizaOS

Dedicated plugin with action + provider wiring for ElizaOS agent runtimes.

**Repo:** [github.com/zhad0/zhad0-eliza](https://github.com/zhad0/zhad0-eliza)

```bash
npm install @zhad0/eliza-plugin @zhad0/sdk
```

```ts
import { AgentRuntime } from "@elizaos/core";
import { zhad0Plugin } from "@zhad0/eliza-plugin";

const agent = await AgentRuntime.create({
  plugins: [
    zhad0Plugin({
      network: "base-mainnet",
      relayerMode: "simulated",  // "ghost" once mainnet launches
    }),
  ],
});
```

### Action: `ZHAD0_SUBMIT_PRIVATE_INTENT`

```ts
await agent.triggerAction("ZHAD0_SUBMIT_PRIVATE_INTENT", {
  intent: {
    action:       "SWAP",
    tokenIn:      "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
    tokenOut:     "0x4200000000000000000000000000000000000006",
    amountIn:     "1000000000",
    amountOutMin: "0",
  },
});
```

The plugin also registers `zhad0StatusProvider`, which automatically injects live ZHAD0 protocol status into every agent context turn.

---

## Virtuals Protocol

Adapter for Virtuals `GameAgent` with one-line wrapping via `withPrivacy()`.

**Repo:** [github.com/zhad0/zhad0-virtuals](https://github.com/zhad0/zhad0-virtuals)

```bash
npm install @zhad0/virtuals-adapter @zhad0/sdk
```

### Option A: `withPrivacy()` — one-line wrap

```ts
import { GameAgent } from "@virtuals-protocol/game";
import { withPrivacy } from "@zhad0/virtuals-adapter";

const agent = new GameAgent(process.env.VIRTUALS_API_KEY!, {
  name: "my-trading-agent",
  goal: "Maximize yield on Base while protecting strategy",
});

// All agent.submitIntent() calls are now encrypted
const privateAgent = withPrivacy(agent, { network: "base-mainnet" });

await privateAgent.init();
```

### Option B: `zhad0Privacy` class

```ts
import { zhad0Privacy } from "@zhad0/virtuals-adapter";

const zhad0 = new zhad0Privacy({
  onEncrypt: (hash) => console.log("Encrypted:", hash),
});

const receipt = await zhad0.submitIntent({
  action:   "SWAP",
  tokenIn:  "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  tokenOut: "0x4200000000000000000000000000000000000006",
  amountIn: "1000000000",
});
```

---

## Generic Agent Wrap — `wrapAgent()`

If your framework is not listed above, use the core SDK's `wrapAgent` directly on any object with a `submitIntent` method:

```ts
import { wrapAgent, Zhad0Client } from "@zhad0/sdk";

const client = new Zhad0Client({ relayerMode: "simulated" });
const myAgent = new MyCustomAgent({ ... });

const privateAgent = wrapAgent(myAgent, client);

// All .submitIntent() calls are now encrypted automatically
await privateAgent.submitIntent({ action: "SWAP", ... });
```

---

## Coinbase AgentKit

```ts
import { wrapAgent, Zhad0Client } from "@zhad0/sdk";
import { AgentKit } from "@coinbase/agentkit";

const kit = new AgentKit({ ... });
const client = new Zhad0Client();
const privateKit = wrapAgent(kit, client);
```

---

## Low-Level: Encrypt Only

If you want to handle submission yourself:

```ts
import { encryptIntent, canonicalIntentHash } from "@zhad0/sdk";

const intent = {
  action:   "LP_ADD",
  tokenIn:  "0x...",
  amountIn: "5000000000",
};

const hash = await canonicalIntentHash(intent);
const encrypted = await encryptIntent(intent);
// encrypted.ciphertext  — hex AES-256-GCM ciphertext
// encrypted.iv          — hex 12-byte GCM nonce
// encrypted.intentHash  — same as hash above
```

---

## CLI Testing

```bash
npm install -g @zhad0/cli

# Protocol status
zhad0 status

# Encrypt a local intent file
echo '{"action":"SWAP","tokenIn":"0x83...","amountIn":"1000000000"}' > intent.json
zhad0 encrypt intent.json --pretty

# Full client-side simulation
zhad0 simulate intent.json --pretty
```

---

> **Live:** Encryption runs locally in your runtime (AES-256-GCM).
> Ghost Relay submission and on-chain execution are not yet live — receipts are
> client-side simulations until the Base mainnet launch.
> Track progress at [zhad0.io](https://zhad0.io).
