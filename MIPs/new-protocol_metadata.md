---
mip: MIP-2
title: Protocol Metadata
author: Byron Blattel (@alchebyte)
status: Draft
type: Standards
category: Protocol
created: 2018-7-15
---
# MIP Process and Guidelines

## Simple Summary

This MIP describes a basic protocol level methodology for storing and transmitting metadata related to objects of interest elsewhere in the protocol (Profile, OwnProfile, Relation, etc.)

## Abstract

Described here is a generalized methodology for storing, requesting, and transmitting metadata from server to client within the Mercury SDK.

## Motivation

To ensure interoperability between Mercury dApps, a standardized methodology for handling metadata for protocol level objects is desired. Additionally, a schema for Profiles and their Relationships must be defined. A main goal for Mercury is enabling the Open Social Graph. This metadata will represent the standardized elements representing the nodes and edges that define the OSG.

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

- `Semantic Type`: Regardless of serialization format (cap'n'proto, protobuf, JSON, BSON, ASN.1 BEM, BEncode, whatever...) the property has a semantic type. Each type should finds its more natural representation on a specific serialization format, but even if the serialization format cannot distinguish between strings, binary data, numbers, dates, time intervals or names, the type system defined by this MIP should give a stong, semantic type to the property.

- `Atomic Type`: At the wire level each property has a wire compatible type (which could also be its sematic type)

### Schema

A Mercury Schema is a set of properties (with types) that are either present in a given profile or not. If required properties are missing, then that profile does not match this schema. There can be optional properties in a schema that might be present or not. If a property exists with the same key, but different type, then it is not a match. For optional properties, these non-matches must be treated as missing, for required properties, such a non-match means failure of matching the whole schema.

The schema should not be transferred together with the profile. Applications and use-cases should define schemas and match them to profiles. So profiles are not strongly typed, and just looking at a profile it does not need to describe what schemas it implements.

## Specification

Cap'n Proto partial definition -

```capnproto
struct MetaType {
    # typed key for key-value dictionary/map container

    key @0 :Text;
    # key is string (optionally) preceded by a namespace and colon
    # key hierarchy/inheritance is inferred from string by convention-
    #   - MIP defined, ex. "mip-n.phone-cell", "mip-n.homes"
    #   - dApp defined, ex. "app-myfavapp.id", "app-gigit.username"
    #   - adhoc/anonymous, ex. "mySecretApp.secretField", "hairColor", "petNames", "139d2275-1d3b-4ba7-9d28-9a1231e7f49d"

    type @1 :Text;
    # type is a string that represents a 'semi-opinionated' type definition
    # consisting of
    #   - built-in types, ex. "Text", "Bool", "UInt32", "Data", etc. see https://capnproto.org/language.html#built-in-types
    #   - protocol types, ex. "Relation", "List(RelationProof)", "List(Text)", etc.
    #   - mip shape types, ex. "MIP-N:Persona:PhoneNumber", "MIP-N:Persona:...", "MIP-3:Car"
    #   - app shape types, ex. "APP-abc:Widget-1"
}

struct MetaShape {
    # Duck/adhoc (run time) or structural/property-based (storage/wire time) typing is achieved
    # by created a list of MetaTypes. A definition/instance of that list of MetaTypes
    # can be thought of as a 'shape' for the data

    types @0 :List(MetaType);
}

struct MetaData {
    # MetaType/value dictionary/map container, stores all available shapes

    entries @0 :List(Entry);
    struct Entry
    {
        key @0 :MetaType;
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
    getShape @3 (profileId: ProfileId, shape: MetaShape) -> (data: Metadata);
    setShape @3 (profileId: ProfileId, shapeData: Metadata) -> (Void);
}
```

Using these capnproto protocol structures the metadata can then 'shaped' into whatever
granularity fits the use case. There can be standardized shapes (defined by MIPs)
for things like:

- Profile types (Persona, dApp, etc.) or individual elements (Persona:UserName,
    Persona:Avatar, dApp:Id, dApp:Name, etc.)
- Relation types (Contact, AppUser) or individual elements (Contact:Address,
    Contact:Type, AppUser:Id)

Or adhoc/anonymous shapes, not 'meant to be shared' with other apps
(mySecretApp:myField, '139d2275-1d3b-4ba7-9d28-9a1231e7f49d:1')

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