---
description: Multi-chain address resolution for ENS names
contributors:
  - wentelteefje
ensip:
  created: '2026-04-16'
  status: draft
---

# ENSIP-X: Multi-chain Address Resolution

## Abstract

This ENSIP defines a standard for resolving ENS names to addresses on multiple blockchain networks beyond Ethereum.

## Motivation

As the ecosystem grows across L2s and alternative chains, users need a single ENS name that resolves to their address on any supported chain.

## Specification

Resolvers SHALL support the `addr(bytes32 node, uint256 coinType)` function as defined in ENSIP-9, extended with a registry of chain-specific coin types.

## Rationale

Reusing the existing multi-coin address record interface ensures backwards compatibility while enabling cross-chain resolution.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
