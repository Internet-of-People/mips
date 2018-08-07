---
mip: 
title: Protected Profile Data
author: Byron Blattel (@alchebyte),  Wigy (@wigy-opensource-developer), Bartmoss (@izolyomi)
status: Draft
type: Standards
category: Protocol
created: 
---
# Protected Profile Data

## Simple Summary

This MIP describes a secure basic data structure for storing, transmitting, querying or mutating Profile Data.

## Abstract

Described here is a generalized data structure for storing, transmitting, querying or mutating Profile Data within the Mercury SDK.

## Motivation

To ensure interoperability between standardized Mercury dApps, a methodology for handling protected Profile Data is desired. To support the Open Social Graph, a user shareable schema for Profiles and their Relations must be developed. The granularity of sharing ideally will be at an individual property level so the end user has the greatest freedom over their privacy per Profile. The sharing of that data needs to be wholly defined and encrypted client side to minimize the risk of a bad actor at the server. Private keys should not have to leave client. However it would be more efficient to keep any protected data off the wire when the recipient does not have the private key to decrypt it. So being able to (publicly) see who has access would be beneficial to the server.

## Definitions

### Profile

A Mercury Profile represents a given keypair and metadata that describes it for different use-cases. Currently there are three types of profiles-

- `Persona`: A profile that is used by a real-life person as an avatar (or real identity) in some application. A single real-life person can have multiple personas.
- `Home`: A profile that is used by a home node to describe its end-points, versions and anything related to its service.
- `Application`: A profile that is used by an application author to describe the way that application uses Mercury. Think in terms of package.json or cargo.toml, but more focused on how it uses profiles and what versions it has.

### Property

A Mercury Property is single piece of information in the Profile that has a unique name and (potentially optional) protected value.

## Specification

### Pseudo code

```c
struct property {
    name: string, // The unique name of the property within a schema
    vault: vault, // A lockable storage container for the data
}

struct vault {
    // each vault generates a keypair when locked
    publicKey: string, // from keypair, if empty data is public, address in DHT?
    accessKeys: list<key>, // encrypted copies of keypair private key
    data: string, // encrypted with publicKey
}

struct key {
    publicKey: string,      // Profile ID/address/public key that has access
    encryptedKey: string,   // Private key from vault keypair encrypted with publicKey
}
```

## Rationale

## Backwards Compatibility

Not applicable

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).