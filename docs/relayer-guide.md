# Ghost Relayer Guide

> **Status:** Relayer software and staking contracts are not yet released.
> This guide describes the planned v1 operator experience.

---

## Overview

A Ghost Relayer is a staked node operator that:

1. Receives encrypted intent blobs from the ZHAD0 queue
2. Validates ZK proofs on-chain (Base L2)
3. Submits execution transactions to Base
4. Earns 60% of every relay fee in ETH

Relayers **never see intent contents** — threshold decryption requires 2-of-3 relayer cooperation and only occurs at the moment of Base submission.

---

## Requirements

| Resource | Minimum |
|---|---|
| Compute | 2 vCPU / 4 GB RAM |
| Storage | 40 GB SSD |
| Network latency | < 100 ms global |
| Minimum stake | 10,000 ZHAD0 |

---

## Expected Economics

Based on projected v1 network volume:

| Metric | Estimate |
|---|---|
| Relay fee share | 60% of each intent fee (ETH) |
| Staking pool share | 20% pro-rata (if staking > minimum) |
| Expected APR | 15-25% denominated in ETH |
| Stake lockup | None (withdrawal with 7-day unbonding) |

*These are design targets, not guaranteed returns. No fees or rewards are active today.*

---

## Planned Setup (Post-Launch)

### Step 1 — Clone and Install

```bash
git clone https://github.com/zhad0/zhad0-relayer
cd zhad0-relayer
npm install
```

### Step 2 — Configure

```bash
cp .env.example .env
```

```env
RPC_URL=https://mainnet.base.org
PRIVATE_KEY=0x...
NODE_NAME=my-ghost-relay
```

### Step 3 — Run Locally

```bash
npm run dev

# Verify:
curl http://localhost:8080/health
# { "status": "ok" }
```

### Step 4 — Deploy to Railway (recommended)

```bash
npm install -g @railway/cli
railway login
railway init
railway up
```

### Step 5 — Register On-Chain

```bash
zhad0 relayer register \
  --stake 10000 \
  --payout-address 0xYourPayoutAddress
```

Or use the Stake tab on [zhad0.io/relayer](https://zhad0.io/relayer).

---

## Fee Distribution

```
Intent Fee (ETH)
    │
    ├── 60%  Executing relayer
    ├── 20%  Staking pool (pro-rata to all staked ZHAD0)
    └── 20%  Protocol treasury (DAO-controlled)
```

---

## Slashing Conditions

| Violation | Penalty |
|---|---|
| Submitting intent with invalid ZK proof | -10% stake |
| Coordinating to deanonymize agent transactions | -10% stake |
| Attempting to censor specific agents | -10% stake |

Slashing requires on-chain proof of violation verified by the Base contract.

---

## Security Model

The system tolerates up to **t-1** dishonest relayers (where t = threshold, default 2-of-3). Under this model:

- **Privacy holds** even if t-1 relayers are adversarial
- **Liveness holds** as long as t honest relayers are online
- **No single relayer can censor** any specific agent

---

Website: [zhad0.io](https://zhad0.io)
