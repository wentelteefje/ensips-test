---
description: A framework for ENS subdomain rental and temporary ownership
contributors:
  - wentelteefje
ensip:
  created: '2026-04-16'
  status: draft
---

# ENSIP-X: ENS Subdomain Rental

## Abstract

This ENSIP defines a framework for renting ENS subdomains with time-limited ownership and automatic expiry.

## Motivation

Many ENS name holders want to monetize their names by offering subdomains, but lack a standardized rental mechanism.

## Specification

Subdomain rental contracts SHALL implement a `rent(bytes32 node, address tenant, uint256 duration)` function.

## Rationale

A standardized rental interface allows marketplaces and wallets to integrate subdomain rentals uniformly.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
