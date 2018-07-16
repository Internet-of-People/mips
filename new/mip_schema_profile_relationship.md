---
mip: <to be assigned>
title: OSG - Profile and Relationship Schemas
author: Byron Blattel (@alchebyte)
status: Draft
type: Standards
category: Schema
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

## Rationale
Using capnproto protocol definitions for low level elements of the Mercury API provides a number of desired performance advantages-
- Profile/Relation data is maximally compressed for the 'wire', reducing network bandwidth requirements
- Profile/Relation data requires minimal memory footprint because it is stored and accessed on native memory boundaries via a platform specific Connect SDK for both server and client
- Profile/Relation data can be natively stored in a hash table can be used for efficient indexing/lookup for API (network crawlers and dApps)
- API calls to retrieve Profile/Relation data need not transform the in memory representation 
## Backwards Compatibility
<!--All MIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The MIP must explain how the author proposes to deal with these incompatibilities. MIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
All MIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The MIP must explain how the author proposes to deal with these incompatibilities. MIP submissions without a sufficient backwards compatibility treatise may be rejected outright.

## Test Cases
<!--Test cases for an implementation are mandatory for MIPs that are affecting 'Standards' track changes. Other MIPs can choose to include links to test cases if applicable.-->
Test cases for an implementation are mandatory for MIPs that are affecting consensus changes. Other MIPs can choose to include links to test cases if applicable.

## Implementation
<!--The implementations must be completed before any MIP is given status "Final", but it need not be completed before the MIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
The implementations must be completed before any MIP is given status **Final**, but it need not be completed before the MIP is **Accepted**. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).