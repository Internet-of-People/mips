---
mip:
title: Alternative approach for metadata handling 
author: Byron Blattel (@alchebyte), Wigy (@wigy-opensource-developer), Rache Bartmoss (@izolyomi)
status: Draft
type: Standards
category: Protocol
created: 2018-07-25
---

# Alternative approach for metadata handling

## Simple Summary

This MIP describes methodology for storing and transmitting Mercury profiles
in weakly typed document formats at the basic protocol levels
and deducing strong types (i.e. schema or metadata) for them later on request. 

## Abstract

<!--
A short (~200 word) description of the technical issue being addressed.
-->
A short (~200 word) description of the technical issue being addressed.

## Motivation

To ensure interoperability between standardized Mercury dApps,
a methodology for handling metadata for protocol level objects is desired.
Additionally, a schema for Profiles and their Relationships must be defined.
A main goal for Mercury is enabling the Open Social Graph.
This metadata will represent the standardized elements representing the nodes and edges that define the OSG.

## Definitions

### Profile

A Mercury Profile represents a given keypair and metadata that describes it for different use-cases.
Currently there are three types of profiles-

- `Persona`: A profile that is used by a real-life person as an avatar (or real identity) in some application.
   A single real-life person can have multiple personas.
- `Home`: A profile that is used by a home node to describe its end-points,
   versions and anything related to its service.
- `Application`: A profile that is used by an application author to describe the way
   that application uses Mercury. Think in terms of package.json or cargo.toml,
   but more focused on how it uses profiles and what versions it has.

### Property

A Mercury Property is single piece of information in the Profile that has a unique name and
a semantic (high-level) type of arbitrary complexity, potentially containing a set of further properties.

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
    - '139d2275-1d3b-4ba7-9d28-9a1231e7f49d'
- `Semantic Type`: **TODO reconsider if this still applies in the same form as in Byron's MIP.**
  Regardless of its serialization format
  (cap'n'proto, protobuf, JSON, XML, BSON, ASN.1 BEM, BEncode, whatever) each property has a semantic type.
   Each type can find its natural representation on a specific serialization format, we have no restrictions there.
   Semantic types have similar forms to Keys:
  - MIP-defined hierarchical types, such as:
    - 'MIP3:Person:Name'
    - 'MIP3:Address'
    - 'MIP3:Home:MultiAddress'
  - Application-defined hierarchical property name, such as:
    - 'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Actor'
    - 'APP.139d2275-1d3b-4ba7-9d28-9a1231e7f49d.Spark'

### Schema

A Mercury Schema is a set of properties (with types) that are either present in a given profile or not.
If required properties are missing, then that profile does not match/represent this schema.
There can be optional properties in a schema that might be present or not.
If a property exists with the same key, but different type, then it is not a match.
For optional properties, these non-matches must be treated as missing.
For required properties, such a non-match means failure of matching the whole schema.

By representing the schema as a flat list of properties developers can easily combine MIP based shared schemas
with their own dApp defined schemas into a single server request for metadata
or alternately a fine grained composition tailored to the specific needs of a view in a UI.

**TODO consider how to handle arrays in naming. We could use names like 'items.0.id' but you cannot define in advance
the number of items in an array, so schema matching must respect an arbitrary number of elements**

The schema should not be transferred together with the profile, it is an independent definition.
Applications and use-cases should define schemas and match them to profiles.
So profiles are not strongly typed, and just looking at a profile it does not need to describe what schemas it implements.

## Specification

### Network transport format

To communicate through the network, applications must use a platform-independent data format,
i.e. data on the wire must not be specific to any hardware platform, operating system or programming language.
Initially we provide a Cap'n'Proto network interface in Mercury but there must be no obstacle
to use any different networking protocol, e.g. it should be easy to add also REST-based communication.

As an extract of our Cap'n'Proto-RPC definition, we propose the following approach.
Note that `Data` is simply a binary array in capnp, thus it's basically just a buffer to send.
We could also use something like `using Profile = Data;` but having structures provides more
flexibility including changing fields and thus evolving the protocol if needed. 

```capnproto
# public data related to an arbitrary profile, i.e. a node of the social graph
struct Profile {
    data @0 : Data;
}

# a connection between two profiles, i.e. an edge of the social graph
struct Relation {
    data @0 : Data;
}

# encrypted private data related to your own profile
struct OwnProfile {
    data @0 : Data;
}
```

You can see that the wire protocol does not make any assumption about the data sent.
Instead it just handles every type as some unknown binary serialized structure.
This ensures that the transport is completely orthogonal and is unaffected by any change
in the structure or semantics of the underlying data types.

### Data serialization

We shouldn't restrict the serialization to any specific format such as XML or BSON.
Instead, we should be able to support any formats and leave the choice to developers
building their apps on top of Mercury.

For this reason we should use self-describing data formats defined in the
[multicodec/multistream specification](https://github.com/multiformats/multicodec)
which enables receiving data in multiple formats and sending data in any format of your choice.
At the moment of writing this, there are implementations available for several languages, e.g. JS, go or Python.
A (still very limited) Rust implementation developed by one of our core members is
[available here](https://github.com/mudlee/rust-multicodec).

As minimal requirement regarding the serialization format, it must have weak typing and be able to describe its own structural representation without further metadata. To do so it must support the following features:
1. Serialize a structure as a list of all of its fields, i.e applying the other rules recursively. Note that this is the rule applied when serializing a "root" structure. 
2. Serialize a field with a simple value at least as a pair of variable name and its related string value. Support for other simple variable types is optional. 
3. Serialize a field having a collection of values as a pair of a variable name and an array of its values.
4. Serialize a field having a type of a complex structure as a pair of variable name and a list of variable name and value pairs.

Note that these rules are quite generic and provided by most serialization frameworks, e.g. Rust `serde` out of the box.

Also not that formats requiring code generation (e.g. Corba, Cap'n'Proto or ProtoBuf) usually have stronger typing thus work when additional schema information is stored with the data itself. As a result they are not format-independent anymore hence ideal for our purpose and we do not support them.

### Schema matching

We send and receive serialized data on the network that is already (limitedly) self-describing in itself if meeting our requirements above. In other words, the structure of objects is rebuilt during deserialization including properties and embedded arrays and objects. For example, we could use [metainformation created for our storage crate](https://github.com/Internet-of-People/mercury-rust/blob/develop/storage/src/meta/mod.rs#L5) as this intermediate deseralizaed format.
 
Consequently, related schema information is never needed to be sent together with the data itself or even explicitly defined. Instead, data schema is deduced only implicitly with the duck-typing method described at our definition of schema.

### Example

Assume we have the following, not too complex type in our program.

```rust
struct Point {
  x: f32,
  y: f32,
}

struct Shape {
  color: RgbColor,
  points: Vec<Point> 
}
``` 

Sending a `shape` object on the network, we serialize it using `multicodec`. If we choose to serialize it as Xml, we might end up with something like

```xml
XML_codec
<shape>
  <color value="#906090"/>
  <points>
    <item x="1" y="1">
    <item x="2" y="-42"/>
  </points>
</shape>
```

or we can prefer Json and have something like

```json
JSON_codec
{
  'color': '#906090',
  'points': [
    { 'x': 1, 'y': 1 },
    { 'x': 2, 'y': -42 }
  ]
}
```

Whatever format we've chosen, the receiving side can parse it based on the codec prefix using `multicodec` decoding.
 
#### Option 1
 
We will deserialize the following intermediate data structure out of the data received. Note that this simplified code will not directly compile in Rust, serves for informative purpose only [following the logic defined here](https://github.com/Internet-of-People/mercury-rust/blob/develop/storage/src/meta/mod.rs#L5)

```rust
Object{
  Attribute{ 'color',  String("#906090") },
  Attribute{ 'points', Array[
    Object{
      Attribute{ 'x', Integer(1) },
      Attribute{ 'y', Integer(1) }
    },
    Object{
      Attribute{ 'x', Integer(2) },
      Attribute{ 'y', Integer(-42) }
    }
  ] }
}
```

This format-independent data structure then can be matched against any schema expected by the application.

```rust
// This Colored schema matches our sample data 
let schema_colored = AttributeValue::Object{
  Attribute{ 'color', String }
};
```

#### Option 2

Following the same sample data, the receiving side tries to deserialize the following strongly-typed structure directly out of the `multicodec` network data without intermediate representation format, effectively leaving schema matching to the serialization library used, e.g. `serde`.

```rust
struct Colored {
  color: String,
}
...
let colored: Result<Colored, Error> = multicodec::decode(network_data);
```


## Rationale

This proposal provides the following advantages:
- keeps the network layer and internal data structures completely orthogonal, each one can be changed without affecting the other
- uses duck typing, so schema evolution can be done with minimal coding efforts
- never sends schema information, so spares bandwidth
- does not complicate the network layer by schema-related stuff

## Backwards Compatibility

Not applicable.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).