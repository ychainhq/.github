# Ychain

> Open-source financial infrastructure for AI agents and autonomous systems.

Bitcoin-first. MCP-first. API-native. Multi-tenant. Multi-chain.

---

## Why

AI agents can reason, plan, and use tools.

But they still lack safe financial infrastructure.

Modern autonomous systems will need:

- wallets,
- balances,
- payment requests,
- transaction preparation,
- ledgers,
- tenant isolation,
- webhook delivery,
- programmable money movement.

Ychain exists to build that layer.

---

## Roadmap

| Chain | Type | Status |
|-------|------|--------|
| **Bitcoin** | PoW | ✅ Complete |
| **TRON** (TRX + USDT TRC-20) | DPoS / non-PoW | ✅ Complete |
| **EVM family** (incl. Hyperledger Besu) | non-PoW | 🔄 In progress |
| **USDC on Ethereum** | EVM | 📅 Planned |

### Completed: Bitcoin

Full Bitcoin payment infrastructure — deposit detection, UTXO management, PSBT preparation, batch withdrawals, multi-node failover, webhook delivery.

### Completed: TRON (TRX + USDT TRC-20)

Full TRON stack — native TRX transfers, USDT TRC-20 support, HD wallet derivation (BIP44 `m/44'/195'/0'`), sweep automation, multi-node failover, dynamic fee estimation (bandwidth points + energy model), configurable USDT withdrawal fee models, on-chain balance caching via dedicated indexer.

### In progress: EVM family

Multi-chain EVM support built on the same chain-agnostic architecture. Covers public networks and enterprise deployments — including **Hyperledger Besu** for private and permissioned EVM environments.

### Planned: USDC on Ethereum

Native USDC support on Ethereum mainnet, extending the stablecoin infrastructure pattern already established for USDT on TRON.

---

## Scale

Ychain is designed to run at the scale of large enterprises and high-volume financial platforms.

### Millions of customers and transactions

The architecture is built around a set of deliberate choices that remove the common ceilings in crypto payment infrastructure:

- **PostgreSQL 16+** as the production database — no SQLite ceiling, scales without structural changes from thousands to millions of rows
- **`SELECT FOR UPDATE SKIP LOCKED`** for distributed worker coordination — multiple engine instances process work in parallel without contention or external coordination services
- **Dedicated block indexers** — deposit detection runs at O(transactions per block), not O(monitored addresses). The indexer scales independently of the engine and of the number of customers.
- **Multi-node chain connectivity** — Bitcoin Core and TRON Full Node pools with TTL-cached dynamic failover. One node going down does not impact throughput.
- **Batch withdrawal processing** — withdrawals are grouped, batched, and signed in bulk, keeping on-chain fees predictable and throughput high regardless of withdrawal volume.
- **On-chain balance caching** — TRON balances are written by the indexer immediately after on-chain events and served from SQL in a single query, regardless of wallet size.

### Enterprise deployments

Ychain is designed for organizations that need more than a hosted API:

- **Multi-tenant isolation** — each tenant operates in a fully isolated namespace: API keys, wallets, customers, ledgers, UTXOs, transactions, webhooks, and chain bindings are never shared across tenants.
- **Self-hosted** — the entire stack runs in your own infrastructure. No dependency on Ychain-operated nodes or signers.
- **Bring your own nodes** — connect your own Bitcoin Core or TRON Full Node instances. Ychain supports a pool of nodes per chain with health monitoring and automatic failover.
- **Active-active engine** — multiple engine instances can run simultaneously against the same PostgreSQL backend, enabling horizontal scaling and zero-downtime deployments.
- **Immutable audit trail** — every financial state transition is recorded in an append-only tickler log. Compliant with audit and record-keeping requirements expected in regulated financial environments.
- **Enterprise signer integration** — private keys live in your Vault, KMS, or HSM. Ychain never touches them. See Enterprise Key Security below.

---

## MCP-first infrastructure

Ychain is designed for the emerging ecosystem of AI agents and MCP-compatible systems.

Financial infrastructure should be:

- tool-callable,
- composable,
- machine-readable,
- automation-friendly,
- safe for autonomous workflows.

Ychain exposes primitives that can become native building blocks for agentic systems:

- payment requests,
- balance queries,
- deposit monitoring,
- transaction preparation,
- ledger operations,
- webhook events,
- programmable financial state.

The future financial stack will not only serve humans and apps.

It will serve autonomous systems.

---

## What Ychain provides

### Bitcoin infrastructure

- address monitoring
- deposit detection
- confirmation tracking
- UTXO management and coin selection
- PSBT transaction preparation
- batch withdrawal processing
- signed transaction broadcasting

### TRON infrastructure (TRX + USDT TRC-20)

- HD wallet derivation — BIP44, per-customer isolated addresses
- TRX native transfer preparation
- USDT TRC-20 transfer preparation
- sweep automation (hot wallet consolidation)
- dynamic fee estimation — bandwidth points and energy model
- configurable USDT withdrawal fee: tenant-pays / sender-pays / recipient-pays
- per-tenant confirmation threshold
- on-chain balance caching via dedicated tron-indexer
- multi-node failover with TTL-cached node selection

### Financial primitives for AI-native systems

- payment requests
- customer balances
- tenant-scoped ledgers
- webhook infrastructure
- idempotent financial operations

### Multi-tenant architecture

Every tenant gets isolated:

- API keys
- wallets
- customers
- deposits
- ledger accounts
- transactions
- webhooks
- UTXO and address selection
- chain bindings and node preferences

---

## Enterprise Key Security

### Sovereign key control

Ychain never holds private keys.

The engine prepares unsigned transactions — PSBT for Bitcoin, raw transaction for TRON.  
Signing happens entirely outside Ychain, in infrastructure you operate and control.

Your keys never touch Ychain's network, database, or process memory.

### Two signing editions

**Signer OSS** — open source, for development, testing, and smaller self-hosted deployments.  
Keys stored locally or in environment variables.

**Signer Enterprise** — for production deployments requiring bank-grade key security, auditability, and compliance.

### Enterprise signing providers

| Provider | Mechanism |
|----------|-----------|
| HashiCorp Vault Transit | sign-without-export — private key never leaves Vault |
| AWS KMS (secp256k1) | hardware-backed signing, non-exportable keys |
| Azure Key Vault | HSM option, FIPS 140-2 Level 2/3 |

Priority chain: Vault Transit → AWS KMS → Azure Key Vault → environment fallback (Tier 2).

Supported for both **Bitcoin** (PSBT signing) and **TRON** (65-byte recoverable EC signature — recovery ID reconstructed from DER output without private key exposure).

### Compliance posture

The external signer architecture is designed to meet the strictest requirements for financial key custody:

- **Key isolation** — private key material never transits Ychain infrastructure. The engine communicates only unsigned payloads (PSBT hash or raw transaction ID). Signing occurs entirely within the HSM or KMS boundary.
- **Sign-without-export semantics** — Vault Transit and AWS KMS are configured so that private keys cannot be exported, even by administrators. The key exists as an opaque, non-extractable handle.
- **Hardware-backed key material** — all three enterprise providers support FIPS 140-2 Level 2 or higher hardware security modules.
- **Immutable audit trail** — every signing task is recorded in Ychain's tickler system, an append-only audit log. Tasks are never modified or deleted after creation.
- **Separation of duties** — key management (Vault/KMS administrator) and transaction authorization (Ychain tenant) are operationally separate roles. Neither side alone can complete a transaction.
- **Multi-signer per tenant** — a tenant can register multiple independent signers, enabling threshold approval workflows.

This design supports compliance with PCI DSS, SOC 2, ISO 27001, and the key custody requirements applied to payment service providers and custodians under banking regulation.

---

## Architecture

```txt
Client Apps / AI Agents
        ↓
     Ychain API  (Node.js · TypeScript · Express · PostgreSQL)
        ↓
Multi-tenant Services
   ├── Bitcoin Services
   │       ↓
   │  btc-indexer  ←  Bitcoin Core Node(s)
   │
   └── TRON Services
           ↓
      tron-indexer  ←  TRON Full Node(s)

External Signer (OSS / Enterprise)
   ← polls for signing tasks
   → submits signed payload
   ← Ychain validates and broadcasts
```

Core components:

- tenant context middleware and actor-scoped RBAC
- wallet infrastructure — Bitcoin and TRON, watch-only by design
- deposit monitoring via chain-agnostic indexer processes
- UTXO management and coin selection
- PSBT and raw transaction preparation
- batch withdrawal processing and batcher
- webhook delivery
- ledger services with immutable tickler audit log
- Bitcoin and TRON chain adapter layer
- multi-node dynamic failover (TTL-cached node selector)
- MCP tool layer for AI agent integration

### Block indexer architecture

Each supported chain runs a dedicated indexer process — a lightweight, stateless binary that scans blocks and writes chain events into the `chain_events` table.

The engine processes each `chain_events` record exactly once, guaranteed by a UNIQUE constraint and `SELECT FOR UPDATE SKIP LOCKED`.

The indexer has no knowledge of tenants, customers, or business logic.  
It outputs only: address → on-chain event.

Adding a new chain requires a new indexer binary and a new chain adapter.  
The deposit, ledger, and webhook infrastructure does not change.

---

## Design principles

### MCP-first

Financial infrastructure should be usable by AI agents directly.

Ychain is designed around:

- composable APIs,
- machine-readable states,
- deterministic workflows,
- safe retries,
- event-driven automation,
- autonomous financial tooling.

### API-native

Everything should be composable and automatable.

### External signing first — zero key custody

Ychain does not custody private keys in any form.

The platform prepares the unsigned payload.  
The external signer validates, signs, and returns the result.  
Ychain validates the signature, broadcasts, and records the audit event.

Private keys never enter Ychain's process memory, network, or storage.

### Clear financial state

Deposits are modeled explicitly:

```txt
detected
confirmed
```

Withdrawals follow:

```txt
created → batched → pending_signature → broadcast → confirmed
```

Financial systems should expose reality instead of hiding it.

### Chain-agnostic by design

The `chain_events` table, deposit processing, ledger, and webhook infrastructure are chain-neutral.

Each chain is an adapter plus an indexer.  
The financial layer does not change when a new chain is added.

### PostgreSQL-grade persistence

Production deployments run on PostgreSQL 16+ with:

- row-level tenant isolation,
- `SELECT FOR UPDATE SKIP LOCKED` for safe distributed worker coordination,
- BigInt-safe value storage as `TEXT` (satoshis, sun, micro-USDT),
- immutable tickler audit log.

---

## Status

Active development — production infrastructure live for Bitcoin and TRON.  
EVM chain support in progress.

---

## Philosophy

Stripe made payments programmable for applications.

The next layer makes financial infrastructure programmable for autonomous systems.

AI agents need money rails.

---

## Contributing

We are looking for contributors interested in:

- Bitcoin infrastructure
- TRON and EVM chain integration
- TypeScript backend systems
- ledger architecture
- agent tooling and MCP ecosystems
- programmable finance
- API design
- enterprise key security and HSM integrations

More contribution docs coming soon.

---

## Links

- Website: https://ycha.in
- X/Twitter: https://x.com/useychain

---

## License

TBD
