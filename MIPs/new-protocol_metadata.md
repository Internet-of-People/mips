---
mip: 
title: Protocol Metadata
author: Byron Blattel (@alchebyte),  Wigy (@wigy-opensource-developer), Bartmoss (@izolyomi)
status: Draft
type: Standards
category: Protocol
created: 
---
# Mercury Protocol Metadata

## Simple Summary

This MIP describes a basic protocol level methodology for storing and transmitting metadata related to objects of interest elsewhere in the protocol (Profile, OwnProfile, Relation, etc.)

## Abstract

Described here is a generalized methodology for storing, requesting, and transmitting metadata from server to client within the Mercury SDK.

## Motivation

To ensure interoperability between standardized Mercury dApps, a methodology for handling metadata for protocol level objects is desired. Additionally, a schema for Profiles and their Relationships must be defined. A main goal for Mercury is enabling the Open Social Graph. This metadata will represent the standardized elements representing the nodes and edges that define the OSG.

## Definitions

### Profile

A Mercury Profile represents a given keypair and metadata that describes it for different use-cases. Currently there are three types of profiles-

- `Persona`: A profile that is used by a real-life person as an avatar (or real identity) in some application. A single real-life person can have multiple personas.
- `Home`: A profile that is used by a home node to describe its end-points, versions and anything related to its service.
- `Application`: A profile that is used by an application author to describe the way that application uses Mercury. Think in terms of package.json or cargo.toml, but more focused on how it uses profiles and what versions it has.

### Property

A Mercury Property is single piece of information in the Profile that has a unique name, a semantic (high-level) type and an atomic (wire) type.

- `Key`: The unique name of the property in the mapping. This name can have multiple forms:
  - MIP-defined hierarchical property name, such as:
    - 'MIP3.Persona.Username'
    - 'MIP3.Home.Addresses'
    - 'MIP3.Persona.Name.First'
  - Application-defined hierarchical property name, such as:
    - 'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Actor.Username'
    - 'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Sparks'
  - Or any other compatible string (for adhoc/anonymous names)
    - 'name'
    - 'local.petNames'
    - '139d2275-1d3b-4ba7-9d28-9a1231e7f49d')

- `Atomic Type`: At the wire level each property has a wire compatible [built-in type](https://capnproto.org/language.html#built-in-types) or core [Mercury defined type or struct](https://gitlab.libertaria.community/mercury/client-sdk-rust/blob/develop/home-protocol/protocol/mercury.capnp)
  - '`Bool`' (boolean)
  - '`Int8`', '`Int16`', '`Int32`', '`Int64`' (Integers)
  - '`UInt8`', '`UInt16`', '`UInt32`', '`UInt64`' (Unsigned Integers)
  - '`Float32`', '`Float64`' (Floating Point Numbers)
  - '`Text`' (Strings and string based sematic types)
  - '`Data`' (byte blobs and binary based semantic types)
  - '`List(T)`' (where T is any other defined type, semantic or atomic)
  - '`ProfileId`', '`ApplicationId`', '`Signature`', '`RelationProof` (core Mercury defined protocol defines/structs)

- `Semantic Type`: Regardless of serialization format (cap'n'proto, protobuf, JSON, BSON, ASN.1 BEM, BEncode, whatever...) the property has a semantic type. Each type could finds its more natural representation on a specific serialization format. Semantic types have similar forms to Keys:
  - MIP-defined hierarchical types, such as:
    - 'MIP3:Person:Name'
    - 'MIP3:Address'
    - 'MIP3:Home:MultiAddress'
  - Application-defined hierarchical property name, such as:
    - 'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Actor'
    - 'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Spark'
  - Or an Atomic type defined above

### Schema

A Mercury Schema is a set of properties (with types) that are either present in a given profile or not. If required properties are missing, then that profile does not match/represent this schema. There can be optional properties in a schema that might be present or not. If a property exists with the same key, but different type, then it is not a match. For optional properties, these non-matches must be treated as missing, for required properties, such a non-match means failure of matching the whole schema.

By representing the schema as a flat list of properties developers can easily combine MIP based shared schemas with their own dApp defined schemas into a single server request for metadata or alternately a fine grained composition tailored to the specific needs of a view in a UI.

The schema should not be transferred together with the profile, it is an independent definition. Applications and use-cases should define schemas and match them to profiles. So profiles are not strongly typed, and just looking at a profile it does not need to describe what schemas it implements.

## Specification

Cap'n Proto partial definition -

```capnproto
struct MetaProperty {
    # typed key for key-value dictionary/map container

    key @0 :Text;
    # The unique name of the property in the mapping. This name can have multiple forms:
    #   - MIP defined, ex. 'MIP3.Persona.Username', 'MIP3.Home.Addresses',
    #        'MIP3:Home:MultiAddress'
    #   - dApp defined, ex. 'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Actor.Username',
    #        'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Sparks'

    atomic @1 :Text;
    # semantic is a string that represents an optional codec-like type definition
    # semantic types have similar forms to Keys:
    #   - MIP defined, ex. 'MIP3:Person:Name', 'MIP3:Address', 'MIP3:Home:MultiAddress'
    #   - dApp defined, ex. 'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Actor.Username',
    #       'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Sparks'
    #   - An Atomic type defined above

    semantic @1 :Text;
    # semantic is a string that represents an optional codec-like type definition
    # semantic types have similar forms to Keys:
    #   - MIP defined, ex. 'MIP3:Person:Name', 'MIP3:Address', 'MIP3:Home:MultiAddress'
    #   - dApp defined, ex. 'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Actor.Username',
    #       'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Sparks'
    #   - An Atomic type defined above
}

struct MetaSchema {
    # A schema is defined by a list of MetaProperty definitions

    types @0 :List(MetaProperty);
}

struct MetaData {
    # MetaProperty/value dictionary/map container, stores all available shapes

    entries @0 :List(Entry);
    struct Entry
    {
        key @0 :MetaProperty;
        value @2 :Data;
    }
}

struct Profile {
    # homes, addresses, appId are moved to OwnProfile.data
}

struct OwnProfile {
    profile     @0 : Profile;
    data        @2 : MetaData;
}

interface ProfileRepo {
    getSchema @3 (profileId: ProfileId, schema: MetaSchema) -> (data: MetaData);
    setSchema @3 (profileId: ProfileId, data: MetaData) -> (Bool);
}
```

## Rationale

Using capnproto protocol definitions for low level elements of the Mercury API
provides a number of desired performance advantages-

- Profile/Relation data is maximally compressed for the 'wire', reducing network bandwidth requirements
- Profile/Relation data requires minimal memory footprint because it is stored and accessed in native memory via a platform specific Connect SDK for both server and client
- On the server, Profile/Relation data can be natively stored in a hash table that can be used for efficient indexing/lookup for API (network crawlers and dApps)
- Client calls to the server to retrieve Profile/Relation data need not transform the in memory representation, that can happen via the Connect SDK client instead

## Backwards Compatibility

Not applicable

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).