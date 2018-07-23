---
mip: MIP-2
title: Protocol Metadata
author: Byron Blattel (@alchebyte)
status: Draft
type: Standards
category: Protocol
created: 2018-7-15
---
# Simple Summary

This MIP describes a basic protocol level method for storing and transmitting
metadata related to objects of interest elsewhere in the protocol (Profile,
OwnProfile, Relation, etc.)

## Abstract

Described here is a generalized methodology for storing, requesting, and
transmitting (semi-opinionated) metadata from server to client within the
Mercury SDK.

## Motivation

To ensure interoperability between Mercury dApps, a standardized methodology
for handling metadata for protocol level objects is desired. Additionally, a
schema for Profiles and their Relationships must be defined. A main goal for
Mercury is enabling the Open Social Graph. This metadata will represent the
standardized elements representing the nodes and edges that define the OSG.

## Specification

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