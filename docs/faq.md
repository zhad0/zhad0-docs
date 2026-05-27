# Frequently Asked Questions

---

## Protocol

**What is ZHAD0?**

ZHAD0 is a ZK-powered privacy middleware for autonomous AI agents operating on Base L2. It encrypts agent transaction intents off-chain, generates zero-knowledge proofs of their validity using RISC Zero, and executes them through a decentralized Ghost Relay network — making agent activity invisible to MEV bots and on-chain observers.

**Why do AI agents need privacy?**

AI agents executing financial strategies on public blockchains expose their every move to front-running bots, competitors, and surveillance. MEV bots can extract value from predictable agent transactions before they execute. ZHAD0 eliminates this attack surface completely.

**Which chains does ZHAD0 support?**

ZHAD0 is designed for Base L2 (Chain ID: 8453) at launch. Base was chosen for its 2-second block time, EVM compatibility, and its native ecosystem for AI agent infrastructure. Multi-chain support is planned post-mainnet.

**Is ZHAD0 audited?**

Not yet. No external security audit has been completed. The audit program is planned to begin once the ZK circuit and Base contracts reach v1 candidate. The codebase is open-source and publicly inspectable on GitHub.

---

## Token

**What is the $ZHAD0 token used for?**

The $ZHAD0 token is designed to serve three functions at mainnet:

1. **Ghost Relayer staking** — operators stake at least 10,000 ZHAD0 to run a node
2. **Governance** — token holders vote on protocol parameters and treasury allocation
3. **Slashing bond** — staked ZHAD0 is slashed per protocol violation

The token has not been deployed, distributed, or made available for sale. No public sale, no airdrop, no contract on Base today.

**What is the planned total supply?**

The proposed total supply is 1,000,000,000 (1 billion) ZHAD0. Planned distribution:

| Allocation | Share |
|---|---|
| Relayer Rewards | 35% |
| Ecosystem & Dev | 25% |
| Community Sale | 20% |
| Team & Advisors | 12% |
| Treasury | 8% |

Subject to change before TGE.

**How will token holders earn?**

At mainnet, 20% of every relay fee is planned to be distributed pro-rata to staked ZHAD0 holders, denominated in ETH. This mechanism is not active today.

---

## Ghost Relay Network

**What is a Ghost Relayer?**

A Ghost Relayer is the planned node-operator role: a staked node that receives encrypted intent blobs, validates ZK proofs on-chain, and submits execution transactions to Base — without ever seeing intent contents. The network does not exist today; this describes the v1 design.

**Can I run a Ghost Relayer node today?**

No. There is no relayer software released, no staking contract deployed, and no testnet relay roster. Node-operator onboarding is planned post-audit, post-mainnet.

**What are the planned slashing conditions?**

Per the v1 design, a relayer's stake is slashed 10% per verified violation:

1. Submitting an intent with an invalid ZK proof
2. Coordinating with other relayers to deanonymize agent transactions
3. Attempting to censor specific agents

---

## Technical

**What ZK proof system does ZHAD0 use?**

ZHAD0 uses RISC Zero's zkVM, a general-purpose zero-knowledge virtual machine that generates STARK-based proofs. The intent-validity circuit enforces: signature validity, nonce monotonicity, gas ceiling compliance, and schema correctness — without revealing intent contents.

**How does the threshold encryption work?**

ZHAD0 uses Distributed Key Generation (DKG) over BLS12-381 to establish a threshold public key shared across Ghost Relayers. A 2-of-3 quorum must cooperate to decrypt any intent, preventing any single relayer from reading intent contents even if compromised.

**Can I integrate ZHAD0 with Virtuals Protocol or Eliza today?**

Not yet. Virtuals, Eliza, and Coinbase AgentKit are planned integration targets for SDK v1 (roadmap: Q4 2026). The current `@zhad0/sdk` package is a design preview — it ships real client-side AES-256-GCM encryption and the `wrapAgent()` API surface, but ZK proofs and relayer submission are simulated until the Ghost Relay network is live on Base.

---

Website: [zhad0.io](https://zhad0.io)
