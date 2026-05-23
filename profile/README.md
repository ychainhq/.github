# Ychain

> Open-source financial infrastructure for AI agents.

Bitcoin-first. MCP-first. API-native. Multi-tenant.

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

### Bitcoin-first infrastructure

- address monitoring
- deposit detection
- confirmation tracking
- UTXO management
- PSBT transaction preparation
- signed transaction broadcasting

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
- UTXO selection

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

### External signing first

Ychain beta does not custody private keys.

The platform prepares PSBTs.  
Clients sign externally.  
Ychain validates and broadcasts.

### Simple infrastructure first

The beta intentionally starts with:

- Bitcoin only
- SQLite
- one Node.js process
- in-process workers
- minimal operational complexity

### Clear financial state

Payments and deposits are modeled explicitly:

```txt
created
detected
pending_confirmation
confirmed
finalized
```

Financial systems should expose reality instead of hiding it.

---

## Current beta scope

### Included

- Bitcoin mainnet
- watch-only wallets
- external signer flow
- payment requests
- deposit monitoring
- webhook delivery
- minimal ledger
- multi-tenant API architecture
- customer-scoped balances and deposits

### Planned later

- Ethereum & EVM chains
- ERC-20 support
- advanced custody models
- MPC / HSM integrations
- managed withdrawals
- advanced accounting
- distributed workers
- Postgres / HA architecture

---

## Architecture

```txt
Client Apps / AI Agents
        ↓
     Ychain API
        ↓
Multi-tenant Services
        ↓
 Bitcoin Core Node
```

Core components:

- tenant context middleware
- wallet infrastructure
- deposit monitoring
- transaction preparation
- webhook delivery
- ledger services
- Bitcoin adapter layer

---

## Philosophy

Stripe made payments programmable for applications.

The next layer makes financial infrastructure programmable for autonomous systems.

AI agents need money rails.

---

## Status

Early beta / active development.

The architecture and APIs are evolving quickly.

---

## Contributing

We are looking for contributors interested in:

- Bitcoin infrastructure
- TypeScript backend systems
- ledger architecture
- agent tooling
- MCP ecosystems
- programmable finance
- API design

More contribution docs coming soon.

---

## Links

- Website: https://ycha.in
- X/Twitter: https://x.com/useychain

---

## License

TBD
