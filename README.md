# ZHAD0 Protocol — Documentation

[![Status](https://img.shields.io/badge/status-design__preview-ff3d8a.svg)](https://zhad0.io)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

> Official documentation for the ZHAD0 Protocol — zero-knowledge privacy middleware
> for autonomous AI agents operating on Base L2.

**Website:** [zhad0.io](https://zhad0.io)

---

## Contents

| Document | Description |
|---|---|
| [Whitepaper](docs/whitepaper.md) | Full technical whitepaper — architecture, cryptography, economics |
| [Integration Guide](docs/integration.md) | How to integrate ZHAD0 into your AI agent |
| [SDK Reference](docs/sdk-reference.md) | `@zhad0/sdk` API documentation |
| [API Reference](docs/api-reference.md) | REST API endpoint reference |
| [Relayer Guide](docs/relayer-guide.md) | How to run a Ghost Relayer node |
| [Token](docs/token.md) | ZHAD0 token economics and distribution |
| [Governance](docs/governance.md) | DAO governance model |
| [FAQ](docs/faq.md) | Frequently asked questions |

---

## Protocol Summary

ZHAD0 is a ZK-powered privacy middleware for autonomous AI agents on Base L2.

```
Agent Intent
    │
    ▼
AES-256-GCM Encryption (client-side)
    │
    ▼
RISC Zero ZK Proof of Intent Validity
    │
    ▼
Ghost Relay Network (threshold 2-of-3)
    │
    ▼
Base L2 Execution
```

**Status:** Design Preview. Client-side encryption is live. ZK proofs and
Ghost Relay network are simulated pending mainnet launch.

---

## Related Repos

| Repo | Description |
|---|---|
| [`zhad0/zhad0-app`](https://github.com/zhad0/zhad0-app) | React web app |
| [`zhad0/zhad0-sdk`](https://github.com/zhad0/zhad0-sdk) | TypeScript SDK + CLI |
| [`zhad0/zhad0-api`](https://github.com/zhad0/zhad0-api) | API server |

---

## License

MIT — see [LICENSE](LICENSE)
