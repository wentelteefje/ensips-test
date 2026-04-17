---
description: Trustless bridging of ENS name ownership and records to L2 networks
contributors:
  - nick.eth
ensip:
  created: '2026-04-17'
  status: draft
---

# ENSIP-X: ENS L2 Bridging

## Abstract

This ENSIP defines a protocol for trustlessly bridging ENS name ownership and resolver records from Ethereum mainnet to Layer 2 networks, enabling low-cost name management while preserving the security guarantees of L1 ownership.

## Motivation

Managing ENS names on Ethereum mainnet incurs significant gas costs for operations such as setting records, transferring ownership, and renewing names. Layer 2 networks offer dramatically lower transaction costs, but currently there is no standardized mechanism for mirroring ENS state to L2 while maintaining trustless verification back to L1.

## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

### Bridge Architecture

The bridging protocol consists of two components:

1. **L1 Bridge Contract** — Deployed on Ethereum mainnet, this contract observes ENS registry and resolver state changes and emits cross-chain messages to the L2 network.
2. **L2 Mirror Contract** — Deployed on the target L2, this contract receives bridged state and exposes an ENS-compatible interface for local resolution.

### Bridging Flow

1. A name owner calls `bridge(bytes32 node, uint256 chainId)` on the L1 Bridge Contract.
2. The bridge reads the current owner, resolver, and TTL from the ENS registry.
3. A cross-chain message is sent to the L2 Mirror Contract on the specified chain.
4. The L2 Mirror Contract stores the bridged state and makes it available for local resolution.

### Record Synchronisation

When records are updated on L1 after bridging, the bridge MUST emit a new cross-chain message to update the L2 mirror. Clients querying the L2 mirror SHOULD check the `lastSynced` timestamp to assess freshness.

## Rationale

Using native L1-to-L2 messaging (e.g., the Optimism or Arbitrum canonical bridges) for state synchronisation ensures that the L2 mirror inherits the security properties of the underlying rollup. This avoids introducing additional trust assumptions beyond those already present in the L2 itself.

## Backwards Compatibility

Existing L1 ENS resolution is unaffected. The L2 Mirror Contract implements the same resolver interface, so L2 clients can resolve names using the same ABI without modification.

## Security Considerations

The L2 mirror is only as fresh as the last bridged message. During periods of L1 congestion or bridge delays, the L2 state may be stale. Clients SHOULD implement a staleness threshold and fall back to L1 resolution when the mirror data exceeds this threshold.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
