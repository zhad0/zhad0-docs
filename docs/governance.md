# Governance

> **Status:** DAO contracts are not yet deployed. This documents the planned governance model.

---

## Overview

ZHAD0 governance is controlled by $ZHAD0 token holders via on-chain DAO. The protocol uses OpenZeppelin's Governor module (OZ Governor + TimelockController) deployed on Base L2.

---

## Governable Parameters

| Parameter | Default | Description |
|---|---|---|
| Threshold (t-of-n) | 2-of-3 | Relayer quorum for decryption |
| Minimum stake | 10,000 ZHAD0 | Minimum to run a relayer node |
| Fee split: relayer | 60% | Relay fee share for executing relayer |
| Fee split: stakers | 20% | Relay fee share for staking pool |
| Fee split: treasury | 20% | Relay fee share for treasury |
| Slashing rate | 10% | Stake slashed per violation |
| Unbonding period | 7 days | Time to withdraw unstaked ZHAD0 |

---

## Voting Process

1. Any address holding at least 100,000 ZHAD0 can submit a proposal
2. Voting period: 7 days
3. Quorum: 4% of circulating supply
4. Timelock: 2 days between approval and execution

---

## Treasury

The 20% treasury allocation is held in a multisig until DAO governance is live, then transferred to the TimelockController. Treasury funds are used for:

- Ecosystem grants (developer tooling, integrations)
- Security audits
- Protocol upgrades
- Marketing and community programs

---

Website: [zhad0.io](https://zhad0.io)
