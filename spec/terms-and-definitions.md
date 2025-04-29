~ ## Terms and Definitions
~ <!-- file name: terms-and-definitions.md -->

~ For the purposes of this document, the following terms and definitions apply.

~ ISO and IEC maintain terminological databases for use in standardization at the following addresses:

~  - ISO Online browsing platform: available at <https://www.iso.org/obp>
~  - IEC Electropedia: available at <http://www.electropedia.org/>

[[def: Autonomic Identifier (AID)]]

~ a self-managing cryptonymous identifier that must be self-certifying (self-authenticating) and must be encoded in CESR as a qualified Cryptographic Primitive.

[[def: Composability]]

~ short for text-binary concatenation composability. An encoding has Composability when any set of self-framing concatenated Primitives expressed in either the Text domain or Binary domain may be converted as a group to the other Domain and back again without loss.

[[def: Cryptographic Primitive]]

~ the serialization of a value associated with a cryptographic operation including but not limited to a digest (hash), a salt, a seed, a private key, a public key, or a signature.

[[def: Domain ]]

~ a representation of a Primitive either Text (T), Binary (B) or Raw binary \(R\).

[[def: Framing Codes]]

~ codes that delineate a number of characters or bytes, as appropriate, that can be extracted atomically from a Stream.

[[def: Group/Count Codes]]

~ special Framing Codes that can be specified to support groups of Primitives which make them pipelinable.  Self-framing grouping using Count Codes is one of the primary advantages of composable encoding.

[[def: Key Event Receipt Infrastructure (KERI)]]

~ or the KERI protocol, is an identity system-based secure overlay for the Internet.

[[def: Message]]

~ consists of a serialized data structure that comprises its body and a set of serialized data structures that are its attachments. Attachments may include but are not limited to signatures on the body.

[[def: Primitive]]

~ a serialization of a unitary value.  All Primitives in KERI must be expressed in CESR.

[[def: Quadlet]]

~ a group of 4 characters in the _T_ domain and equivalently in triplets of 3 bytes each in the _B_ domain used to define variable size.

[[def: Stable]]

~ todo

[[def: Stream]]

~ any set of concatenated Primitives, concatenated groups of Primitives or hierarchically composed groups of Primitives.

[[def: Tritet]]

~ 3 bits. See [Performant resynchronization with unique start bits](#performant-resynchronization-with-unique-start-bits)

[[def: Variable Length]]

~ a type of count code allowing for vaiable size signatures or attachments which can be parsed to get the full size

[[def: Version]]

~ the CESR Version is provided by a special Count Code that specifies the Version of all the the CESR code tables in a given Stream or Stream section.

[[def: Version String]]

~ the first field in any top-level KERI field map in which it appears.

~ [//]: # (Composability and Domain representations {#sec:content})