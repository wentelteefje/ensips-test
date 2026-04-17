---
description: Versioned resolver records with migration support
contributors:
  - raffy.eth
ensip:
  created: '2026-04-17'
  status: draft
---

# ENSIP-X: Resolver Record Versioning

## Abstract

This ENSIP defines a versioning mechanism for ENS resolver records, allowing name owners to atomically reset all records by incrementing a version counter and enabling clients to detect stale cached data.

## Motivation

When an ENS name changes ownership or a name owner wants to clear all existing records, there is no efficient way to do so. Each record type must be individually cleared, which is both gas-expensive and error-prone — a forgotten record could leak stale data from the previous owner.

A versioning mechanism solves this by allowing name owners to increment a single counter, effectively invalidating all existing records in one transaction.

## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

### Version Counter

Resolvers implementing this ENSIP MUST maintain a per-node version counter:

```solidity
mapping(bytes32 => uint64) public recordVersions;
```

### Record Storage

All records MUST be keyed by `(node, version)` rather than just `node`. When a record is read, the resolver MUST use the current version for that node.

### Version Increment

```solidity
function clearRecords(bytes32 node) external {
    require(msg.sender == ens.owner(node), "Not authorised");
    recordVersions[node]++;
    emit VersionChanged(node, recordVersions[node]);
}
```

After calling `clearRecords`, all previously set records become inaccessible without individually deleting them, saving significant gas.

### Client Behaviour

Clients SHOULD include the record version in cache keys. When a `VersionChanged` event is observed, clients MUST invalidate all cached records for that node.

## Rationale

Using a version counter rather than explicit deletion is a well-established pattern (e.g., nonces in EIP-2612). It provides O(1) gas cost regardless of how many records exist, compared to O(n) for individual deletion.

## Backwards Compatibility

Resolvers that do not implement versioning continue to function normally. Clients that do not support versioning will not benefit from cache invalidation but will still resolve records correctly, as the resolver always returns the current version's data.

## Security Considerations

Name owners SHOULD call `clearRecords` immediately after acquiring a previously owned name to prevent the new owner from being associated with the previous owner's records.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
