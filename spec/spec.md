Composable Event Streaming Representation (CESR)
================================================

**Specification Status**: v1.0 Draft

**Latest Draft:**

[https://github.com/trustoverip/tswg-cesr-specification](https://github.com/trustoverip/tswg-cesr-specification)

**Author:**

- [Samuel Smith](https://github.com/SmithSamuelM), [Prosapien](https://prosapien.com/)

**Editors:**

**Contributors:**

**Participate:**

~ [GitHub repo](https://github.com/trustoverip/tswg-cesr-specification)
~ [Commit history](https://github.com/trustoverip/tswg-cesr-specification/commits/main)

[//]: # (\maketitle)

[//]: # (\newpage)

[//]: # (\toc)

[//]: # (\newpage)

[//]: # (::: forewordtitle)

## Foreword

The foreword goes here.

[//]: # (:::)

[//]: # (\newpage)

[//]: # (::: introtitle)

## Introduction

[//]: # (:::)

The Composable Event Streaming Representation (CESR) is a dual text-binary encoding format that has the unique property of text-binary concatenation composability. This composability property enables the round-trip conversion en-masse of concatenated Primitives between the text domain and binary domain while maintaining the separability of individual Primitives. This enables convenient usability in the text domain and compact transmission in the binary domain. CESR Primitives are self-framing. CESR supports self-framing group codes that enable stream processing and pipelining in both the text and binary domains. CESR supports composable text-binary encodings for general data types as well as suites of cryptographic material. Popular cryptographic material suites have compact encodings for efficiency while less compact encodings provide sufficient extensibility to support all foreseeable types. CESR streams also support interleaved JSON, CBOR, and MGPK serializations. CESR is a universal encoding that uniquely provides dual text and binary domain representations via composable conversion. The CESR protocol is used by other protocols such as [[ref: KERI]].

One way to better secure Internet communications is to use cryptographically verifiable primitives and data structures both inside messages and in support of messaging protocols. Cryptographically verifiable primitives provide essential building blocks for zero-trust computing and networking architectures. Traditionally, Cryptographic primitives including but not limited to digests, salts, seeds (private keys), public keys, and digital signatures have been largely represented in some type of binary encoding. This limits their usability in domains or protocols that are human-centric or equivalently that only support ASCII text-printable characters [[ref: RFC20]]. These domains include source code, documents, system logs, audit logs, Ricardian contracts, and human-readable text documents of many types [[spec: RFC4627]].

Generic binary-to-text, [[ref: Bin2Txt]], or simply textual encodings such as Base64 [[spec: RFC4648]], do not provide any information about the type or size of the underlying Cryptographic primitive. Base64 only provides "value" information. More recently [[ref: Base58Check]] was developed as a fit-for-purpose textual encoding of Cryptographic primitives for shared distributed ledger applications that in addition to value may include information about the type and in some cases the size of the underlying Cryptographic primitive, [[ref: WIF]]. But each application may use a non-interoperable encoding of type and optionally size. Interestingly because a binary encoding may include as a subset some codes that are in the text-printable compatible subset of [[ref: ASCII]] such as ISO Latin-1, [[ref: Latin1]] or UTF-8, [[ref: UTF8]], for a given Cryptographic primitive, a text-printable type code from a binary code table serendipitously could be found, such as the table [[ref: MCTable]] from [[ref: MultiCodec]] for [[ref: IPFS]]. Indeed some [[ref: Base58Check]] applications take advantage of the binary MultiCodec tables but only used serendipitous text-compatible type codes. Serendipitous text encodings that appear in binary code tables do not, however, work in general for any size or type. So, the serendipitous approach is not universally applicable and is no substitute for a true textual encoding protocol for Cryptographic primitives.

In general, there is no standard text-based encoding protocol that provides universal type, size, and value encoding for Cryptographic primitives. Providing this capability is the primary motivation for the CESR encoding protocol.

Importantly, a textual encoding that includes type, size, and value is self-framing. A self-framing text Primitive may be parsed without needing any additional delimiting characters. Thus, a stream of concatenated Primitives may be individually parsed individually without the need to encapsulate the Primitives inside textual delimiters or envelopes and a textual self-framing encoding provides the core capability for a streaming text protocol like [[ref: STOMP]] or [[ref: RAET]]. Although a first-class textual encoding of Cryptographic primitives is the primary motivation for the CESR protocol, CESR is sufficiently flexible and extensible to support other useful data types, such as integers of various sizes, floating-point numbers, date-times as well as generic text. Thus the CESR protocol is generally useful to encode data structures of all types into text not merely those that contain Cryptographic primitives.

Textual encodings have numerous usability advantages over binary encodings. The one advantage, however, that a binary encoding has over text is compactness. An encoding protocol that has the property called text-binary concatenation composability or more succinctly, Composability. Composability enables both the usability of text and the compactness of binary. Composability may be the most uniquely innovative and useful feature of the CESR encoding protocol.

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
confusion within this specification between the OWF Contributor License 
and the designated source code license, the terms of the OWF Contributor License shall apply.

These terms are inherited from the Technical Stack Working Group at the Trust over IP Foundation. [Working Group Charter](https://trustoverip.org/wp-content/uploads/TSWG-2-Charter-Revision.pdf).


## Terms of Use

These materials are made available under and are subject to the [OWF CLA 1.0 - Copyright & Patent license](https://www.openwebfoundation.org/the-agreements/the-owf-1-0-agreements-granted-claims/owf-contributor-license-agreement-1-0-copyright-and-patent). Any source code is made available under the [Apache 2.0 license](https://www.apache.org/licenses/LICENSE-2.0.txt).

THESE MATERIALS ARE PROVIDED “AS IS.” The Trust Over IP Foundation, established as the Joint Development Foundation Projects, LLC, Trust Over IP Foundation Series ("ToIP"), and its members and contributors (each of ToIP, its members and contributors, a "ToIP Party") expressly disclaim any warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to the materials. The entire risk as to implementing or otherwise using the materials is assumed by the implementer and user. 
IN NO EVENT WILL ANY ToIP PARTY BE LIABLE TO ANY OTHER PARTY FOR LOST PROFITS OR ANY FORM OF INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES OF ANY CHARACTER FROM ANY CAUSES OF ACTION OF ANY KIND WITH RESPECT TO THESE MATERIALS, ANY DELIVERABLE OR THE ToIP GOVERNING AGREEMENT, WHETHER BASED ON BREACH OF CONTRACT, TORT (INCLUDING NEGLIGENCE), OR OTHERWISE, AND WHETHER OR NOT THE OTHER PARTY HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

[//]: # (\mainmatter)

[//]: # (\doctitle)

## Scope

Needed

## Normative references

This document has no normative references.


## Terms and Definitions

For the purposes of this document, the following terms and definitions apply.

ISO and IEC maintain terminological databases for use in standardization at the following addresses:

 - ISO Online browsing platform: available at <https://www.iso.org/obp>
 - IEC Electropedia: available at <http://www.electropedia.org/>

[[def: Composability]]

~ short for text-binary concatenation composability. An encoding has Composability when any set of self-framing concatenated Primitives expressed in either the text domain or binary domain may be converted as a group to the other domain and back again without loss.

[[def: Domain ]]

~ The Primitives that are defined in CESR inhabit three different domains each with a different abstract representation – Text (T), Binary (B) and Raw binary (R).

[[def: Primitive]]

~ a serialization of a unitary value.  All Primitives in KERI must be expressed in CESR.

[[def: Cryptographic Primitive]]

~ the serialization of a value associated with a cryptographic operation including but not limited to a digest (hash), a salt, a seed, a private key, a public key, or a signature. 

[[def: Autonomic identifier (AID)]]

~ a self-managing cryptonymous identifier that must be self-certifying (self-authenticating) and must be encoded in CESR as a qualified Cryptographic primitive. 

[[def: Key Event Receipt Infrastructure (KERI)]]

~ or the KERI protocol, is an identity system-based secure overlay for the Internet.  

[[def: Framing codes]]

~ codes that delineate a number of characters or bytes, as appropriate, that can be extracted atomically from a Stream.

[[def: Group/Count codes]]

~ special Framing codes that can be specified to support groups of Primitives which make them pipelinable.  Self-framing grouping using Count codes is one of the primary advantages of composable encoding.  

[[def: Message]]

~ consists of a serialized data structure that comprises its body and a set of serialized data structures that are its attachments. Attachments may include but are not limited to signatures on the body.

[[def: Version]]

~ the CESR version is provided by a special Count code that specifies the Version of all the the CESR code tables in a given stream or stream section.

[[def: Variable length]]

~ todo

::: issue
https://github.com/trustoverip/tswg-cesr-specification/issues/17
:::

[[def: Stream]]

~ a CESR Stream is any set of concatenated Primitives, concatenated groups of Primitives or hierarchically composed groups of Primitives.

[[def: Version string]]

~ the first field in any top-level KERI field map in which it appears.

[[ref: Tritet]]

~ todo 

::: issue
https://github.com/trustoverip/tswg-cesr-specification/issues/11
:::

[[ref: Quadlet]]

~ todo 

::: issue
https://github.com/trustoverip/tswg-cesr-specification/issues/12
:::

[[ref: Stable]]

~ todo 

::: issue
https://github.com/trustoverip/tswg-cesr-specification/issues/12
:::

[//]: # (Composability and Domain representations {#sec:content})


## Composability and Domain representations

### Composability

An encoding has Composability when any set of self-framing concatenated Primitives expressed in either the Text domain or Binary domain may be converted as a group to the other Domain and back again without loss. Essentially, Composability provides round-trippable lossless conversion between Text and Binary domain representations of any set of concatenated Primitives when converted as a set not merely individually. The property enables a Stream processor to safely convert en-masse a Stream in the  Text domain to an equivalent Stream in the Binary domain  for compact transmission that may be safely converted back to Text domain en-masse by a Stream processor at the other end for further processing or archival storage. The use of Count codes as independently composable groups enables hierarchical compositions. Such a hierarchically composable encoding protocol enables pipelining (multiplexing and de-multiplexing) of complex Streams in either text or compact binary. This allows management at scale for high-bandwidth applications that benefit from core affinity off-loading of Streams [[ref: Affinity].

### Abstract domain representations

The Cryptographic primitives defined in CESR inhabit three different Domains each with a different representation.  The first Domain is called Text for streamable text and is denoted as ‘T’. The second Domain is called Binary for streamable binary and is denoted as ‘B’. Composability is defined between the ‘T’ and ‘B’ domains. The third Domain is called Raw, which is non-streamable binary, and is denoted as ‘R’. The third Domain is special because Primitives in this Domain are represented by a pair or two-tuple of values namely (text code, raw binary) or `(code, raw)` for short. The text code element of the ‘R’ Domain pair is a string of one or more text characters that provides the type and size information for the encoded Primitive when in the ‘T’ Domain. The raw binary element is composed of bytes. The actual use of Cryptographic primitives happens in the ‘R’ Domain using the raw binary element of the `(code, raw)` pair. Cryptographic primitive values are usually represented as strings of bytes that represent very large integers. Cryptographic libraries typically assume that the inputs and outputs of their functions will be such strings of bytes. The raw binary element of the ‘R’ Domain pair is such a string of bytes. The CESR protocol, however, is not limited to merely encoding Cryptographic primitives but any primary data type (numbers, text, datetimes, lists, maps) may be encoded in a composable way.

A given Primitive in the ‘T’ Domain is denoted with `t`.  A member of an indexed set of Primitives in the ‘T’ Domain is denoted with `t[k]`. Likewise, a given Primitive in the ‘B’ Domain is denoted with `b`. A member of an indexed set of Primitives in the ‘B’ Domain is denoted with `b[k]`. Similarly, a given Primitive in the ‘R’ Domain is denoted with `r`. A member of an indexed set of Primitives in the R’ Domain is denoted with `r[k]`.

#### Transformations between domains

Although the Composability property mentioned in the previous section only applies to conversions back and forth between the ‘T’, and ‘B’, Domains, conversions between the ‘R’, and ‘T’ Domains, as well as conversions between the ‘R’ and ‘B’ Domains,  also are defined and supported by the protocol as described in detail in this section. As a result, there is a total of six transformations, one in each direction, among the three Domains.

Let `T(B)` denote the abstract transformation function from the ‘B’ Domain to the ‘T’ Domain. This is the dual of `B(T)` below.

Let `B(T)` denote the abstract transformation function from the ‘T’ Domain to the ‘B’ Domain. This is the dual of `T(B)` above.

Let `T(R)` denote the abstract transformation function from the ‘R’ Domain to the ‘T’ Domain. This is the dual of `R(T)` below.

Let `R(T)` denote the abstract transformation function from the ‘T’ Domain to the ‘R’ Domain. This is the dual of `T(R)` above.

Let `B(R)` denote the abstract transformation function from the ‘R’ Domain to the ‘B’ Domain. This is the dual of `R(B)` below.

Let `R(B)` denote the abstract transformation function from the ‘B’ Domain to the ‘R’ Domain. This is the dual of `B(R)` above.

Given these transformations, a complete a circuit of transformations can be completed that starts in any of the three Domains and then crosses over the other two Domains in either direction. 

##### Examples of circuits of transformations

###### Example 1
Starting in the ‘R’ Domain, a circuit that crosses into the ‘T' and ‘B’ Domains can be traversed and then crossed back into the ‘R’ Domain as follows:

```text
R->T(R)->T->B(T)->B->R(B)->R
```

##### Example 2
Likewise, starting in the ‘R’ Domain, a circuit that crosses into the ‘B’ and ‘T’ Domains and then crossed back into the ‘R’ Domain as follows:

```text
R->B(R)->B->T(B)->T->R(T)->R
```

Concatenation composability property

Let `+` represent concatenation. Concatenation is associative and may be applied to any two Primitives or any two groups or sets of concatenated Primitives. 

##### Examples of circuits of transformations

###### Example 1

Starting in the ‘R’ Domain, a circuit that crosses into the ‘T' and ‘B’ Domains can be traversed and then crossed back into the ‘R’ Domain as follows:

```text
R->T(R)->T->B(T)->B->R(B)->R
```

##### Example 2

Likewise, starting in the ‘R’ Domain, a circuit that crosses into the ‘B’ and ‘T’ Domains and then crossed back into the ‘R’ Domain as follows:

```text
R->B(R)->B->T(B)->T->R(T)->R
```

Concatenation composability property

Let `+` represent concatenation. Concatenation is associative and may be applied to any two Primitives or any two groups or sets of concatenated Primitives. 

### Concrete domain representations

The Text, ‘T’, domain representations in CESR use only the characters from the URL and filename safe variant of the IETF RFC-4648 Base64 standard [[spec: RFC4648]]. Unless otherwise indicated, all references to Base64 [[spec: RFC4648]] in this document imply the URL and filename safe variant. The URL and filename safe variant of Base64 uses in order the 64 characters `A to Z`, `a to z`, `0 to 9`, `-`, and `_` to encode 6 bits of information. In addition, Base64 uses the `=` character for padding, but CESR does not use the `=` character for any purpose because all CESR-encoded Primitives are composable.

The fact that Base64 [[spec: RFC4648]] by itself does not satisfy the Composability property is notable and must employ pad characters to ensure one-way convertibility between the Binary domain and the Text domain.

In CESR, however, both ‘T’ and ‘B’ Domain representations include a prepended Framing code prefix that is structured in such a way as to ensure Composability.

### Example of using prepended code prefixes

Suppose, that Base64 characters are used in the Text domain and binary bytes are used in the Binary domain, called respectively, naive text and naive binary encodings and Domains. Recall that a byte encodes 8 bits of information and a Base64 character encodes 6 bits of information. Furthermore, suppose that there are three Primitives denoted `a`, `b`, and `c` in the naive Binary domain with lengths of 1, 2, and 3 bytes, respectively.

In the following diagrams, each byte is denoted in a naive binary Primitive with zero-based most significant bit first indices, e.g., `a1` is bit one from `a`, `a0` is bit zero, and `A0` for byte zero, `A1` for byte 1, etc.

The byte and bit-level diagrams for `a` are shown below, where `A` is used to denote its bytes:

```text
|           A0          |
|a7:a6:a5:a4:a3:a2:a1:a0|
```

Likewise, for `b` below:

```text
|           B1          |           B0          |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
```

And finally, for `c` below:

```text
|           C2          |           C1          |           C0          |
|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|

## Conversions

When doing a naive Base64 conversion of a naive binary Primitive, one Base64 character represents only six bits from a given byte. In the following diagrams, each character of a Base64 conversion is denoted using zero-based indices, with the most significant character first.

Therefore, encoding `a` in Base64 requires at least two Base64 characters because the zeroth character only captures the six bits from the first byte, and another character is needed to capture the other two bits. The convention in Base64 uses a Base64 character where the non-coding bits are zeros. This is diagrammed as follows:

### Diagram 1

```text
|           A0          |
|a7:a6:a5:a4:a3:a2:a1:a0|z3:z2:z1:z0|
|        T1       |        T0       |
```
where `aX` represents a bit from `A0` and `zX` represents a zeroed pad bit, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string.

Naive Base64 encoding always pads each individual conversion of a string of bytes to an even multiple of four characters. This provides a property that is not true Composability but does ensure that multiple distinct concatenated conversions from binary to Base64 text are separable. It may be described as a sort of one-way composability. So, with pad characters, denoted by replacing the spaces with `=` characters, the Base64 conversion of `a` is as follows:

#### Diagram 2

```text
|           A0          |
|a7:a6:a5:a4:a3:a2:a1:a0|z3:z2:z1:z0|
|        T3       |        T2       |========P1=======|========P0=======|
```
where `aX` represents a bit from `a`, `AX` represents a byte from `a`, `zX` represents a zeroed pad bit, `PX` represents a trailing pad character, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string. We see that Base64 conversion effectively left shifts `a` by four bits plus two pad characters. In other words, the Base64 conversion of `a` is no longer right-aligned with respect to the trailing Base64 character.

Likewise, `b` requires at least three Base64 characters to capture all of its sixteen bits of information as follows:

#### Diagram 3

```text
|           B1          |           B0          |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|z1:z0|
|        T2       |        T1       |        T0       |
```
Where:       `bX` represents a bit from `b`, `BX` represents a byte from `b`, `zX` represents a zeroed pad bit, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string.

Alignment on a four-character (24-bit) boundary requires one pad character this becomes:

#### Diagram 4

```text
|           B1          |           B0          |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|z1:z0|
|        T2       |        T1       |        T0       |========P0=======|

```
where `bX` represents a bit from `b`, `BX` represents a byte from `b`, `zX` represents a zeroed pad bit, `PX` represents a trailing pad character, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string. We see that Base64 conversion effectively left shifts `a` by two bits plus one pad character. In other words, the Base64 conversion of `b` is no longer right-aligned with respect to the trailing Base64 character.

Finally, `c` requires exactly four Base64 characters to capture all of its twenty-four bits of information. There are no pad characters required.

#### Diagram 5

```text
|           C0          |           C1          |           C2          |
|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|
|        T3       |        T2       |        T1       |        T0       |
```
Where:        `cX` represents a bit from `c`, `CX` represents a byte from `c`, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string. There are no bit shifts because there are no pad bits nor pad characters needed, and the resulting Base64 conversion is right aligned with respect to the trailing Base64 character.

Suppose `a + b` now is concatenated into a three-byte composition in the naive Binary domain before Base64 encoding the concatenated whole. 

#### Diagram 6

```text
|           A0          |           B1          |           B0          |
|a7:a6:a5:a4:a3:a2:a1:a0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|        T3       |        T2       |        T1       |        T0       |
```

The least significant two bits of `A0` are encoded into the same character, `T2` as the four most significant four bits of `B1`. Therefore, a Text domain parser would be unable to cleanly de-concatenate on a character-by-character basis the conversion of `a + b` into separate Text domain Primitives. Therefore, standard (naive) binary to Base64 conversion does not satisfy the Composability constraint.

Starting instead in the Text domain with Primitives `u` and `v` of lengths 1 and 3 characters, respectively, these two Primitives can be concatenated as `u + v` in the Text domain and then converted as a whole to naive binary. 

#### Diagram 7

```text
|        U0       |        V2       |        V1       |        V0       |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|           B2          |           B1          |           B0          |
```

All six bits of information in `U0` are  included in `B2` along with the least significant two bits of information in `V2`. Therefore, a Binary domain parser is unable to cleanly de-concatenate on a byte-by-byte basis the conversion of `u + v` into separate Binary domain Primitives. Therefore, standard (naive) Base64 to binary conversion does not satisfy the Composability constraint.

The Composability property is satisfied only if each Primitive in the ‘T’ Domain is an integer multiple of four Base64 characters (24 bits) and each Primitive in the ‘B’ Domain is an integer multiple of three bytes (24 bits). Each of either four Base64 text characters or three binary bytes captures 24 bits of information. Twenty-four is the least common multiple of six and eight. Therefore, in order to cleanly capture integer multiples of twenty-four bits of information, Primitive lengths must be integer multiples of either four Base64 text characters or three binary bytes in their respective Domains. Given that the constraint of alignment on 24-bit boundaries in either Text domain or Binary domain is satisfied, the conversion of concatenated Primitives in one Domain never results in the same byte or character in the converted Domain sharing bits from two adjacent Primitives. This constraint of 24-bit alignment, therefore, satisfies the Composability property.

To elaborate, when converting Streams made up of concatenated Primitives back and forth between the ‘T’ and ‘B’ Domains, the converted results will not align on byte or character boundaries at the end of each Primitive unless the Primitives themselves are integer multiples of twenty-four bits of information. In other words, all Primitives must be aligned on 24-bit boundaries to satisfy the Composability property. This means that the length of any Primitive in the ‘B’ Domain must be an integer multiple of three binary bytes with a minimum length of three binary bytes. Likewise, this means that the length of any Primitive in the ‘T’ Domain must  be an integer multiple of 4 Base64 characters with a minimum length of four Base64 characters.

### Stable Framing Codes in the text domain

There are many coding schemes that could satisfy the Composability constraint of alignment on 24-bit boundaries. The main reason for using a ‘T’ Domain-centric encoding is higher usability, readability, or human friendliness. A primary design goal of CESR is to select an encoding approach that provides high usability, readability, or human friendliness in the ‘T’ domain. This type of usability goal simply is not realizable in the ‘B’ Domain. The ‘B’ Domain's purpose is merely to provide convenient compactness at scale. Usability in the ‘T’ Domain is maximized when the type portion of the prepended framing code and its postpended value are Stable, i.e., ‘invariant’. 

#### Stable type encoding

Stable type coding makes it much easier to recognize Primitives of a given type when debugging source, reading Messages, or documents in the ‘T’ domain that include encoded Primitives. This is true even when those Primitives have different lengths or values. For Primitive types that have fixed lengths, i.e., all Primitives of that type have the same length, Stable type coding aids not only visual type but visual size recognition.

The usability of Stable type coding is maximized when the type portion appears first in the Framing code. Stability also requires that for a given type, the type coding portion must consume a fixed integer number of characters in the ‘T’ Domain. To clarify, as used here, Stable type coding in the ‘T’ Domain never shares information bits with either length or value coding in any given Framing code character and appears first in the Framing code. Stable type coding in the ‘T’ domain translates to Stable type coding in the ‘B’ Domain except that the type coding portion of the Framing code may not respect byte boundaries. This is an acceptable tradeoff because binary-domain parsing tools easily accommodate bit fields and bit shifts while text-domain parsing tools do not. Generally, Text domain parsing tools only process whole characters. This is another reason to impose a stability constraint on the ‘T’ Domain type coding instead of the ‘B’ Domain.

#### Stable value encoding

A secondary usability constraint is recognizable or readable Stable value coding in the Text, ‘T’, domain. Not all Primitives benefit from Stable value coding. Any representation of a value that is a long random string of characters is essentially unreadable or recognizable versus some other representation. Bit shifts of the value, as long as they are static, do not change the readability. This is not true, however, of values that are small numbers. Base64 encodings of small numbers are readable. for example, the numerical sequence of decimal numbers, `0, 1, 2`, is recognizable as the sequence of Base64 characters, `A, B, C`. Thus, all else equal, readable Stable value encodings also contribute to usability, at least in some cases.

### Code characters and lead bytes

There are two ways to provide the required alignment on 24-bit boundaries to satisfy the Composability property. One is to post-pad, with trailing pad characters, `=`, the Text domain encoding to ensure that the ‘T’ Domain Primitive has a total size (length) that is an integer multiple of 4. This is what naive Base64 encoding does. The other way is to pre-pad leading bytes of zeros to the raw binary value before conversion to Base64 to ensure the total size of the raw binary value with pre-pad bytes is an integer multiple of 3 bytes. This ensures that the size in characters of the Base64 conversion of the pre-padded raw binary is an integer multiple of 4 characters.

Given the second approach, there is one of two options that depend on the specific code. In the first option, an appropriate number of text characters that result from the conversion of a porting of the leading pre-pad zero bytes are replaced with the appropriate number of code characters. In the second option, the code characters are pre-pended to the conversion with leading zeros intact. In the second option, the length of the pre-pended type code must also, thereby, be an integer multiple of 4 characters. In either option, the total length of the ‘T’ Domain Primitive with code is an integer multiple of 4 characters.

The first option may be more compact in some cases than the second. The second option may be easier to compute in some cases. The most significant advantage of the second option is that the value portion of is Stable and more readable both in the Text, ‘T’, Domain and in the Binary, ‘B’, Domain because the value portion is not shifted by the Base64 conversion as it is with the first option.

In order to avoid confusion with the use of the term `pad character`, when pre-padding with bytes that are not replaced later, the term `lead bytes` is used. The term pad may be confusing not merely because both ways use a type of padding but it is also true that the number of pad characters when padding post-conversion equals the number of lead bytes when padding pre-conversion.

Suppose that the raw binary value is 32 bytes in length. The next higher integer multiple of 3 is 33 bytes. Thus 1 additional leading pad byte is needed to make the size (length in byte) of raw binary an integer multiple of 3. The 1 lead byte makes that combination a total of 33 bytes in length. The resultant Base64 converted value will be 44 characters in length, which is an integer multiple of 4 characters. In contrast, recall that when we convert a 32-byte raw binary value to Base64 the converted value will have 1 trailing pad character. In both cases, the resultant length in Base64 is 44 characters.

Similarly, a 64-byte raw binary value needs 2 lead bytes to make the combination 66 bytes in length where 66 is the next integer multiple of 3 greater than 64. When converted the result is 88 characters in length. The number of pad characters added on the result of the Base64 conversion of a 64-byte raw binary is also 2.

In summary, there are two possibilities for CESR's coding scheme to ensure a composable 24-bit alignment. The first is to add trailing pad characters post-conversion. The second is to add leading pad bytes pre-conversion. Because of the greater readability of the value portion of both the fully qualified Text, ‘T’, or fully qualified Binary, ‘B’, Domain representations, the second approach was chosen for CESR.

### Multiple code table approach

The design goals for CESR Framing codes include minimizing the Framing code size for the most frequently used (most popular) codes while also supporting a sufficiently comprehensive set of codes for all foreseeable current and future applications. This requires a high degree of both flexibility and extensibility. This is best achieved with multiple code tables each with a different coding scheme that is optimized for a different set of features instead of a single one-size-fits-all scheme. A specification that supports multiple coding schemes may appear on the surface to be much more complex to implement but careful design of the coding schemes can reduce implementation complexity by using a relatively simple single integrated parse and conversion table. Parsing in any given Domain given Stable type codes may then be implemented with a single function that simply reads the appropriate type selector in the table to know how to parse and convert the rest of the Primitive.

## Text coding scheme design 

### Text code size

Recall that the ‘R’ Domain representation is a pair`(text code, raw binary)`. The text code is Stable and begins with one or more Base64 characters that provide the Primitive type and may also include one or more additional characters that provide the length. The actual usable cryptographic material is provided by the raw binary element.

The corresponding ‘T’ Domain representation of this pair is created by first prepending leading pad bytes of zeros to the raw binary element. This result is then converted to Base64. Depending on the code, either the frontmost characters that result from the Base64 conversion of leading pad bytes of zeros are replaced with the text code element of appropriate size in characters, or an appropriately sized text code element is prepended to the conversion without replacing any characters.

Recall that when the length of a given naive binary string is not an integer multiple of three bytes, standard Base64 conversion software appends one or two pad characters to the resultant Base64 conversion.

With standard Base64 conversion that employs pad characters, the Text domain representation that results from the individual conversion of a set of binary strings when concatenated in the Text domain after conversion and stripping off pad characters is not necessarily equivalent to the Text domain representation that results from converting en-masse to text the concatenation of the same set of binary strings and then stripping off pad characters. In the latter case, knowledge of the set of binary strings is lost because the resultant conversion may have bits from two binary bytes concatenated in one text character. Restated, the problem with standard Base64 is that it does not preserve byte boundaries after the en-masse conversion of concatenated binary strings. Consequently, standard (naive) Base64 does not provide two-way or true Composability as defined above.

To elaborate, the number of pad characters appended with standard Base64 encoding is a function of the length of the binary string. Let ‘N’ be the length in bytes of the binary string. When `N mod 3 = 1`, then there are 8 bits in the remainder that must be encoded into Base64. Recall from the examples above that a single byte (8 bits) requires two Base64 characters. The first encodes 6 bits and the second the remaining 2 bits for a total of 8 bits. The last character is selected such that its non-coding 4 bits are zero. Thus, two additional pad characters are required to pad out the resulting conversion so that its length is an integer multiple of 4 Base64 characters. Furthermore, when `N mod 3 = 1`, then the addition of 2 more zeroed bytes to the length of the binary string such that `M = N + 2` would result in `M mod 3 = 0` or equivalently `N + 2 mod 3 = 0`.

Similarly, when `N mod 3 = 2`, then there are two bytes (16 bits) in the remainder that must be encoded into Base64. Recall from the examples above that two bytes (16 bits) require three Base64 characters. The first two encode 6 bits each (for 12 bits) and the third encodes the remaining 4 bits for a total of 16. The last character is selected such that its non-coding 2 bits are zero. Thus, one additional trailing pad character is required to pad out the resulting conversion so that its length is an integer multiple of 4 characters. Furthermore when
`N mod 3 = 2`, then the addition of 1 more byte of zeros added to the length of the binary string such that `M = N + 1` would result in `M mod 3 = 0` or equivalently `N + 2 mod 3 = 0`. Thus, the number of leading pre-pad zeroed bytes needed to align the binary string on a 24-bit boundary is the same as the number of trailing pad characters needed to align the converted Base64 text string on a 24-bit boundary.

Finally, when `N mod 3 = 0`, then the binary string is aligned on a 24-bit boundary and no trailing pad characters are required to ensure the length of the Base64 conversion is an integer multiple of 4 characters. Likewise, no leading pad bytes are required to ensure the length of the binary string is an integer multiple of 3 bytes.

Thus, in all three cases, the number of trailing post-pad characters, if any, needed to align the converted Base64 text string on a 24-bit boundary is the same as the number of leading pre-pad bytes, if any, needed to align the binary string on a 24-bit boundary.

The number of required trailing Base64 post-pad characters or equivalently the number of leading pre-pad zeroed bytes to ensure 24 bit alignment may be computed with the following formula:

`ps = (3 - (N mod 3)) mod 3)`, where `ps` is the pad size (pre-pad bytes or post-pad characters) and `N` is the size in bytes of the binary string.

Recall that Composability is provided here by prepending text codes that are of the appropriate length to ensure 24-bit boundaries in both the ‘T’ and the corresponding ‘B’ Domain. The advantage of this approach is that naive Base64 software tooling may be used to convert back and forth between the ‘T’ and ‘B’ Domains, i.e., `T(B)` is naive Base64 encode, and `B(T)` is naive Base64 decode. In other words, CESR Primitives are compatible with existing Base64 (RFC-4648) tooling. Whereas new software tooling is needed for conversions between the ‘R’ and ‘T’ Domains, e.g., `T(R)` and `R(T)` and the ‘R’ and ‘B’ Domains, e.g., `B(R)` and `R(B)`.

The pad size computation is also useful for computing the size of the text codes. Because true Composability also requires that the ‘T’ Domain value must be an integer multiple of 4 characters in length, the size of the text code also must be a function of the pad size, `ps`, and hence the length of the raw binary element, `N`. Thus the size of the text code in Base64 characters is a function of the equivalent pad size determined by the length `N mod 3` of the raw binary value. 

#### Example of pad size computation

Let ‘M’ be a non-negative integer-valued variable then :

|   Pad Size   |  Code Size  |
|:------------:|------------:|
|0|4•M|
|1|4•M + 1|
|2|4•M + 2|

The minimum code sizes are 1, 2, and 4 characters for pad sizes of 1, 2, and 0 characters with ‘M’ equaling 0, 0, and 1 respectively. By increasing ‘M’, there are larger code sizes for a given pad size.

### Pre-Padding before conversion

Returning to the examples above, observe what happens when the binary strings are pre-padded with zeroed leading pad bytes of the appropriate length given by `ps = (3 - (N mod 3)) mod 3)` where `ps` is the number of leading pad bytes and `N` is the length of the raw binary string before padding is prepended.

#### One-byte pre-padding

For the one-byte raw binary string `a`, `ps` is two. The pre-padded conversion results in the following:

```text
|           Z1          |           Z0          |           A0          |
|z7:z6:z5:z4:z3:z2:z1:z0|z7:z6:z5:z4:z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
|        T3       |        T2       |        T1       |        T0       |
```
Where:  `      ZX` represents a zeroed pre-pad byte, `zX` represents a zeroed pre-pad bit, `AX` represents a byte from `a`, `aX` represents a bit from `a`, and `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `a`.

It is noteworthy that the first two (i.e., `ps`) characters of the conversion, namely `T3T2`, does not include any bits of information from `a`. This also means that `T3T2` can be modified after conversion without impacting the appearance or value of the converted `a` that appears solely in `T1T0`, i.e., there is no overlap. Moreover, the resulting Base64 conversion of `a` is right aligned with respect to the trailing Base64 character. This means that the numerical values for `a` from such an unshifted Base64 conversion can be ‘read’ and understood.  This also means that a text-based parser on a character-by-character basis can cleanly process `T3T2` separate from the Base64 encoding of `a` that appears in `T1T0`. Given this separation, `T3T2` can be replaced with two-character Base64 textual type code `C1C0` as follows:

```text
|           Z1          |           Z0          |           A0          |
|z7:z6:z5:z4:z3:z2:z1:z0|z7:z6:z5:z4:z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
|        S1       |        S0       |        T1       |        T0       |
|s5:s4:s3:s2:s1:s0|s5:s4|s3:s2:s1:s0|z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
```
Where:        `ZX` represents a zeroed pre-pad byte, `zX` represents a zeroed pre-pad bit, `AX` represents a byte from `a`, `aX` represents a bit from `a`,  `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `a`, `SX` represents a Base64 code character replacing one of the `TX`, and `sX` is a code bit. The resultant four-character Base64 encoded Primitive would be `C1C0T1T0`.

When `C1C0T1T0` is converted back to binary from Base64, the result would be as follows:

```text
|        S1       |        S0       |        T1       |        T0       |
|s5:s4:s3:s2:s1:s0|s5:s4|s3:s2:s1:s0|z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
|           U1          |        U0             |           A0          |
```

Where:      `CX` represents a Base64 code character replacing one of the `TX`, `cX` is a code bit, `UX` represents a byte from the converted code char, which may include zeroed bits, `zX` represents a zeroed pre-pad bit, `AX` represents a byte from `a`, `aX` represents a bit from `a`, and `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `a`.

Stripping off `U1U0` leaves `a` in its original state. It is noteworthy that the code characters (only) are effectively left shifted 4 bits after conversion. The code characters `S1S0` can be recovered as the first two characters that are obtained from simply converting `U1O0` only back to Base64.

#### Two-byte pre-padding

For the two-byte would be `S0T2T1T0`.

When `S0T2T1T0` is converted back to binary from Base64, the result would be as follows:

```text
|        S0       |        T2       |        T1       |        T0       |
|s5:s4:s3:c2:cs:s0|z1:z0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|           U0          |           U1          |           A0          |
```
Where:      `SX` represents a Base64 code character replacing one of the `TX`, `sX` is a code bit, `UX` represents byte from converted code char which may include zeroed bits, `zX` represents a zeroed pre-pad bit, `BX` represents a byte from `b`, `bX` represents a bit from `b`, and `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `b`.

Stripping off `U0` leaves `b` in its original state. It is noteworthy is that the code character (only) is effectively left shifted 4 bits after conversion. The code character `S0` can be recovered as the first character obtained from simply converting `U0` only to Base64.

#### Three-byte pre-padding

For the three-byte raw binary string `c`, `ps` is zero. So pre-padding is not needed.
```text
|           C2          |           C1          |           C2          |
|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|
|        T3       |        T2       |        T1       |        T0       |
```
Where:      `cX` represents a bit from `c`, `CX` represents a byte from `c`, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string. There are no bit shifts because there are no pad bits nor pad characters needed, and the resulting Base64 conversion is right aligned with respect to the trailing Base64 character.

Without pad characters, however, there is no room to hold a type code. Consequently, any text type code is just prepended to the conversion. The prepended type code must  be an integer multiple of four Base64 characters. Let `S3S2S1S0` be the type code, then the full Primitive with code and converted raw binary is given by the eight-character Base64 string `S3S2S1S0T3T2T1T0`.

When `S3S2S1S0T3T2T1T0` is converted back to binary, there is no overlap or bit shifting because both the code and raw binary `c` are each separately aligned on twenty-four-bit boundaries.

#### Examples of pre-padding

Suppose that two-byte raw binary numbers are to be encoded into CESR using the pre-pad approach described above. In order to achieve 24-bit alignment, the pre-pad size for two-byte numbers is 1 byte. As described above, this means the minimally sized text code is 1 Base64 character. Suppose that the text code is `M` (Base64).  The following table provides examples of encoding the different two-byte raw binary values in the three Domains: Raw, Text, and Binary. Recall that the Raw domain is expressed by a tuple of (code, raw) where the code is Base64 text and the raw is the raw binary value without code. For readability, raw binary values are represented in hexadecimal notation.

|  Raw |  Text  |  Binary |
|------------:|------------:|------------:|
| ("M", 0x0000) | "MAAA" | 0x300000 |
| ("M", 0x0001) | "MAAB" | 0x300001 |
| ("M", 0xffff) | "MP__" | 0x30ffff |

With this approach, both the Binary domain and Text domain representations are as compact as possible for a fully qualified Primitive that satisfies the Composability property. The Text domain representation has a Stable readable code and a Stable readable value. The Binary domain is value right aligned. The Text domain representation consists of 4 text printable characters from the Base64 set of characters, and the Binary domain representation consists of 3 bytes. Both are able to be parsed in each Domain along character/byte boundaries respectively. A parser reads the first character/byte and then processes that value to get an index into a lookup table that it uses to find how many remaining characters/bytes to extract from the Stream. This makes the Primitive self-framing.

### Count or Group Framing codes

As mentioned above, one of the primary advantages of composable encoding is that special framing codes can be specified to support groups of Primitives. Grouping enables pipelining. Other suitable terms for these special Framing codes  are Group codes or Count codes for short. These are suitable terms because these Framing codes can be used to count characters or bytes in a group of Primitives when parsing and offloading a Stream of CESR Primitives. A group of Primitives may be recursively composed into a group of groups.

A Count code is its own composable Primitive, and its length, therefore, must  be an integer multiple of four characters in the Text domain or equivalently an integer multiple of three bytes in the Binary domain. To clarify, a Count code is a special Primitive that does not include a raw binary value, only its text code. Because a Count code's raw binary element value is empty and its length is an integer multiple of four characters (three bytes), its pad size is always 0.

To elaborate, Count codes can be used as separators to better organize a Stream of Primitives or to interleave non-native (non-CESR) serializations. Count codes enable the grouping together of any combination of Primitives, groups of Primitives, or non-native serializations to optimize pipelining and offloading.

###  Interleaved non-CESR serializations

As mentioned above, one extremely useful property of CESR is that special Count codes enable CESR to be interleaved with other serializations. Many applications use JSON [[ref: JSON]] [[spec: RFC4627]], CBOR [[ref: CBOR]] [[spec: RFC8949]], or MsgPack (MGPK) [[ref: MGPK]] to serialize flexible self-describing data structures based on field maps, also known as dictionaries or hash tables. With respect to field map serializations, CESR Primitives may appear in two different contexts. The first context is as a delimited text Primitive inside of a field map serialization. The delimited text may be either the key or value of a (key, value) pair. The second context is as a standalone serialization that is interleaved with field map serializations in a stream. Special CESR Count codes enable support for the second context of interleaving standalone CESR with other serializations.

### Cold start stream parsing problem

After a cold start, a Stream processor looks for framing information to know how to parse groups of elements in the Stream. If that framing information is ambiguous, then the parser may become confused and require yet another cold start. While processing a given Stream, a parser may become confused, especially if a portion of the Stream is malformed in some way. This usually requires flushing the Stream and forcing a cold start to resynchronize the parser to subsequent Stream elements. A re-synchronization mechanism that does not require flushing the in-transit buffers but merely skipping to the next well-defined Stream element boundary in order to execute a cold start is a better option. Good cold start re-synchronization is essential to robust performant Stream processing.

For example, in Transmission Control Protocol (TCP), a cold start usually means closing and then reopening the TCP connection. This flushes the TCP buffers and sends a signal to the other end of the Stream that may be interpreted as a restart or cold start. In User Datagram Protocol (UDP), each packet is framed individually but a Stream may be segmented into multiple packets.  So a cold start may require an explicit ack or nack to force a restart.

Special CESR Count codes support re-synchronization at each boundary between interleaved CESR and other serializations like JSON, CBOR, or MGPK.

#### Performant resynchronization with unique start bits

Given the popularity of three specific serializations, namely, JSON, CBOR, and MGPK, more fine-grained serialization boundary detection for interleaving CESR may be highly beneficial for both performance and robustness reasons. One way to provide this is by selecting the Count code start bits such that there is always a unique (mutually distinct) set of start bits at each interleaved boundary between CESR, JSON, CBOR, and MGPK.

Furthermore, it may also be highly beneficial to support in-stride switching between interleaved CESR text-domain Streams and CESR Binary domain Streams. In other words, the start bits for Count codes in both the ‘T’ Domain and the ‘B’ Domain should be unique. This would provide the analogous equivalent of a UTF Byte Order Mark (BOM) [[ref: BOM]]. Recall that a BOM enables a parser of UTF-encoded documents to determine if the UTF codes are big-endian or little-endian [[ref: BOM]]. In the CESR case, an analogous feature would enable a Stream parser to know if a Count code, along with its associated counted group of Primitives, is expressed in the ‘T’ or ‘B’ Domain. Together these impose the constraint that the boundary start bits for interleaved text CESR, binary CESR, JSON, CBOR, and MGPK be mutually distinct.

Amongst the codes for map objects in the JSON, CBOR, and MGPK, only the first three bits are fixed and not dependent on mapping size. In JSON, a serialized mapping object always starts with `{`. This is encoded as `0x7b`. the first three bits are `0b011`. In CBOR, the first three bits of the major type of its serialized mapping object are `0b101`. In MGPK (MsgPack), there are three different mapping object codes. The FixMap code starts with `0b100`. Both the Map16 code and Map32 code start with `0b110`.

There is a set of four used starting tritets (3 bits) in numeric order of `0b011`, `0b100`, `0b101`, and `0b110`. This leaves four unused tritets, namely, `0b000`, `0b001`, `0b010`, and `0b111`, that are potential candidates for the CESR Count code start bits. In Base64, there are two codes that satisfy the constraints. The first is the dash character, `-`, encoded as `0x2d`. Its first three bits are `0b001`. The second is the underscore character,`_`, encoded as `0x5f`. Its first three bits are `0b010`. Both of these are distinct from the starting tritets of any of the JSON, CBOR, and MGPK encodings above. Moreover, the starting tritet of the corresponding binary encodings of `-` and `_` is `0b111`, which is also distinct from all the others. To elaborate, Base64 uses `-` in position 62 or `0x3E` (hex) and uses `_` in position 63 or `0x3F` (hex), both of which have starting tritet of `0b111`

Two different Base64 characters, `-` and `_`,  can be used for the first character of any Count code in the ‘T’ Domain. This also means there can be two different classes of Count codes. Using Count codes in this way also provides a BOM-like capability that enables a parser to determine if the Count code itself is expressed in either the ‘T’ or ‘B’ Domain. To clarify, if a Stream group starts with the tritet `0b111`, then the Stream frame is ‘B’ Domain CESR, and a Stream parser would thereby know how to convert the first sextet of the Stream group to determine which of the two Count codes is being used, `0x3E` or `0x3F`. If, on the other hand, the Count  code starts with either of the tritets `0b001` or `0b010`, then the Count code is expressed in the ‘T’ domain, and a Stream parser likewise would thereby know how to convert the first character (octet) of the Count code to determine which Count code is being used for that group. Otherwise, if a Stream starts with `0b011`, then it is  JSON, with either `0b100` or `0b110`, then it is MGPK, and with `0b101`, then it is CBOR.

This is summarized in the following table:

##### Table 1

|   Starting Tritet   |  Serialization  | Character |
|:------------:|:------------:|:------------:|
|0b000|Unused||
|0b001|CESR ‘T’ Domain Count Code|`-`|
|0b010|CESR ‘T’ Domain Op Code|`_`|
|0b011|JSON|`{`|
|0b100|MGPK||
|0b101|CBOR||
|0b110|MGPK||
|0b111|CESR ‘B’ Domain Count Code or Op Code||

#### Stream parsing rules

Given this set of tritets (3 bits), a requirement for a well-formed Stream start and restart can be expressed.

Each stream must start (restart) with one of seven cases:

1. A Count code in CESR ‘T’ Domain
2. A Count code in CESR ‘B’ Domain.
3. An Op code in the CESR ‘T’’ Domain
4. An Op code in the CESR ‘B’’ Domain
5. A JSON encoded mapping.
6. A CBOR encoded mapping.
7. A MGPK encoded mapping.

A parser merely needs to examine the first tritet (3 bits) of the first byte of the Stream start to determine which one of the seven it is. When the first tritet is a Count code, then the remainder of the Count code itself will include the additional information needed to parse the attached group. When the first tritet indicates its JSON, CBOR, or MGPK, then the mapping's first field must be a Version string that provides the additional information needed to fully parse the associated encoded field map serialization.

The Stream must resume with a frame starting byte that begins with one of the 7 tritets, either another Count code expressed in the ‘T’ or ‘B’ Domain or a new JSON, CBOR, or MGPK encoded mapping.

This provides an extremely compact and elegant Stream parsing formula that generalizes not only support for CESR Composability but also support for interleaved CESR with three of the most popular field map serializations.

### Compact fixed size codes

As mentioned above, CESR uses a multiple-code table design that enables both size-optimized text codes for the most popular Primitive types and extensible universal support for all other Primitive types. Modern cryptographic suites support limited sets of raw binary Primitives with fixed (not variable) sizes. The design aesthetic is based on the understanding that there is minimally sufficient cryptographic strength and more cryptographic strength just wastes computation and bandwidth. Cryptographic strength is measured in bits of entropy which also corresponds to the number of trials that must be attempted to succeed in a brute-force attack. The accepted minimum for cryptographic strength is 128 bits of entropy or equivalently `2**128` (2 raised to the 128th power) brute force trials. The size in bytes of a given raw binary Primitive for a given modern cryptographic suite is usually directly related to this minimum strength of 128 bits (16 bytes).

For example, the raw binary Primitives from the well-known [[ref: NaCL]] ECC (Elliptic Curve Cryptography) library all satisfy this 128-bit strength goal. In particular, the digital signing public key raw binary Primitives for EdDSA are 256 bits (32 bytes) in length because well-known algorithms can reduce the number of trials to brute force invert an ECC public key to get the private key by the square root of the number of scalar multiplications which is also related to the size of both the private key and public key coordinates (discrete logarithm problem [[ref: DLog]]). Therefore, 256-bit (32-byte) ECC keys are needed to achieve 128 bits of cryptographic strength. In general, the size of a given raw binary Primitive is typically some multiple of 128 bits of cryptographic strength. This is also true for the associated EdDSA raw binary signatures which are 512 bits (64 bytes) in length.

Similar scale factors exist for cryptographic digests. A standard default Blake3 digest is 256 bits (32 bytes) in length in order to get 128 bits of cryptographic strength. This is also true of SHA3-256. The sweet spots for modern cryptographic raw Primitive lengths are 32 bytes for many digests as well as EdDSA public and private keys as well as ECDSA private keys. Likewise, 64 bytes is the sweet spot for EdDSA and ECDSA-secp256k1 signatures and 64-byte variants of the most popular digests. Therefore, optimized text code tables for these two sweet spots (32 and 64 bytes) would be highly advantageous.

A 32-byte raw binary value has a pad size of 1 character.

```text
((3 - (32 mod 3)) mod 3) = 1
```

Therefore, the minimal text code size is 1 character for 32-byte raw binary cryptographic material and all other raw binary material values whose pad size is 1 character.

A 64-byte raw binary value has a pad size of 2 characters.

```text
((3 - (64 mod 3)) mod 3) = 2
```

Therefore, the minimal text code size is 2 characters for 64-byte raw binary cryptographic material and all other raw binary material values whose pad size is 1 character. For example, a 16-byte raw binary value also has a pad size of 2 characters.

For all other cryptographic material values whose pad size is 0, such as the 33-byte ECDSA public keys then, the minimum size text code is 4 characters. So the minimally sized text code tables are 1, 2, and 4 characters, respectively.

Given that a given cryptographic Primitive type has a known fixed raw binary size, then that Primitive type and size can be encoded efficiently with just the type information. The size is given by the type.

For example, an Ed25519 (EdDSA) raw public key is always 32 bytes so knowing that the type is `Ed25519 public key` implies the size of 32 bytes and a pad size of 1 character that, therefore, may be encoded with a 1-character text code. Likewise, an Ed25519 (EdDSA) signature is always 64 bytes, so knowing that the type is `Ed25519 signature` implies the size of 64 bytes and a pad size of 2 characters that, therefore, may be encoded with a 2-character text code.

### Code table selectors

In order to parse a Stream of Primitives with types from multiple text code tables efficiently, the first character in the text code must determine which code table to use, either a default code table or a code table selector character when not the default code table. Thus the 1-character text code table must do double duty. It must provide selectors for the different text code tables and also provide type codes for the most popular Primitives that have a pad size of 1 that appears as the default code table. There are 64 Base64 characters (64 values). Only 12 tables are needed to support all the codes and code formats needed for the foreseeable future. Therefore, only 12 of those characters need to be dedicated as code table selectors, which leaves 52 characters that may be used for the 1-character type codes in the default table. This gives a total of 13 type code tables consisting of the dual purpose 1 character type or selector code table and 12 other tables.

As described above, the selector characters for the Count code tables that best support interleaved JSON, CBOR, and MGPK are `-` and `_`. The numerals `0` through `9` are used each to serve as a selector for the other tables. That leaves the letters `A` to `Z` and `a` to `z` as single character selectors. This provides 52 unique type codes for fixed-length Primitive types with raw binary values that have a pad size of 1.

To clarify, the first character of any Primitive is either a selector or a 1-character code type. The characters `0` through `9`, `-`, and `_` are selectors that select a given code table and indicate the number of remaining characters in the text code.

## Table types

There are two major types of tables – for codes of fixed length raw sizes and the other for codes with variable length raw sizes.

### Tables for codes with fixed length raw sizes  

#### Small fixed raw size tables

There are two special tables that are dedicated to the most popular fixed-size raw binary Cryptographic primitive types. These are the most compact, so they optimize bandwidth but only provide a small number of total types. In both of these, the text code size equals the number of pad characters, i.e., the pad size.

##### One-character fixed raw size table

The one-character type code table does not have a selector character per se but uses as type codes the non-selector characters `A` - `Z` and `a` - `z`. This provides 52 unique type codes for fixed-size raw binary values with a pad size of 1.

##### Two-character fixed raw size table

The two-character type code table uses the selector `0` as its first character. The second character is the type code. This provides 64 unique type codes for fixed-size raw binary values that have a pad size of 2.

#### Large fixed raw size tables

The three tables in this group are for large fixed raw-size Primitives. These three tables use 0, 1, or 2 lead bytes as appropriate for a pad size of 0, 1, or 2 for a given fixed raw binary value. The text code size for all three tables is 4 characters. The selector character not only encodes the table but also implicitly encodes the number of lead bytes. The 3 remaining characters is each type code in each table, providing 262,144 unique types. This should provide enough type codes to accommodate all fixed raw-size Primitive types for the foreseeable future.

##### Large Fixed Raw Size Table With 0 Lead Bytes

This table uses `1` as its first character or selector. The remaining 3 characters provide the type of each code. Only fixed-size raw binaries with a pad size of 0 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values (`262144 = 64**3`) for fixed-size raw binary Primitives with a pad size of 0.

##### Large fixed raw size table with 1 lead byte

This table uses `2` as its first character or selector. The remaining 3 characters provide the type of each code. Only fixed-size raw binaries with a pad size of 1 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values (`262144 = 64**3`). Together with the 52 values from the 1-character code table above, there are 262,196 type codes for fixed-size raw binary Primitives with a pad size of 1.

##### Large fixed raw size table with 2 lead bytes

This table uses `3` as its first character or selector. The remaining 3 characters provide the type of each code. Only fixed-size raw binaries with a pad size of 2 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values (`262144 = 64**3`). Together with the 64 values from the 2-character code table above (selector `0`), there are 262,208 type codes for fixed-size raw binary Primitives with a pad size of 2.

### Tables for codes with variable length raw sizes  

#### Small variable raw size tables

Although many Primitives have fixed raw binary sizes, especially those for modern cryptographic suites such as keys, signatures, and digests, there are other Primitives that benefit from variable sizings such as either encrypted material or legacy cryptographic material types found in the GNU Privacy Guard (GPG) or Open Secure Sockets Layer (OpenSSL) libraries (like the Rivest-Shamir-Adleman (RSA) algorithm. Furthermore, CESR is meant to support not merely cryptographic material types but other basic types, such as generic text strings and numbers. These basic non-cryptographic types may also benefit from variable-size codes.

The three tables in this group are for small variable raw-size Primitives. These three tables use 0, 1, or 2 lead bytes as appropriate given the pad size of 0, 1, or 2 for a given variable size raw binary value. The text code size for all three tables is 4 characters.  The first character is the selector, the second character is the type, and the last two characters provide the size of the value as a Base64 encoded integer. The number of unique type codes in each table is, therefore, 64. A given type code is repeated in each table for the same type so that all that differs between codes of the same type in each table is the number of lead bytes needed to align a given variable length on a 24-bit boundary. To clarify, what is different in each table is the number of lead bytes needed to achieve 24-bit alignment for a given variable length. Thus, the selector not only encodes for which type table but also implicitly encodes the number of lead bytes. The variable size is measured in quadlets of 4 characters each in the ‘T’ Domain and equivalently in triplets of 3 bytes each in the ‘B’ Domain. Thus, computing the number of characters when parsing or off-loading in the ‘T’ Domain means multiplying the variable size by 4. Computing the number of bytes when parsing or off-loading in the ‘B’ domain means multiplying the variable size by 3. The two Base64 size characters provide value lengths in quadlets/triplets from 0 to 4095 (`64**2 -1`). This corresponds to value lengths of up to 16,380 characters (`4095 * 4`) or 12,285 bytes (`4095 * 3`).

##### Small variable raw size table with 0 lead bytes

This table uses `4` as its first character or selector. The second character provides the type. The final two characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 0 are encoded with this table. The 1-character type code provides a total of 64 unique type code values. The maximum length of the value provided by the 2 size characters is 4095 quadlets of characters in the ‘T’ Domain and triplets of bytes in the ‘B’ Domain. All are raw binary Primitives with a pad size of 0 that each includes 0 lead bytes.

##### Small variable raw size table with 1 lead byte

This table uses `5` as its first character or selector. The second character provides the type. The final two characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 1 are encoded with this table. The 1-character type code provides a total of 64 unique type code values. The maximum length of the value provided by the 2 size characters is 4095 quadlets of characters in the ‘T’ Domain and triplets of bytes in the ‘B’ Domain. All are raw binary Primitives with a pad size of 1 that each includes 1 lead byte.

##### Small variable raw size table with 2 lead bytes

This table uses `6` as its first character or selector. The second character provides the type. The final two characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 0 are encoded with this table. The 1-character type code provides a total of 64 unique type code values. The maximum length of the value provided by the 2 size characters is 4095 quadlets of characters in the ‘T’ Domain and triplets of bytes in the ‘B’ Domain. All are raw binary Primitives with a pad size of 2 that each includes 2 lead bytes.

#### Large variable raw size tables 

Many legacy cryptographic libraries such as OpenSSL and GPG support any variable-sized Primitive for keys, signatures, and digests such as RSA. Although this approach is often criticized for providing too much flexibility, many legacy applications depend on this degree of flexibility. Consequently, these large variable raw size tables provide a sufficiently expansive set of tables with enough types and sizes to accommodate all the legacy cryptographic libraries as well as all the variable-sized non-cryptographic raw Primitive types for the foreseeable future .

::: issue
https://github.com/trustoverip/tswg-cesr-specification/issues/14
:::

The three tables in this group are for large variable raw size Primitives. These three large variable raw size tables use 0, 1, or 2 lead bytes as appropriate for the associated pad size of 0, 1, or 2 for a given variable-sized raw binary value. The text code size for all three tables is 8 characters. As a special case, the first 62 entries in these tables represent that same crypto suite type as the 62 entries in the small variable raw size tables above. This allows one type to use a smaller 4-character text code when the raw size is small enough.

The first character is the selector, the next three characters provide the type, and the last four characters provide the size of the value as a Base64 encoded integer. With 3 characters for each unique type code, each table provides 262,144 unique type codes. This should be enough type codes to accommodate all fixed raw size Primitive types for the foreseeable future.  A given type code is repeated in each table for the same type. What is different for each table is the number of lead bytes needed to align a given length on a 24-bit boundary. The selector not only encodes the table but also implicitly encodes the number of lead bytes. The variable size is measured in quadlets of 4 characters each in the ‘T’ Domain and equivalently in triplets of 3 bytes each in the B’ Domain. Thus, computing the number of characters when parsing or off-loading in the ‘T’ Domain means multiplying the variable size by 4. Likewise computing the number of bytes when parsing or off-loading in the ‘B’ Domain means multiplying the variable size by 3. The four Base64 size characters provide value lengths in quadlets/triplets from 0 to 16,777,215 (`64**4 -1`). This corresponds to value lengths of up to 67,108,860 characters (`16777215 * 4`) or 50,331,645 bytes (`16777215 * 3`).

##### Large variable raw size table With 0 lead bytes

This table uses `7` as its first character or selector. The next three characters provide the type. The final four characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 0 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values. The maximum length of the value provided by the 4 size characters is 16,777,215 quadlets of characters in the ‘T’ Domain and triplets of bytes in the ‘B’ domain. All are raw binary Primitives with pad size of 0 that each includes 0 lead bytes.

##### Large variable raw size table with 1 lead byte

This table uses `8` as its first character or selector. The next three characters provide the type. The final four characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 1 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values. The maximum length of the value provided by the 4 size characters is 16,777,215 quadlets of characters in the ‘T’ domain and triplets of bytes in the ‘B’ domain. All are raw binary Primitives with a pad size of 1 that each includes 1 lead byte.

##### Large variable raw size table with 2 lead bytes

This table uses `9` as its first character or selector. The next three characters provide the type. The final four characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 2 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values. The maximum length of the value provided by the 4 size characters is 16,777,215 quadlets of characters in the ‘T’ domain and triplets of bytes in the ‘B’ domain. All are raw binary Primitives with a pad size of 2 that each includes 2 lead bytes.

### Count code tables

There may be as many at 13 Count code tables, but only three are currently specified. These three are the small count, four-character table, the large count, eight-character table, and the eight-character protocol genus and version table. Because Count codes only count quadlets/triplets or the number of Primitives or groups of Primitives, Count codes have no value component but have only type and size components. Because Primitives already are guaranteed to be composable, Count codes do not need to account for pad size as long as the Count code itself is aligned on a 24-bit boundary. The Count code type indicates the type of Primitive or group being counted and the size indicates either how many of that type are in the group or the number of quadlets/triplets consumed by that group. Both Count code tables use the first two characters as a nested set of selectors. The first selector uses`-` as the initial selector for Count codes. The next character is either a selector for another Count code table or is the type for the small Count code table. When the second character is numeral `0` - `9` or the letters `-` or `_` , then it is a secondary Count code table selector. When the second character is a letter in the range `A` - `Z` or `a` - `z`, then it is a unique Count code type. This results in a total of 52 single-character Count code types.

::: issue
https://github.com/trustoverip/tswg-cesr-specification/issues/15
:::

#### Small count code table

Codes in the small Count code table are each four characters long. The first character is the selector `-`. The second character is the Count code type. the last two characters are the count size as a Base64 encoded integer. The Count code type must be a letter `A` - `Z` or `a` - `z`. If the second character is not a letter but is a numeral `0` - `9` or `-` or `_`, then it is a selector for a different Count code table. The set of letters provides 52 unique count codes. A two-character size provides counts from 0 to 4095 (`64**2 - 1`).

#### Large count code table

Codes in the large Count code table are each 8 characters long. The first two characters are the selectors `-`0. The next two characters are the Count code type. the last four characters are the count size as a Base64 encoded integer. With two characters for type, there are 4096 unique large-count code types. A four-character size provides counts from 0 to 16,777,215 (`64**4 - 1`).

### Protocol genus and version table

The protocol genus/version table is special because its codes modify the following Count code group. A protocol genus and version code itself does not provide a count of the following quadlets or triplets but modifies the protocol genus and Version of all the following Count codes until another protocol and genus Count code is provided. Consequently, a protocol genus and version code must only appear at the top level of any count group. In other words, a protocol genus and version code must not be nested inside any other Count code.

The purpose of this table is twofold. First, it allows CESR to be used for different protocols and protocol stacks, where each protocol may have its own dedicated set of code tables. The only table that all protocols must share is the protocol genus and version table (protocol table for short) in the set Count code tables. All other entries in all other tables may vary by protocol. Secondly, for a given protocol genus, a protocol genus and version code provides the Version of that given protocol's table set. This allows versioning of the CESR code tables for a given protocol.

The format for a protocol genus and version code is as follows: `--GGGVVV` where `GGG` represents the protocol genus and `VVV` is the version of that protocol genus. The genus uses three Base64 characters for a possible total of 262,144 different protocol genera. The Version also uses three Base64 characters which can be used in a protocol genus-specific manner. One suggested approach is to use one character for each of the three components of a semantic version of the form `major.minor.patch` [[ref: SEMVER]]. This provides 64 major Versions, where each major version has up to 64 minor Versions, and where each minor Version in turn, has up to 64 patches.

### OpCode tables

The `_` selector is reserved for the yet-to-be-defined opcode table or tables. Opcodes are meant to provide Stream processing instructions that are more general and flexible than simply concatenated Primitives or groups of Primitives. A yet-to-be-determined stack-based virtual machine could be executed using a set of opcodes that provides Primitive, groups of Primitives , or stream processing instructions. This would enable highly customizable uses for CESR.

### Selector codes and encoding scheme design

#### Encoding scheme table

The following table summarizes the ‘T’ Domain coding schemes by selector code for the 13 code tables defined above:

##### Table 1

|  Selector |  Selector | Type Chars | Value Size Chars | Code Size | Lead Bytes | Pad Size | Format |
|:---------:|:---------:|:----:|:---:|:---:|:---:|:---:|--------------:|
|           |           |      |     |     |     |     |               |
|`[A-Z,a-z]`|           | `1*` |  0  |  1  |  0  |  1  |         `$&&&`|
|     `0`   |           |   1  |  0  |  2  |  0  |  2  |         `0$&&`|
|     `1`   |           |   3  |  0  |  4  |  0  |  0  |     `1$$$&&&&`|
|     `2`   |           |   3  |  0  |  4  |  1  |  1  |     `2$$$%&&&`|
|     `3 `  |           |   3  |  0  |  4  |  2  |  2  |     `3$$$%%&&`|
|     `4`   |           |   1  |  2  |  4  |  0  |  0  |     `4$##&&&&`|
|     `5`   |           |   1  |  2  |  4  |  1  |  1  |     `5$##%&&&`|
|     `6`   |           |   1  |  2  |  4  |  2  |  2  |     `6$##%%&&`|
|     `7`   |           |   3  |  4  |  8  |  0  |  0  | `7$$$####&&&&`|
|     `8`   |           |   3  |  4  |  8  |  1  |  1  | `8$$$####%&&&`|
|     `9`   |           |   3  |  4  |  8  |  2  |  2  | `9$$$####%%&&`|
|     `-`   |`[A-Z,a-z]`| `1*` |  0  |  4  |  0  |  0  |         `-$##`|
|     `-`   |     `0`   |   2  |  0  |  8  |  0  |  0  |     `-0$$####`|
|     `-`   |     `-`   |   2  |  0  |  8  |  0  |  0  |     `--$$$###`|
|     `_`   |           |  TBD | TBD | TBD | TBD | TBD |            `_`|


Encoding scheme symbols
The following table defines the meaning of the symbols used in the encoding scheme table:

##### Table 2

|  Symbol |  Description  |
|:---:|:----:|
|   |   |
| `*` | selector-code character also provides the type |
| `$` | type-code character from subset of Base64  `[A-Z,a-z,0-9,-,_]` |
| `%` | lead byte where pre-converted binary includes the number of lead bytes shown |
| `#` | Base64 digit as part of a base 64 integer. When part of Primitive determines the number of following quadlets or triplets. When part of a Count code determines the count of the following Primitives or groups of Primitives |
| `&` | Base64 value characters that represent the converted raw binary value.  The actual number of characters is determined by the prepended text code.  Shown is the minimum number of value characters. |
| `TBD` | to be determined, reserved for future  |

### Parsing via table design

Text domain parsing can be simplified by using a parse size table. A Text domain parser uses the first character selector code to look up the hard-size (stable) portion of the text code. The parser then extracts hard-size characters from the text Stream. These characters form an index for the parse size table, which includes a set of sizes for the remainder of the Primitive. Using these sizes for a given code allows a parser to extract and convert a given Primitive. In the Binary domain, the same text parse table may be used, but each size value represents a multiple of a sextet of bits instead of Base64 characters. Example entries from that table are provided below. Two of the rows may always be calculated given the other 4 rows, so the table need only have 4 entries in each row. Thus, all basic Primitives may be parsed with one parse size table.

#### Table 1

Selector code size table

|  selector |  hs  |
|:---------:|:----:|
|           |      |
|       `B` |   1  |
|       `0` |   2  |
|       `5` |   2  |
|           |      |

#### Table 2 

Parse size table

Below is a snippet from the Parse Size Table for some example codes:

| hard sized index |  hs  |  ss  |  vs  |  fs  |  ls  |  ps  |
|:---------:|:----:|:----:|:----:|:----:|:----:|:----:|
|           |      |      |      |      |      |      |
|       `B` |   1  |   0  |  43\* |  44  |  0   |  1   |
|      `0B` |   2  |   0  |  86\* |  88  |  0   |  2\*  |
|      `5A` |   2  |   2  |  \#   |   \#  |  1   |  1\*  |
|           |      |      |      |      |      |      |

#### Table 3

Parse table symbols

|  Symbol |  Description  |
|:---:|:----:|
|   |   |
| `*` | entry's size may be calculated from other sizes |
| `#` | entry's size may be calculated from extracted code characters given by other sizes |
|   |      |

#### Table 4

Parse part sizes

The following table includes both labels of parts shown in the columns in the Parse Size table as well as parts that may be derived from the Parse Table parts or from transformations,

|  Label |  Description  |
|:----:|:----:|
| ***hs*** | hard size in chars (fixed) part of code size   |
| ***ss*** | soft size in chars, (Variable) part of code size  |
| ***os*** | other size in chars, when soft part includes two Variable values   |
| *ms* | main size in chars, (derived) when soft part provides two Variable values where *ms = ss - os*  |
| *cs* | code size in chars (derived value), where where *cs = hs + ss*  |
| ***vs*** | value size in chars |
| ***fs*** | full size in chars where *fs = hs + ss + vs* |
| ***ls*** | lead size in bytes to pre-pad raw binary bytes |
| ***ps*** | pad size in chars Base64 encoded |
| *rs* | raw size in bytes (derived) of binary value where *rs is derived from `R(T)` |
| *bs* | binary size in bytes (derived) where where *bs = ls + rs* |

### Special context-specific code tables

The set of tables above provides the basic or master encoding schemes. These coding schemes constitute the basic or master set of code tables. This basic or master set, however, may be extended with context-specific code tables. The context in which a Primitive occurs may provide an additional implicit selector that is not part of the actual explicit text code. This allows context-specific coding schemes that would otherwise conflict with the basic or master encoding schemes and tables.

#### Indexed codes

Currently, there is only one context-specific coding scheme, that is, for indexed signatures. A common use case is thresholded multi-signature schemes. A threshold-satisficing subset of signatures belonging to an ordered set or list of public keys may be provided as part of a Stream of Primitives. One way to compactly associated each signature with its public key is to include in the text code for that signature the index into the ordered set of public keys.

A popular signature raw binary size is 64 bytes which has a pad size of 2. This gives two code characters for a compact text code. The first character is the selector and type code. The second character is the Base64 encoded integer index.  By using a similar dual selector type code character scheme as above, where the selectors are the numbers `0-9` and `-` and `_`. Then there are 52 type codes given by the letters `A- Z` and `a-z`. The index has 64 values which support up to 64 members in the public key list. A selector can be used to select a large text code with more characters dedicated to larger indices. Some applications of CESR, like KERI, have the need for dual-indexed signatures (i.e., each signature has two indices) in order to support pre-rotation with partial or reserved participants in a rotation. With partial rotation, a given signature may contribute to the signing threshold for two different thresholds, each on two different lists of keys where the associated key may appear at a different location in each list. For 64-byte signatures, the Ed25519 and ECDSA secp256k1 schemes have entries in the table. For dual-indexed codes, the next larger code size that aligns a 64-byte signature on a 24-bit boundary is 6 characters. The table provides entries for dual-indexed 64-byte signatures. The code includes one selector character, one type character, and two each of two-character indices.

A new signature scheme based on Ed448 with 114-byte signatures is also supported. These signatures have a pad size of zero so require a four-character text code. The first character is the selector `0`, the second character is the type with 64 values, and the last two characters each provide a one-character index as a Base64 encoded integer with 64 different values. A big Version code consumes eight characters with one character for the selector, one for the type, and three characters for each of the dual indices.

##### Indexed code table

The associated indexed schemes are provided in the following table:

|  Selector | Type Chars | Index Chars | Ondex Chars | Code Size | Lead Bytes | Pad Size |     Format    |
|:---------:|:----------:|:-----------:|:-----------:|:---------:|:----------:|:--------:|--------------:|
|           |            |             |             |           |            |          |               |
|`[A-Z,a-z]`|  `1*`      |     1       |      0      |      2    |      0     |      2   |         `$#&&`|
|     `0`   |   1        |     1       |      1      |      4    |      0     |      0   |     `0$##&&&&`|
|     `2`   |   1        |     2       |      2      |      6    |      0     |      2   |   `0$####&&&&`|
|     `3`   |   1        |     3       |      3      |      6    |      0     |      0   | `0$######&&&&`|

##### Encoding scheme format symbol table

The following table defines the meaning of the symbols used in the Indexed Code table:

|  Symbol |  Description  |
|:---:|:----:|
|   |   |
| `*` | selector-code character also provides the type |
| `$` | type-code character from subset of Base64  `[A-Z,a-z,0-9,-,_]` |
| `%` | lead byte where pre-converted binary includes the number of lead bytes shown |
| `#` | Base64 digit as part of a base 64 integer. When part of Primitive determines the number of following quadlets or triplets. When part of a Count code determines the count of following Primitives or groups of Primitives |
| `&` | Base64 value characters that represent the converted raw binary value.  The actual number of characters is determined by the prepended text code.  Shown is the minimum number of value characters. |
| `TBD` | to be determined, reserved for future  |


[//]: # (\backmatter)


[//]: # (# KERI Protocol Stack Code Tables {#sec:annexA .informative})

## Annex

### KERI Protocol Stack Code Tables

#### Code table entry policy 

The policy for placing entries into the tables in general is in order of first needed first-entered basis. In addition, the compact code tables prioritize entries that satisfy the requirement that the associated cryptographic operations maintain at least 128 bits of cryptographic strength. This precludes the entry of many weak cryptographic suites into the compact tables. CESR's compact code table includes only best-of-class cryptographic operations along with common non-cryptographic Primitive types. At the time of this writing, there is the expectation that the National Institute of Standards and Technology (NIST) soon will  approve standardized post-quantum resistant cryptographic operations. When that happens, codes for the most appropriate post-quantum operations will be added. For example, Falcon appears to be one of the leading candidates with open-source code already available.

#### Table 1

Master code table

This master table includes both the Primitive and Count code types. The types are separated by headers. The table has 5 columns. These are as follows:

1) The Base64 stable (hard) text code itself.
2) A description of what is encoded or appended to the code.
3) The length in characters of the code.
4) the length in characters of the count portion of the code
5) The length in characters of the fully qualified primitive, including code and its appended material or the number of elements in the group. This is empty when variable length.



|   Code     | Description                       | Code Length | Count Length | Total Length |
|:----------:|:----------------------------------|:-----------:|:------------:|:------------:|
|            |    Basic One Character Codes  |             |              |              |
|     `A`    | Seed of Ed25519 private key       |      1      |              |      44      |
|     `B`    | Ed25519 non-transferable prefix public verification key |      1      |              |      44      |
|     `C`    | X25519 public encryption key.     |      1      |              |      44      |
|     `D`    | Ed25519 public verification key   |      1      |              |      44      |
|     `E`    | Blake3-256 Digest                 |             |      44      |
|     `F`    | Blake2b-256 Digest                |      1      |              |      44      |
|     `G`    | Blake2s-256 Digest                |      1      |              |      44      |
|     `H`    | SHA3-256 Digest                   |      1      |              |      44      |
|     `I`    | SHA2-256 Digest                   |      1      |              |      44      |
|     `J`    | Seed of ECDSA secp256k1 private key  |      1      |              |      44      |
|     `K`    | Seed of Ed448 private key         |      1      |              |      76      |
|     `L`    | X448 public encryption key        |      1      |              |      76      |
|     `M`    | Short number 2 byte b2            |      1      |              |       4      |
|     `N`    | Big number 4 byte b2              |      1      |              |       12     |
|     `O`    | X25519 private decryption key     |      1      |              |       44     |
|     `P`    | X25519 124 char b64 Cipher of 44 char qb64 Seed     |      1      |              |      124     |
|            |  Basic Two Character Codes    |             |              |              |
|    `0A`    | Random salt, seed, private key, or sequence number of length 128 bits |      2      |              |      24      |
|    `0B`    | Ed25519 signature                 |      2      |              |      88      |
|    `0C`    | ECDSA secp256k1 signature         |      2      |              |      88      |
|    `0D`    | Blake3-512 Digest                 |      2      |              |      88      |
|    `0E`    | Blake2b-512 Digest                |      2      |              |      88      |
|    `0F`    | SHA3-512 Digest                   |      2      |              |      88      |
|    `0G`    | SHA2-512 Digest                   |      2      |              |      88      |
|    `0H`    | Long value of length 32 bits      |      2      |              |       8      |
|            |  Basic Four Character Codes   |             |              |              |
|   `1AAA`   | ECDSA secp256k1 non-transferable prefix public verification key   |      4      |              |      48      |
|   `1AAB`   | ECDSA secp256k1 public verification or encryption key |      4      |              |      48      |
|   `1AAC`   | Ed448 non-transferable prefix public verification key |      4      |              |      80      |
|   `1AAD`   | Ed448 public verification key     |      4      |              |      80      |
|   `1AAE`   | Ed448 signature                   |      4      |              |      156     |
|   `1AAF`   | Tag Base64 4 chars or 3 byte number  |      4      |              |      8       |
|   `1AAG`   | DateTime Base64 custom encoded 32 char ISO-8601 DateTime |      4      |              |      36      |
|   `1AAH`   | X25519 100 char b64 Cipher of 24 char qb64 Salt |      4      |              |      100      |
|            |  Small Variable Raw Size Codes   |             |              |              |
|   `4A`     | String Base64 Only Lead Size 0      |      4      |      2        |            |
|   `5A`     | String Base64 Only Lead Size 1      |      4      |      2        |            |
|   `6A`     | String Base64 Only Lead Size 2      |      4      |      2        |            |
|   `4B`     | Bytes Lead Size 0                  |      4      |      2        |            |
|   `5B`     | Bytes Lead Size 1                  |      4      |      2        |            |
|   `6B`     | Bytes Lead Size 2                  |      4      |      2        |            |
|            |  Large Variable Raw Size Codes   |             |              |              |
|   `7AAA`   | String Big Base64 Only Lead Size 0 |      8      |      4        |            |
|   `8AAA`   | String Big Base64 Only Lead Size 1 |      8      |      4        |            |
|   `7AAA`   | String Big Base64 Only Lead Size 2 |      8      |      4        |            |
|   `7AAB`   | Bytes Big Lead Size 0              |      8      |      4        |            |
|   `8AAB`   | Bytes Big Lead Size 1              |      8      |      4        |            |
|   `9AAB`   | Bytes Big Lead Size 2              |      8      |      4        |            |
|            |  Counter Four Character Codes |             |              |              |
|   `-A##`   | Count of attached qualified Base64 indexed controller signatures |      4      |       2      |       4      |
|   `-B##`   | Count of attached qualified Base64 indexed witness signatures    |      4      |       2      |       4      |
|   `-C##`   | Count of attached qualified Base64 nontransferable identifier receipt couples  pre+sig |      4      |       2      |       4      |
|   `-D##`   | Count of attached qualified Base64 transferable identifier receipt quadruples  pre+snu+dig+sig   |      4      |       2      |       4      |
|   `-E##`   | Count of attached qualified Base64 first seen replay couples fn+dt |      4      |       2      |       4      |
|   `-F##`   | Count of attached qualified Base64 transferable indexed sig groups pre+snu+dig + idx sig group|      4      |       2      |       4      |
|   `-V##`   | Count of total attached grouped material qualified Base64 4 char quadlets |      4      |       2      |       4      |
|            |  Counter Eight Character Codes |             |              |              |
| `-0V#####` | Count of total attached grouped material qualified Base64 4 char quadlets |      8      |       5      |       8      |
|            |    Protocol Genus Version Codes    |             |              |             |
|`--AAA###`  | KERI ACDC protocol stack  version      |      8      |       5      |       8     |

#### Table 2

Indexed code table

|   Code    | Description                            | Code Length | Index Length | Ondex Length | Total Length |
|:-------- :|:---------------------------------------|:-----------:|:------------:|:------------:|:------------:|
|           |    Indexed Two Character Codes     |             |              |              |              |
|    `A#`   | Ed25519 indexed signature both same    |      2      |       1      |      0       |      88      |
|    `B#`   | Ed25519 indexed signature current only |      2      |       1      |      0       |      88      |
|    `C#`   | ECDSA secp256k1 indexed sig both same  |      2      |       1      |      0       |      88      |
|    `D#`   | ECDSA secp256k1 indexed sig curr only  |      2      |       1      |      0       |      88      |
|           |    Indexed Four Character Codes    |             |              |              |              |
|   `0A##`  | Ed448 indexed signature  dual          |      4      |       1      |       1      |      156     |
|   `0B##`  | Ed448 indexed signature current only   |      4      |       1      |       1      |      156     |
|           |    Indexed Six Character Codes     |             |              |              |              |
| `2A####`  | Ed25519 indexed sig big dual           |      6      |       2      |       2      |      92      |
| `2B####`  | Ed25519 indexed sig big current only   |      6      |       2      |       2      |      92      |
| `2C####`  | ECDSA secp256k1 indexed sig big dual   |      6      |       2      |       2      |      92      |
| `2D####`  | ECDSA secp256k1 idx sig big curr only  |      6      |       2      |       2      |      92      |
|           |    Indexed Eight Character Codes   |             |              |              |              |
|`3A######` | Ed448 indexed signature  big dual      |      8      |       3      |       3      |      160     |
|`3B######` | Ed448 indexed signature  big curr only |      8      |       3      |       3      |      160     |

#### Count code tables

#### Examples
The tables above include complex groups that maybe composed of other groups. For example, consider the counter attachment group with code `-F##` where `##` is replaced by the two-character Base64 count of the number of complex groups. This is known as the TransIndexedSigGroups counter.  Within the complex group are one or more attached
groups where each group consists of a triple pre+snu+dig followed by a ControllerIdxSigs group that in turn, consists of a counter code `-A##` followed by one or more
indexed signature Primitives.

The following example details how a complex nested group may appear.

The example has only one group that includes nested groups. The example is annotated with comments, spaces, and line feeds for clarity.

```text
-FAB     # Trans Indexed Sig Groups counter code 1 following group
E_T2_p83_gRSuAYvGhqV3S0JzYEF2dIa-OCPLbIhBO7Y    # trans prefix of signer for sigs
-EAB0AAAAAAAAAAAAAAAAAAAAAAB    # sequence number of est event of signer's public keys for sigs
EwmQtlcszNoEIDfqD-Zih3N6o5B3humRKvBBln2juTEM      # digest of est event of signer's public keys for sigs
-AAD     # Controller Indexed Sigs counter code 3 following sigs
AA5267UlFg1jHee4Dauht77SzGl8WUC_0oimYG5If3SdIOSzWM8Qs9SFajAilQcozXJVnbkY5stG_K4NbKdNB4AQ         # sig 0
ABBgeqntZW3Gu4HL0h3odYz6LaZ_SMfmITL-Btoq_7OZFe3L16jmOe49Ur108wH7mnBaq2E_0U0N0c5vgrJtDpAQ    # sig 1
ACTD7NDX93ZGTkZBBuSeSGsAQ7u0hngpNTZTK_Um7rUZGnLRNJvo5oOnnC1J2iBQHuxoq8PyjdT3BHS2LiPrs2Cg  # sig 2
```

::: issue
https://github.com/trustoverip/tswg-cesr-specification/issues/16
:::

### Self-addressing identifier (SAID)

A `SAID` (Self-Addressing IDentifier) is a special type of content-addressable identifier based on encoded cryptographic digest that is self-referential. The `SAID` derivation protocol defined herein enables verification that a given `SAID` is uniquely cryptographically bound to a serialization that includes the `SAID` as a field in that serialization. Embedding a `SAID` as a field in the associated serialization indicates a preferred content-addressable identifier for that serialization that facilitates greater interoperability, reduced ambiguity, and enhanced security when reasoning about the serialization. Moreover, given sufficient cryptographic strength, a cryptographic commitment such as a signature, digest, or another `SAID`, to a given `SAID` is essentially equivalent to a commitment to its associated serialization. Any change to the serialization invalidates its `SAID` thereby ensuring secure immutability evident reasoning with `SAIDs` about serializations or equivalently their `SAIDs`. Thus `SAIDs` better facilitate immutably referenced data serializations for applications such as Verifiable Credentials or Ricardian Contracts.

`SAIDs` are encoded with `CESR` (Composable Event Streaming Representation) [CESR] which includes a pre-pended derivation code that encodes the cryptographic suite or algorithm used to generate the digest. A `CESR` primitive's primary expression (alone or in combination ) is textual using Base64 URL-safe characters. `CESR` primitives may be round-tripped (alone or in combination) to a compact binary representation without loss. The `CESR` derivation code enables cryptographic digest algorithm agility in systems that use `SAIDs` as content addresses. Each serialization may use a different cryptographic digest algorithm as indicated by its derivation code. This provides interoperable future proofing. `CESR` was developed for the [KERI] protocol.

The primary advantage of a content-addressable identifier is that it is cryptographically bound to the content (expressed as a serialization), thus providing a secure root-of-trust for reasoning about that content. Any sufficiently strong cryptographic commitment to a content-addressable identifier is functionally equivalent to a cryptographic commitment to the content itself.

A `self-addressing identifier (SAID)` is a special class of content-addressable identifier that is also self-referential. This requires a special derivation protocol that generates the `SAID` and embeds it in the serialized content.  The reason for a special derivation protocol is that a naive cryptographic content-addressable identifier must not be self-referential, i.e. the identifier must not appear within the content that it is identifying. This is because the naive cryptographic derivation process of a content-addressable identifier is a cryptographic digest of the serialized content. Changing one bit of the serialization content will result in a different digest. Therefore, self-referential content-addressable identifiers require a special derivation protocol.

To elaborate, this approach of deriving self-referential identifiers from the contents they identify, is called `self-addressing`. It allows any validator to verify or re-derive the `self-referential, self-addressing identifier` given the contents it identifies. To clarify, a `SAID` is different from a standard content address or content-addressable identifier in that a standard content-addressable identifier may not be included inside the contents it addresses. Moreover, a standard content-addressable identifier is computed on the finished immutable contents, and therefore is not self-referential. In addition, a `self-addressing identifier (SAID)` includes a pre-pended derivation code that specifies the cryptographic algorithm used to generate the digest.

An authenticatable data serialization is defined to be a serialization that is digitally signed with a non-repudiable asymmetric key-pair based signing scheme. A verifier, given the public key, may verify the signature on the serialization and thereby securely attribute the serialization to the signer. Many use cases of authenticatable data serializations or statements include a self-referential identifier embedded in the authenticatable serialization. These serializations may also embed references to other self-referential identifiers to other serializations. The purpose of a self-referential identifier is to enable reasoning in software or otherwise about that serialization.  Typically, these self-referential identifiers are not cryptographically bound to their encompassing serializations such as would be the case for a content-addressable identifier of that serialization. This poses a security problem because there now may be more than one identifier for the same content. The first is self-referential, included in the serialization, but not cryptographically bound to its encompassing serialization and the second is cryptographically bound but not self-referential, not included in the serialization.

When reasoning about a given content serialization, the existence of a non-cryptographically bound but self-referential identifier is a security vulnerability. Certainly, this identifier cannot be used by itself to securely reason about the content because it is not bound to the content. Anyone can place such an identifier inside some other serialization and claim that the other serialization is the correct serialization for that self-referential identifier.  Unfortunately, a standard content-addressable identifier for a serialization which is bound to the serialization can not be included in the serialization itself, i.e. can be neither self-referential nor self-contained; it must be tracked independently. In contrast, a `self-addressing identifier` is included in the serialization to which it is cryptographically bound making it self-referential and self-contained. Reasoning about `self-addressing identifiers (SAIDs)` is secure because a `SAID` will verify if and only if its encompassing serialization has not been mutated, which makes the content immutable. `SAIDs` used as references to serializations in other serializations enable tamper-evident reasoning about the referenced serializations. This enables a more compact representation of an authenticatable data serialization that includes other serializations by reference to their `SAIDs` instead of by embedded containment.

#### Generation and Verification Protocols

The *self-addressing identifier* (`SAID`) verification protocol is as follows:

- Make a copy of the embedded `CESR` [CESR] encoded `SAID` string included in the serialization.
- replace the `SAID` field value in the serialization with a dummy string of the same length. The dummy character is `#`, that is, ASCII 35 decimal (23 hex).
- Compute the digest of the serialization that includes the dummy value for the `SAID` field. Use the digest algorithm specified by the `CESR` [CESR] derivation code of the copied `SAID`.
- Encode the computed digest with CESR [CESR] to create the final derived and encoded SAID of the same total length as the dummy string and the copied embedded `SAID`.
- Compare the copied `SAID` with the recomputed `SAID`. If they are identical then the verification is successful; otherwise unsuccessful.


##### Example Computation
The `CESR` [CESR] encoding of a Blake3-256 (32 byte) binary digest has 44 Base-64 URL-safe characters. The first character is `E` which represents Blake3-256. Therefore, a serialization of a fixed field data structure with a SAID generated by a Blake3-256 digest must reserve a field of length 44 characters. Suppose the initial value of the fixed field serialization is the following string:


```
field0______field1______________________________________field2______
```

In the string, `field1` is of length 44 characters. The first step to generating the `SAID` for this serialization is to replace the contents of `field1` with a dummy string of `#` characters of length 44 as follows:

```
field0______############################################field2______
```
The Blake3-256 digest is then computed on the above string and encoded in `CESR` format. This gives the following `SAID`:

```
E8wYuBjhslETYaLZcxMkWrhVbMcA8RS1pKYl7nJ77ntA
```

The dummy string is then replaced with the `SAID` above to produce the final serialization with embedded `SAID` as follows:

```
field0______E8wYuBjhslETYaLZcxMkWrhVbMcA8RS1pKYl7nJ77ntA______
```

To verify the embedded `SAID` with respect to its encompassing serialization above, just reverse the generation steps.

##### Serialization Generation

###### Order-Preserving Data Structures
The crucial consideration in `SAID` generation is reproducibility. This requires the ordering and sizing of fields in the serialization to be fixed. Data structures in most computer languages have fixed fields. The example above is such an example.

A very useful type of serialization especially in some languages like Python or JavaScript is of self-describing data structures that are mappings of (key, value) or (label, value) pairs. These are often also called dictionaries or hash tables. The essential feature needed for reproducible serialization of such mappings is that mapping preserve the ordering of its fields on any round trip to/from a serialization. In other words the mapping is ordered with respect to serialization. Another way to describe a predefined order preserving serialization is canonicalization or canonical ordering. This is often referred to as the mapping canonicalization problem.

The *natural* canonical ordering for such mappings is *insertion order* or sometimes called *field creation order*. Natural order allows the fields to appear in a preset order independent of the lexicographic ordering of the labels. This enables functional or logical ordering of the fields. Logical ordering also allows the absence or presence of a field to have meaning. Fields may have a priority given by their relative order of appearance. Fields may be grouped in logical sequence for better usability and labels may use words that best reflect their function independent of their relative lexicographic ordering. The most popular serialization format for mappings is *JSON*. Other popular serializations for mappings are CBOR and MsgPack.

In contrast, from a functional perspective, lexicographic ordering appears un-natural. In lexicographic ordering the fields are sorted by label prior to serialization.  The problem with lexicographic ordering is that the relative order of appearance of the fields is determined by the labels themselves not some logical or functional purpose of the fields themselves. This often results in oddly-labeled fields that are so named merely to ensure that the lexicographic ordering matches a given logical ordering.

Originally mappings in most if not all computer languages were not insertion order preserving. The reason is that most mappings used hash tables under the hood. Early hash tables were highly efficient but by nature did not include any mechanism for preserving field creation or field insertion order for serialization. Fortunately, this is no longer true in general. Most if not all computer languages that support dictionaries or mappings as first-class data structures now support variations that are insertion order preserving.

For example, since version 3.6 the default `dict` object in Python is insertion order preserving. Before that, Python 3.1 introduced the `OrderedDict` class which is insertion order preserving, and before that, custom classes existed in the wild for order preserving variants of a Python `dict`. Since version 1.9 the Ruby version of a `dict`, the `Hash` class, is insertion order preserving. Javascript is a relative latecomer but since ECMAScript `ES6` the insertion ordering of JavaScript objects was preserved in `Reflect.ownPropertyKeys()`. Using custom `replacer` and `reviver` functions in `.stringify` and `.parse` allows one to serialize and de-serialize JavaScript objects in insertion order. Moreover, since ES11 the native `.stringify` uses insertion order all text string labeled fields in Javascript objects. It is an uncommon use case to have non-text string labels in a mapping serialization. A list is usually a better structure in those cases. Nonetheless, since ES6 the new Javascript `Map` object preserves insertion order for all fields for all label types. Custom `replacer` and `reviver` functions for `.stringify` and `.parse` allows one to serialize and de-serialize Map objects to/from JSON in natural order preserving fashion. Consequently, there is no need for any canonical serialization but natural insertion order preserving because one can always use lexicographic ordering to create the insertion order.

##### Example Python dict to JSON Serialization with SAID

Suppose the initial value of a Python `dict` is as follows:

```python
{
    "said": "",
    "first": "Sue",
    "last": "Smith",
    "role": "Founder"
}
```

As before the `SAID` will be a 44 character CESR encoded Blake3-256 digest. The serialization will be *JSON*. The `said` field value in the `dict` is to be populated with the resulting `SAID`. First the value of the `said` field is replaced with a 44 character dummy string as follows:

```python
{
    "said": "############################################",
    "first": "Sue",
    "last": "Smith",
    "role": "Founder"
}
```

The `dict` is then serialized into JSON with no extra whitespace. The serialization is the following string:

```json
{"said":"############################################","first":"Sue","last":"Smith","role":"Founder"}
```

The Blake3-256 digest is then computed on that serialization above and encoded in `CESR` to provide the `SAID` as follows:

```
EnKa0ALimLL8eQdZGzglJG_SxvncxkmvwFDhIyLFchUk
```

The value of the `said` field is now replaced with the computed and encoded `SAID` to produce the final serialization with embedded `SAID` as follows:

```json
{"said":"EnKa0ALimLL8eQdZGzglJG_SxvncxkmvwFDhIyLFchUk","first":"Sue","last":"Smith","role":"Founder"}
```

The final serialization may be converted to a python `dict` by deserializing the JSON to produce:

```python
{
    "said": "EnKa0ALimLL8eQdZGzglJG_SxvncxkmvwFDhIyLFchUk",
    "first": "Sue",
    "last": "Smith",
    "role": "Founder"
}
```

The generation steps may be reversed to verify the embedded `SAID`. The `SAID` generation and verification protocol for mappings assumes that the fields in a mapping serialization such as JSON are ordered in stable, round-trippable, reproducible order, i.e., canonical. The natural canonical ordering is called `field insertion order`.

## Example Schema Immutability using JSON Schema with SAIDs

`SAIDs` make [JSON Schema](https://json-schema.org/draft/2020-12/json-schema-core.html) fully self-contained with self-referential, unambiguously cryptographically bound, and verifiable content-addressable identifiers. We apply the `SAID` derivation protocol defined above to generate the `$id` field.

First, replace the value of the `$id` field with a string filled with dummy characters of the same length as the eventual derived value for `$id`.

```json
    {
        "$id": "############################################",
        "$schema": "http://json-schema.org/draft-07/schema#",
        "type": "object",
        "properties": {
            "full_name": {
            "type": "string"
        }
    }
```


Second, make a digest of the serialized schema contents that include the dummy value for the `$id`.

```
EZT9Idj7zLA0Ek6o8oevixdX20607CljNg4zrf_NQINY
```

Third, replace the dummy identifier value with the derived identifier value in the schema contents.

```json
    {
        "$id": "EZT9Idj7zLA0Ek6o8oevixdX20607CljNg4zrf_NQINY",
        "$schema": "http://json-schema.org/draft-07/schema#",
        "type": "object",
        "properties": {
            "full_name": {
            "type": "string"
        }
    }
```

Usages of `SAIDs` within authentic data containers as demonstrated here are referred to as `self-addressing data (SAD)`.


##### Discussion
As long as any verifier recognizes the derivation code of a `SAID`, the `SAID` is a cryptographically secure commitment to the contents in which it is embedded; it is a cryptographically verifiable, self-referential, content-addressable identifier. Because a `SAID` is both self-referential and cryptographically bound to the contents it identifies, anyone can validate this binding if they follow the _derivation protocol_ outlined above.


To elaborate, this approach of deriving self-referential identifiers from the contents they identify, is called `self-addressing`. It allows any validator to verify or re-derive the self-referential, self-addressing identifier given the contents it identifies. To clarify, a `SAID` is different from a standard content address or content-addressable identifier in that a standard content-addressable identifier may not be included inside the contents it addresses. Moreover, a standard content-addressable identifier is computed on the finished immutable contents, and therefore is not self-referential.

[//]: # (\newpage)

[//]: # (\makebibliography)

## Bibliography

[[def: KERI-WP]]

~ https://arxiv.org/abs/1907.02143

[[def: ASCII, RFC20]]

~ https://www.rfc-editor.org/rfc/rfc20

[[def: JSON]]

~ [[ref: RFC4627]]

[[def: CBOR]]

~ [[ref: RFC8949]]

[[def: MGPK]]

~ https://github.com/msgpack/msgpack/blob/master/spec.md

[[def: BOM, UTF Byte Order Mark]]

~ https://en.wikipedia.org/wiki/Byte_order_mark

[[def: DLog, Discrete Logarithm Problem]]
  
~ https://en.wikipedia.org/wiki/Discrete_logarithm

[[def: NaCL]]

~ https://nacl.cr.yp.to

[[def: MultiCodec Multiformats Codecs, MultiCodec]]

~ https://github.com/multiformats/multicodec

[[def: MultiCodec Table, MCTable]]

~ https://github.com/multiformats/multicodec/blob/master/table.csv

[[def: IPFS MultiFormats, IPFS]]

~ https://richardschneider.github.io/net-ipfs-core/api/Ipfs.Registry.HashingAlgorithm.html


[[def: Base58Check Encoding, Base58Check]]

~ https://en.bitcoin.it/wiki/Base58Check_encoding


[[def: Wallet Import Format ECDSA Base58Check, WIF]]

~ https://en.bitcoin.it/wiki/Wallet_import_format


[[def: Binary to Text Encoding, Bin2Txt]]

~ https://en.wikipedia.org/wiki/Binary-to-text_encoding

[[def: UTF8, UTF-8 Unicode]]

~ https://en.wikipedia.org/wiki/UTF-8

[[def: Latin1]]

~ https://en.wikipedia.org/wiki/ISO/IEC_8859-1

[[def: Simple Text Oriented Messaging Protocol, STOMP]]

~ https://stomp.github.io

[[def: Reliable Asynchronous Event Transport, RAET]]

~ https://github.com/RaetProtocol/raet

[[def: Analysis of the Effect of Core Affinity on High-Throughput Flows, Affinity]]

~ https://crd.lbl.gov/assets/Uploads/Nathan-NDM14.pdf

[[def: Semantic Versioning, SEMVER]]

~ https://semver.org

[[spec]]
