---
mip: <to be assigned>
title: Protocol object Metadata
author: Byron Blattel (@alchebyte)
status: Draft
type: Standards
category: Protocol
created: 2018-7-15
---
## Simple Summary
This MIP describes a basic protocol level method for storing and transmitting metadata related to objects of interest elsewhere in the protocol (Profile, OwnProfile, Relation, etc.)
## Abstract
Described here is a generalized methodology for storing, requesting, and transmitting (semi-opinionated) metadata from server to client within the Mercury SDK.
## Motivation
To ensure interoperability between Mercury dApps, a standardized methodology for handling metadata for protocol level objects is desired. Additionally, a schema for Profiles and their Relationships must be defined. A main goal for Mercury is enabling the Open Social Graph. This metadata will represent the standardized elements representing the nodes and edges that define the OSG.
## Specification
The Profile (node) and Relation (edge) data structures of the Mercury OSG are defined with the following capnproto protocol objects.
```
# proposal for Profile formalization via "shape's" of a semi-opinionated, typed key-value pair, metadata repository
struct MetaType
{
    # key is string (optionally) preceded by a namespace and colon (by mip #) "mip-n:phone-cell", "mip-n:homes"
    key @0 :Text;
    # type is a string that represents a 'semi-opionated' type definition consisting of
    #   - built-in types ('text', 'bool', etc.)
    #   - protocol types List(RelationProof),
    #   - shape types ('mip-n.phone-number, mip-3.car')
    #   - app (or anonymous) shape types ('app-123.widget')
    type @1 :Text;
}

# Duck (runtime) or structural/property-based (storagetime) typing is achieved by created a list of MetaTypes
# The defintion/instance of that list of types can be thought of as a 'shape' for the data
struct MetaShape
{
    types @0 :List(MetaType);
}

# metadata repository/container
struct MetaData
{
    entries @0 :List(Entry);
    struct Entry
    {
        key @0 :MetaType;
        value @2 :Data;
    }
}

struct Profile
{
    # homes, addresses, appId are moved to OwnProfile.data
}

struct OwnProfile
{
    profile     @0 : Profile;
    data        @2 : Metadata;
}

interface ProfileRepo
{
    getShape @3 (profileId: ProfileId, shape: MetaShape) -> (data: Metadata);
    setShape @3 (profileId: ProfileId, shape: MetaShape, data: Metadata) -> ();
}

```
## Rationale
Using capnproto protocol definitions for low level elements of the Mercury API provides a number of desired performance advantages-
- Profile/Relation data is maximally compressed for the 'wire', reducing network bandwidth requirements
- Profile/Relation data requires minimal memory footprint because it is stored and accessed in native memory via a platform specific Connect SDK for both server and client
- On the server, Profile/Relation data can be natively stored in a hash table that can be used for efficient indexing/lookup for API (network crawlers and dApps)
- Client calls to the server to retrieve Profile/Relation data need not transform the in memory representation, that can happen via the Connect SDK client instead
## Backwards Compatibility
Nor applicable
## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).