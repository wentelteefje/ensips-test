---
description: On-chain identity attestations linked to ENS names via EAS
contributors:
  - nick.eth
  - slobo.eth
ensip:
  created: '2026-04-18'
  status: draft
---

# ENSIP-X: ENS Identity Attestations

## Abstract

This ENSIP defines a standard for linking on-chain identity attestations to ENS names using the Ethereum Attestation Service (EAS). It establishes a schema registry and resolver integration that allows ENS names to serve as portable, verifiable identity anchors.

## Motivation

ENS names are increasingly used as decentralized identities across web3 applications. However, there is no standardized way to attach verifiable claims — such as proof of personhood, KYC status, or professional credentials — to an ENS name. Each protocol implements its own bespoke attestation mechanism, leading to fragmentation.

By standardizing attestation storage and retrieval through ENS resolvers and EAS, this ENSIP enables:

- Portable identity across dApps without re-verification
- Composable trust layers where applications can query attestation status by ENS name
- Privacy-preserving selective disclosure using attestation UIDs

## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

### Attestation Text Record

ENS names MAY store attestation references in text records using the following key format:

```
attestation[<schemaUID>]
```

Where `<schemaUID>` is the EAS schema UID (a `bytes32` hex string). The value MUST be a comma-separated list of attestation UIDs referencing valid EAS attestations.

### Schema Registry

A canonical set of ENS identity schemas SHALL be registered on EAS. Each schema defines the structure of the attestation data:

| Schema Name | Fields | Purpose |
|---|---|---|
| `ens.identity.personhood` | `bool isHuman` | Proof of personhood |
| `ens.identity.email` | `bytes32 emailHash` | Verified email (hashed) |
| `ens.identity.github` | `string username` | Verified GitHub account |
| `ens.identity.twitter` | `string handle` | Verified Twitter/X account |

### Resolver Integration

Resolvers that support attestation lookups MUST implement the following interface:

```solidity
interface IAttestationResolver {
    function attestations(bytes32 node, bytes32 schemaUID) external view returns (bytes32[] memory attestationUIDs);
    function hasAttestation(bytes32 node, bytes32 schemaUID) external view returns (bool);
}
```

### Verification Flow

1. A client queries `hasAttestation(node, schemaUID)` on the name's resolver.
2. If true, the client retrieves the attestation UIDs via `attestations(node, schemaUID)`.
3. The client verifies each attestation on the EAS contract, checking that it is not revoked and that the recipient matches the ENS name owner.

## Rationale

EAS was chosen as the attestation layer because it is chain-agnostic, supports both on-chain and off-chain attestations, and has an established ecosystem of attesters. Using text records as the linking mechanism preserves backward compatibility — resolvers that do not implement `IAttestationResolver` can still store attestation UIDs as plain text records.

## Backwards Compatibility

This ENSIP is fully backward compatible. The text record format (`attestation[<schemaUID>]`) follows the existing parameterized key convention used by other ENSIPs. Resolvers without native attestation support can still store and retrieve attestation UIDs as string values.

## Security Considerations

Attestation UIDs stored in ENS text records are references, not the attestations themselves. Clients MUST verify attestations on-chain via the EAS contract and MUST NOT trust the text record value alone. Specifically, clients MUST check:

1. The attestation has not been revoked.
2. The attestation recipient corresponds to the current ENS name owner.
3. The attester is trusted for the given schema.

If an ENS name is transferred, existing attestations may become invalid if the recipient field no longer matches. Attesters SHOULD revoke attestations when notified of ownership changes.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
