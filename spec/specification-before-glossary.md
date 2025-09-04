Composable Event Streaming Representation (CESR)
================================================

**Specification Status**: v0.9 Draft

**Latest Draft:**

[https://github.com/trustoverip/tswg-cesr-specification](https://github.com/trustoverip/tswg-cesr-specification)

**Author:**

- [Samuel Smith](https://github.com/SmithSamuelM), [Prosapien](https://prosapien.com/)

**Editors:**

- [Kevin Griffin](https://github.com/m00sey), [GLEIF](https://gleif.org/)

**Contributors:**

- [Henk van Cann](https://github.com/henkvancann), [Blockchainbird](https://blockchainbird.org/)
- [Kor Dwarshuis](https://github.com/kordwarshuis), [Blockchainbird](https://blockchainbird.org/)

**Participate:**

~ [GitHub repo](https://github.com/trustoverip/tswg-cesr-specification)
~ [Commit history](https://github.com/trustoverip/tswg-cesr-specification/commits/main)

[//]: # (\maketitle)

[//]: # (\newpage)

[//]: # (\toc)

[//]: # (\newpage)

[//]: # (\newpage)

[//]: # (::: introtitle)

## Introduction

[//]: # (:::)

The Composable Event Streaming Representation (CESR) is a dual text-binary encoding format that has the unique property of text-binary concatenation composability. This Composability property enables the round-trip conversion en-masse of concatenated Primitives between the text domain and binary domain while maintaining the separability of individual Primitives. This enables convenient usability in the text domain and compact transmission in the binary domain. CESR Primitives are self-framing. CESR supports self-framing Group Codes that enable stream processing and pipelining in both the text and binary domains. CESR supports composable text-binary encodings for general data types as well as suites of cryptographic material. Popular cryptographic material suites have compact encodings for efficiency, while less compact encodings provide sufficient extensibility to support all foreseeable types. CESR streams also support interleaved JSON, CBOR, and MGPK serializations. CESR is a universal encoding that uniquely provides dual text and binary domain representations via composable conversion. The CESR protocol is used by other protocols such as [[1](#ref1)].

One way to better secure Internet communications is to use cryptographically verifiable Primitives and data structures inside Messages and in support of messaging protocols. Cryptographically verifiable Primitives provide essential building blocks for zero-trust computing and networking architectures. Traditionally, Cryptographic Primitives, including but not limited to digests, salts, seeds (private keys), public keys, and digital signatures, have been largely represented in some binary encoding. This limits their usability in domains or protocols that are human-centric or equivalently that only support ASCII text-printable characters [[ref: RFC20]]. These domains include source code, documents, system logs, audit logs, legally defensible archives, Ricardian contracts, and human-readable text documents of many types [[RFC4627](#RFC4627)].

Generic binary-to-text, [[12](#ref12)], or simply textual encodings such as Base64 [[RFC4648](#RFC4648)], do not provide any information about the type or size of the underlying Cryptographic Primitive. Base64 only provides "value" information. More recently, [[10](#ref10)] was developed as a fit-for-purpose textual encoding of Cryptographic Primitives for shared distributed ledger applications that, in addition to value, may include information about the type and, in some cases, the size of the underlying Cryptographic Primitive [[11](#ref11)]. Each application, however, may use a non-interoperable type and optionally size encoding because a binary encoding may include as a subset some codes that are in the text-printable compatible subset of [[2](#ref2)] such as ISO Latin-1, [[14](#ref14)] or UTF-8, [[13](#ref13)]. Interestingly, for a given Cryptographic Primitive, a text-printable type code from a binary code table could be found serendipitously from a set of binary encodings. This is the case for the Multicodec encodings, which are binary but include a subset of "serendipitous" ASCII codes. [[8](#ref8)] [[7](#ref7)] [[ref: IPFS]]. Indeed, some [[10](#ref10)] applications take advantage of the binary MultiCodec tables but only use serendipitous text-compatible type codes. Serendipitous text encodings in binary code tables do not generally work for any size or type. So, the serendipitous approach is not universally applicable and is no substitute for a true textual encoding protocol for Cryptographic Primitives.

A textual or binary encoding that includes type, size, and value is Self-Framing. A self-framing Primitive may be extracted from a stream of characters or bytes without needing any additional delimiting characters. Thus, a stream of concatenated Primitives may be parsed without the need to encapsulate each Primitive inside a set of delimiters or an envelope. A textual Self-Framing encoding provides the core capability for a streaming text protocol like [[15](#ref15)] or [[16](#ref16)]. Although a first-class textual encoding of Cryptographic Primitives is the primary motivation for the CESR protocol, CESR is sufficiently flexible and extensible to support other useful data types, such as integers of various sizes, floating-point numbers, date-times as well as generic text. Thus, the CESR protocol is generally useful to encode data structures of all types into text, not merely those that contain Cryptographic Primitives.

Textual encodings have numerous usability advantages over binary encodings. The one advantage, however, of a binary encoding over text is compactness. An encoding protocol with the property called text-binary concatenation composability or, more succinctly, Composability enables both text's usability and binary's compactness. Composability may be the most uniquely innovative and useful feature of the CESR encoding protocol.

No standard text-based encoding protocol provides universal type, size, and value encoding for Cryptographic Primitives as compact atomic values. Providing this capability is one of the primary motivations for the CESR encoding protocol. But text-based atomic cryptographic primitives alone are insufficient for cryptography-heavy protocols. Grouping those primitives into cryptographically verifiable data structures, including messages with attachments, is also essential.  Consequently, CESR provides encodings for groups or collections of primitives such as lists, field maps, fixed field data structures, messages, attachments to messages, and arbitrary collections of groups. 

Like primitives, CESR group encodings are Self-Framing. This enables efficient stream processing of CESR streams.  A CESR parser can efficiently extract whole groups from the stream without parsing into the group. The extracted groups can then be diverted to other processor resources to be processed in parallel. This enables pipelining of CESR streams and messages within a stream.

The support for efficient stream processing is reflected in how a cryptographic commitment to some data is associated with that data. For example, a serialized data structure that constitutes a message may be signed digitally. The signature constitutes a non-repudiable commitment by the holder of the private key to the message. Cryptographically, the signature (commitment) can not be part of the data it signs (commits to).  Therefore, the signature must be attached to the message in some way. This constraint also applies to other commitments like cryptographic digests (hashes). The signature may be used as a strong authentication factor for the message. A stream processor may want to drop any messages whose signatures do not verify. One common way of associating commitments to a message is to create a new message that acts as a wrapper or envelope on the original message. The wrapper message includes both the original message and the commitment. However, enveloping or wrapping may defeat efficient stream processing, especially when that envelope is block delimited. The parser now has to parse into the wrapper to find the signature to verify it against the message. The wrapper is discarded.  A more stream-processing-friendly approach is to attach commitments to messages as Self-Framing stream parts without creating disposable wrappers. Consequently, CESR provides Self-Framing group encodings for attachments instead of wrappers. Properly, in CESR parlance, a full Message consists of a Message Body plus Attachments.

## Status of This Memo

Information about the current status of this document, any errata,
and how to provide feedback on it, may be obtained at
[https://github.com/trustoverip/tswg-cesr-specification](https://github.com/trustoverip/tswg-cesr-specification).

## Copyright Notice

This specification is subject to the **OWF Contributor License Agreement 1.0 - Copyright**
available at
[https://www.openwebfoundation.org/the-agreements/the-owf-1-0-agreements-granted-claims/owf-contributor-license-agreement-1-0-copyright](https://www.openwebfoundation.org/the-agreements/the-owf-1-0-agreements-granted-claims/owf-contributor-license-agreement-1-0-copyright).

If source code is included in the specification, that code is subject to the
[Apache 2.0 license](https://www.apache.org/licenses/LICENSE-2.0.txt) unless otherwise marked. In the case of any conflict or
confusion between the OWF Contributor License and the designated source code license within this specification, the terms of the OWF Contributor License MUST apply.

These terms are inherited from the Technical Stack Working Group at the Trust over IP Foundation. [Working Group Charter](https://trustoverip.org/wp-content/uploads/TSWG-2-Charter-Revision.pdf).


## Terms of Use

These materials are made available under and are subject to the [OWF CLA 1.0 - Copyright & Patent license](https://www.openwebfoundation.org/the-agreements/the-owf-1-0-agreements-granted-claims/owf-contributor-license-agreement-1-0-copyright-and-patent). Any source code is made available under the [Apache 2.0 license](https://www.apache.org/licenses/LICENSE-2.0.txt).

THESE MATERIALS ARE PROVIDED “AS IS.” The Trust Over IP Foundation, established as the Joint Development Foundation Projects, LLC, Trust Over IP Foundation Series ("ToIP"), and its members and contributors (each of ToIP, its members and contributors, a "ToIP Party") expressly disclaim any warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to the materials. The entire risk as to implementing or otherwise using the materials is assumed by the implementer and user. 
IN NO EVENT WILL ANY ToIP PARTY BE LIABLE TO ANY OTHER PARTY FOR LOST PROFITS OR ANY FORM OF INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES OF ANY CHARACTER FROM ANY CAUSES OF ACTION OF ANY KIND WITH RESPECT TO THESE MATERIALS, ANY DELIVERABLE OR THE ToIP GOVERNING AGREEMENT, WHETHER BASED ON BREACH OF CONTRACT, TORT (INCLUDING NEGLIGENCE), OR OTHERWISE, AND WHETHER OR NOT THE OTHER PARTY HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

[//]: # (\mainmatter)

[//]: # (\doctitle)

## Scope

Implementation design of a protocol-based data serialization specification that supports loss-less round-tripping between text and binary representations of compositions of primitives and groups of primitives. The encoding scheme includes first-class cryptographically agile encodings for the full range of cryptographic primitives such as random numbers, digests, secrets, private keys, public keys, signatures, encrypted data, etc. This enables cryptographic heavy protocols to succinctly represent integrated cryptographic primitives in the text domain for improved usability and readability while supporting loss-less round trip convertibility to binary for more compact transmission and storage. This better supports the increasing demand for cryptographic heavy protocols for enhanced security. Also supported are interleaved JSON, CBOR, and MGPK encodings of field maps that contain cryptographic primitives as field values. The application scope includes any electronically transmitted information.  The implementation dependency scope includes Base64 encoding/decoding libraries, standardized cryptographic primitive definitions, JSON, CBOR, and MGPK libraries.


## Normative references

[a]. IETF RFC-2119 Key words for use in RFCs to Indicate Requirement Levels
[a]: https://www.rfc-editor.org/rfc/rfc2119.txt

[b]. IETF RFC-4648 Base64 
[b]: https://www.rfc-editor.org/rfc/rfc4648.txt

[c]. IETF RFC-8259 JSON 
[c]: https://www.rfc-editor.org/rfc/rfc8259.txt

[d]. IETF RFC-8949 CBOR 
[d]: https://www.rfc-editor.org/rfc/rfc8949.txt

[e]. MessagePack Specification MGPK
[e]: https://github.com/msgpack/msgpack/blob/master/spec.md

[f]. Black3 Specification Blake3
[f]: https://github.com/BLAKE3-team/BLAKE3-specs



## Terms and Definitions

For the purposes of this document, the following terms and definitions apply.

ISO and IEC maintain terminological databases for use in standardization at the following addresses:

 - ISO Online browsing platform: available at <https://www.iso.org/obp>
 - IEC Electropedia: available at <http://www.electropedia.org/>

