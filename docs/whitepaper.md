# ZHAD0: A ZK-Powered Privacy Layer for AI Agents on Base

**Version:** 1.0  
**Chain:** Base (Chain ID: 8453)  
**Proof System:** RISC Zero  
**Status:** Design Preview

---

## Table of Contents

1. [Abstract](#1-abstract)
2. [Problem Statement](#2-problem-statement)
3. [Architecture](#3-architecture)
4. [Cryptographic Constructions](#4-cryptographic-constructions)
5. [Security Model](#5-security-model)
6. [Economic Mechanism](#6-economic-mechanism)
7. [Base L2 Integration](#7-base-l2-integration)

---

## 1. Abstract

As AI agents increasingly execute autonomous financial transactions on public blockchains, they face systemic privacy failures: front-running via MEV extraction, strategy leakage through wallet transparency, and balance surveillance.

ZHAD0 is a ZK-powered privacy middleware layer for AI agents operating on Base L2. ZHAD0 encrypts agent intents off-chain, generates zero-knowledge proofs of intent validity via RISC Zero, and executes them through a decentralized threshold-relayed Ghost Relay network. The architecture preserves agent operational privacy while maintaining composability with Base's existing DeFi ecosystem.

---

## 2. Problem Statement

Public blockchains are transparent by design. Every transaction, wallet balance, and on-chain interaction is visible to anyone who inspects the mempool or chain state. For human users, this is a known trade-off. For autonomous AI agents executing high-frequency financial strategies, this transparency is catastrophic.

Three specific failure modes emerge at scale:

- **MEV Extraction** — sophisticated searchers monitor the mempool for predictable agent transactions and front-run them within the same block, extracting value before execution.
- **Strategy Leakage** — wallet-linked activity over time reveals agent trading patterns, portfolio allocation, and behavioral signatures to competitors and adversaries.
- **Balance Surveillance** — real-time capital visibility enables targeted attacks, forced liquidations, and manipulation of agent behavior.

---

## 3. Architecture

ZHAD0 operates as a middleware layer between agent frameworks and Base L2. The protocol consists of three primary components:

### 3.1 Intent Encryption Layer

Agent payloads are serialized and encrypted using threshold public-key cryptography. No single party holds the decryption key; a 2-of-3 threshold among Ghost Relayer cohorts is required to reconstruct intent. The encrypted blob is committed to an off-chain queue.

### 3.2 ZK Proof Generation

A RISC Zero zkVM circuit verifies that the encrypted intent is structurally valid — correct nonce, valid signature, within gas bounds — and produces a validity proof without revealing the intent contents. This proof is attached to the encrypted blob.

### 3.3 Ghost Relay Network

A permissioned-but-open network of staked relayers receives blobs, validates proofs on-chain, and submits execution transactions to Base. The executing relayer never learns the intent content. Threshold decryption only occurs at the moment of Base submission.

```
Agent Intent
    │
    ▼
AES-256-GCM Encryption
    │   (client-side, threshold key)
    ▼
RISC Zero ZK Proof
    │   (intent validity, no content)
    ▼
Ghost Relay Network
    │   (2-of-3 threshold)
    ▼
Base L2 Execution
```

---

## 4. Cryptographic Constructions

The ZHAD0 cryptographic stack is built on three primitives:

### 4.1 Threshold Encryption

Distributed Key Generation (DKG) over BLS12-381 establishes a threshold key shared across Ghost Relayers. A 2-of-3 quorum must cooperate to decrypt, preventing any single relayer from reading intent contents.

### 4.2 ZK Proof System

Intent validity proofs use RISC Zero's STARK-based zkVM. The circuit enforces:

1. Signature validity against agent public key
2. Nonce monotonicity
3. Gas ceiling compliance
4. Schema correctness

Proof generation is performed off-chain by the agent. Verification is performed on-chain by the Base verifier.

### 4.3 Commitment Scheme

Intent blobs are committed using Pedersen commitments before relay submission, ensuring blob integrity across the relay lifecycle without revealing contents.

---

## 5. Security Model

ZHAD0's threat model assumes an adversarial network in which up to t-1 of t Ghost Relayers may collude or be compromised, where t is the threshold parameter (default: 2-of-3).

| Property | Guarantee |
|---|---|
| **Privacy** | An adversary controlling fewer than t relayers learns nothing about intent contents |
| **Liveness** | Execution proceeds as long as at least t honest relayers are online |
| **Integrity** | ZK proofs verified on-chain; invalid proofs cause revert + stake slash |
| **Censorship Resistance** | No single relayer can censor an agent; any t-quorum can execute |

---

## 6. Economic Mechanism

### 6.1 Fee Distribution

Each relayed intent pays a fee in ETH, set by the agent:

| Recipient | Share |
|---|---|
| Executing relayer | 60% |
| Staking pool (pro-rata to staked ZHAD0) | 20% |
| Protocol treasury | 20% |

### 6.2 Slashing Conditions

Relayers lose 10% of staked ZHAD0 per verified violation:
- Submitting an intent with an invalid ZK proof
- Coordinating to deanonymize agent transactions
- Attempting to censor specific agents

### 6.3 Expected Yield

Based on projected network volume and a 10,000 ZHAD0 minimum stake, relayer operators can expect **15-25% APR** denominated in ETH, correlated with network adoption.

### 6.4 Treasury Governance

The 20% treasury allocation is controlled by ZHAD0 token holders via on-chain governance, enabling protocol parameter updates and ecosystem grants.

---

## 7. Base L2 Integration

ZHAD0 is deployed exclusively on Base L2 (Chain ID: 8453). Base provides the ideal execution environment:

| Property | Detail |
|---|---|
| **Block Time** | 2 seconds — near-real-time intent execution |
| **EVM Compatibility** | Standard Solidity verifier, auditable by existing tooling |
| **Ethereum Security** | Inherits Ethereum mainnet economic security |
| **AgentKit Ecosystem** | Natural home for AI agent infrastructure on Base |

---

> **Status Note:** ZHAD0 is a design preview. No contracts are deployed on Base mainnet or testnet today. ZK proofs and Ghost Relay submission are simulated in the current SDK. Client-side AES-256-GCM encryption is fully functional.

Website: [zhad0.io](https://zhad0.io)
