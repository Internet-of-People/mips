---
mip: MIP-2
title: Protocol Metadata
author: Byron Blattel (@alchebyte)
status: Draft
type: Standards
category: Protocol
created: 2018-7-15
---
## Simple Summary
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
The Profile (node) and Relation (edge) data structures of the Mercury OSG are 
defined with the following capnproto protocol objects.
```
# proposal for metadata formalization via shape's of a semi-opinionated, typed 
# key-value pair, metadata repository
struct MetaType
{
    key @0 :Text;
    # key is string (optionally) preceded by a namespace and colon 
    # key 'type' is inferred from string by convention-
    #   - MIP defined, ex. "mip-n:phone-cell", "mip-n:homes"
    #   - dApp defined, ex. "app-myfavapp:id", "app-gigit:username"
    #   - ad hoc/anonymous, ex. "hairColor", "petNames", "139d2275-1d3b-4ba7-9d28-9a1231e7f49d"

    type @1 :Text;
    # type is a string that represents a 'semi-opionated' type definition 
    # consisting of
    #   - built-in types, ex. "Text", "Bool", "UInt32", "Data", etc. see https://capnproto.org/language.html#built-in-types
    #   - protocol types, ex. "Relation", "List(RelationProof)", "List(Text)", etc.
    #   - mip shape types, ex. "MIP-N.PhoneNumber", "MIP-3.Car"
    #   - app shape types, ex. "APP-abc.Widget-1"
}

# Duck (run time) or structural/property-based (storage/wire time) typing is achieved 
# by created a list of MetaTypes. The definition/instance of that list of types 
# can be thought of as a 'shape' for the data
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
Using capnproto protocol definitions for low level elements of the Mercury API 
provides a number of desired performance advantages-
- Profile/Relation data is maximally compressed for the 'wire', reducing network bandwidth requirements
- Profile/Relation data requires minimal memory footprint because it is stored and accessed in native memory via a platform specific Connect SDK for both server and client
- On the server, Profile/Relation data can be natively stored in a hash table that can be used for efficient indexing/lookup for API (network crawlers and dApps)
- Client calls to the server to retrieve Profile/Relation data need not transform the in memory representation, that can happen via the Connect SDK client instead
## Backwards Compatibility
Nor applicable
## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).