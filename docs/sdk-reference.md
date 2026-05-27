# SDK Reference — `@zhad0/sdk`

> **Design Preview.** Client-side AES-256-GCM encryption is real.
> ZK proofs and Ghost Relay submission are simulated until mainnet.

Install:

```bash
npm install @zhad0/sdk
```

---

## Zhad0Client

Main entry point for interacting with the protocol.

```ts
import { Zhad0Client } from "@zhad0/sdk";

const client = new Zhad0Client();
```

### `client.submitIntent(intent)`

Encrypts an intent, generates a (simulated) ZK proof, and returns a relay receipt.

```ts
const receipt = await client.submitIntent({
  action: "SWAP",
  tokenIn:  "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913", // USDC on Base
  tokenOut: "0x4200000000000000000000000000000000000006", // WETH on Base
  amountIn: "1000000000",
  slippageBps: 50,
  deadline: Math.floor(Date.now() / 1000) + 300,
});

console.log(receipt.intentHash);  // 0x...
console.log(receipt.status);      // "SIMULATED_OK"
```

**Parameters**

| Field | Type | Description |
|---|---|---|
| `action` | `string` | Intent type: `SWAP`, `BRIDGE`, `LP_ADD`, `LP_REMOVE` |
| `tokenIn` | `string` | Input token address |
| `tokenOut` | `string` | Output token address |
| `amountIn` | `string` | Amount in wei |
| `slippageBps` | `number` | Max slippage in basis points |
| `deadline` | `number` | Unix timestamp |

**Returns:** `Promise<SubmitReceipt>`

```ts
type SubmitReceipt = {
  intentHash:  string;   // SHA-256 of canonical intent
  encryptedBlob: string; // AES-256-GCM ciphertext (hex)
  proof:       ZKProof;  // RISC Zero proof (simulated)
  relayStatus: "SIMULATED_OK" | "RELAY_PENDING" | "CONFIRMED";
  txHash:      string | null;
  relayedAt:   number;   // Unix ms
};
```

---

## `wrapAgent(agent, client?)`

Transparently wraps any agent object to encrypt all `submitIntent` calls.

```ts
import { wrapAgent, Zhad0Client } from "@zhad0/sdk";

const myAgent = { submitIntent: async (i) => { /* ... */ } };
const privateAgent = wrapAgent(myAgent, new Zhad0Client());

// All calls to privateAgent are now encrypted
const receipt = await privateAgent.submitIntent({ action: "SWAP", ... });
```

---

## `encryptIntent(intent)`

Low-level: encrypt an intent without submitting.

```ts
import { encryptIntent } from "@zhad0/sdk";

const result = await encryptIntent({
  action: "SWAP",
  tokenIn: "0x...",
  tokenOut: "0x...",
  amountIn: "1000000000",
});

console.log(result.ciphertext);     // 0x...
console.log(result.iv);             // 0x... (12-byte GCM nonce)
console.log(result.keyFingerprint); // 0x... (8-byte)
console.log(result.intentHash);     // SHA-256 canonical hash
```

---

## `canonicalIntentHash(intent)`

Compute the canonical SHA-256 hash of an intent (deterministic over key-sorted JSON).

```ts
import { canonicalIntentHash } from "@zhad0/sdk";

const hash = await canonicalIntentHash({
  action: "SWAP",
  tokenIn: "0x...",
  tokenOut: "0x...",
  amountIn: "1000000000",
});

console.log(hash); // 0x...
```

---

## CLI — `zhad0`

The `@zhad0/cli` package ships the `zhad0` binary.

```bash
npm install -g @zhad0/cli

zhad0 status               # Protocol phase and capability matrix
zhad0 version              # Print version
zhad0 encrypt intent.json  # Encrypt an intent file
zhad0 simulate intent.json # Full client-side dry run
```

See [`zhad0-sdk`](https://github.com/zhad0/zhad0-sdk) for full CLI docs.

---

## Status Flags

All SDK responses include a `DESIGN_PREVIEW` notice until mainnet:

| Capability | Status |
|---|---|
| Client-side AES-256-GCM encryption | **LIVE** |
| `wrapAgent()` | **LIVE** |
| Canonical intent hash | **LIVE** |
| ZK proof generation | **SIMULATED** |
| Ghost Relay submission | **SIMULATED** |
| Base mainnet execution | **PLANNED** |
| Gasless mode | **PLANNED** |

---

Website: [zhad0.io](https://zhad0.io) | GitHub: [zhad0/zhad0-sdk](https://github.com/zhad0/zhad0-sdk)
