# API Reference

Base URL: `https://zhad0.io/api`  
All responses are JSON. No authentication required.

---

## Health

### `GET /api/healthz`

```json
{ "status": "ok" }
```

---

## Protocol Stats

### `GET /api/stats`

Live protocol statistics.

```json
{
  "totalRelayers": 12,
  "activeRelayers": 10,
  "totalIntentsProcessed": 48291,
  "totalFeesEth": "4.823100",
  "totalStakedZhad0": "1200000",
  "zkProofsGenerated": 48291,
  "avgRelayTimeMs": 187,
  "uptimePct": 99.7
}
```

---

## Relayers

### `GET /api/relayers`

List all Ghost Relayers, ordered by registration date.

```json
[
  {
    "id": "relayer-001",
    "operatorAddress": "0x...",
    "stakedZhad0": "50000",
    "status": "active",
    "uptimePct": 99.9,
    "intentsRelayed": 12400,
    "earningsEth": "1.24000000",
    "region": "US-EAST",
    "registeredAt": "2026-01-15T00:00:00.000Z"
  }
]
```

`status` enum: `active` | `inactive` | `slashed`

### `GET /api/relayers/:id`

Single relayer by ID. Returns `404` if not found.

---

## Intents

### `GET /api/intents/feed`

Last 50 anonymized intent activities, newest first.

```json
[
  {
    "id": "0xabc...",
    "proofHash": "0xdef...",
    "agentFramework": "Virtuals",
    "relayedAt": "2026-05-27T12:00:00.000Z",
    "feeEth": "0.00012500",
    "status": "confirmed",
    "relayerRegion": "EU-WEST"
  }
]
```

`agentFramework` enum: `Virtuals` | `Eliza` | `AgentKit` | `Unknown`  
`status` enum: `confirmed` | `pending` | `failed`

### `GET /api/intents/stats`

Aggregated statistics by agent framework and status.

```json
{
  "byFramework": [
    { "framework": "Virtuals", "count": 18200, "pct": 37.7 },
    { "framework": "Eliza",    "count": 15000, "pct": 31.0 }
  ],
  "byStatus": [
    { "status": "confirmed", "count": 46900, "pct": 97.1 }
  ],
  "last24hIntents": 2100,
  "last24hFeesEth": "0.210000"
}
```

---

## Chain

### `GET /api/chain`

Base chain metadata.

```json
{
  "name": "Base",
  "chainId": 8453,
  "rpcUrl": "https://mainnet.base.org",
  "explorerUrl": "https://basescan.org",
  "nativeCurrency": { "name": "Ether", "symbol": "ETH", "decimals": 18 },
  "isL2": true,
  "settlementChain": "Ethereum Mainnet",
  "blockTime": 2,
  "contractAddress": "0xZHAD0000000000000000000000000000000000001"
}
```

---

## Token

### `GET /api/token`

ZHAD0 token info, distribution, vesting schedule, and protocol phase status.

```json
{
  "symbol": "ZHAD0",
  "name": "ZHAD0 Protocol Token",
  "totalSupply": "1000000000",
  "circulatingSupply": "0",
  "holders": 0,
  "contractAddress": null,
  "distribution": [
    { "label": "Relayer Rewards", "pct": 35, "amount": "350000000", "color": "#e83c87" },
    { "label": "Ecosystem & Dev", "pct": 25, "amount": "250000000", "color": "#a855f7" }
  ],
  "vestingSchedule": [ ... ],
  "phaseStatus": [
    { "phase": 1, "label": "Protocol Design", "status": "completed", "description": "..." },
    { "phase": 2, "label": "Testnet Alpha",   "status": "active",    "description": "..." }
  ]
}
```

---

## OpenAPI Spec

Full OpenAPI 3.1 spec available at [`zhad0/zhad0-api`](https://github.com/zhad0/zhad0-api/blob/main/openapi.yaml).

---

Website: [zhad0.io](https://zhad0.io)
