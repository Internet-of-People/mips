---
mip: <to be assigned>
title: OSG - Profile and Relationship Protocols
author: Byron Blattel (@alchebyte)
status: Draft
type: Standards
category: Protocol
created: 2018-7-15
---
## Simple Summary
This MIP describes the schema for Mercury Profiles and their Relationships. Together they form the Mercury Open Social Graph (OSG)
## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Provide a schema for the core Mercury OSG data structures.
## Motivation
To ensure interoperability between Mercury dApps, a standardized schema for Profiles and their Relationships must be defined. Because a main goal for Mercury is the Open Social Graph, the required data structures also represent the standardized elements representing the nodes and edges that define the OSG.
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
    #   - app (or anonymous) shape types ('app-123.')
    type @1 :Text;
}

# Duck (runtime) or structural/property-based (storagetime) typing is achieved by created a list of MetaTypes
# The defintion/instance of that list of types can be thought of as a 'shape' for the data
struct MetaShape
{
    types @0 :List(MetaType);
}

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
    # NOTE these are mandatory in the API, but will be serialized into the data instead
    id        @0 : ProfileId;
    publicKey @1 : PublicKey;
    data      @2 : Metadata; # this holds all  
    
    facet        : union {
        persona :group {
            homes @2 : ;
            # data: TODO
        }
        home :group {
            addresses @3 : List(Text);  # MultiAddress
            # data: TODO
        }
        application :group {
            id @4 : Text;
            # data: TODO
        }
    }
}
```

## Rationale
Using capnproto protocol definitions for low level elements of the Mercury API provides a number of desired performance advantages-
- Profile/Relation data is maximally compressed for the 'wire', reducing network bandwidth requirements
- Profile/Relation data requires minimal memory footprint because it is stored and accessed in native memory via a platform specific Connect SDK for both server and client
- Profile/Relation data can be natively stored in a hash table can be used for efficient indexing/lookup for API (network crawlers and dApps)
- API calls to retrieve Profile/Relation data need not transform the in memory representation 
## Backwards Compatibility
Nor applicable
## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).