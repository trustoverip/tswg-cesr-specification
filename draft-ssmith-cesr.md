---

title: "Composable Event Streaming Representation (CESR)"
abbrev: "CESR"
docname: draft-ssmith-cesr-latest
category: info

ipr: trust200902
area: TODO
workgroup: TODO Working Group
keyword: Internet-Draft

stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
  -
    name: S. Smith
    organization: ProSapien LLC
    email: sam@prosapien.com

normative:
  RFC4648:  Base64

  RFC20: ASCII

informative:
  KERI:
    target: https://arxiv.org/abs/1907.02143
    title: Key Event Receipt Infrastructure (KERI)
    author:
      ins: S. Smith
      name: Samuel M. Smith
      org: ProSapien LLC
    date: 2021

  JSON:
    target: https://www.json.org/json-en.html
    title: JavaScript Object Notation Delimeters

  RFC4627:
    target: https://datatracker.ietf.org/doc/rfc4627/
    title: The application/json Media Type for JavaScript Object Notation (JSON)

  CBOR:
    target: https://en.wikipedia.org/wiki/CBOR
    title: CBOR Mapping Object Codes

  RFC8949: CBOR

  MGPK:
    target: https://github.com/msgpack/msgpack/blob/master/spec.md
    title: Msgpack Mapping Object Codes

  BOM:
    target: https://en.wikipedia.org/wiki/Byte_order_mark
    title: UTF Byte Order Mark

  DLog:
    target: https://en.wikipedia.org/wiki/Discrete_logarithm
    title: Discrete Logarithm Problem

  NaCL:
    target: https://nacl.cr.yp.to
    title: NaCl Networking and Cryptography library

  MultiCodec:
    target: https://github.com/multiformats/multicodec
    title: MultiCodec Multiformats Codecs

  MCTable:
    target: https://github.com/multiformats/multicodec/blob/master/table.csv
    title: MultiCodec Table

  IPFS:
    target: https://richardschneider.github.io/net-ipfs-core/api/Ipfs.Registry.HashingAlgorithm.html
    title: IPFS MultiFormats

  Base58Check:
    target: https://en.bitcoin.it/wiki/Base58Check_encoding
    title: Base58Check Encoding

  WIF:
    target: https://en.bitcoin.it/wiki/Wallet_import_format
    title: Wallet Import Format ECDSA Base58Check

  Bin2Txt:
    target: https://en.wikipedia.org/wiki/Binary-to-text_encoding
    title: Binary to Text Encoding

  ASCII:
    target: https://en.wikipedia.org/wiki/ASCII
    title: Text Printable ASCII Characters

  UTF8:
    target: https://en.wikipedia.org/wiki/UTF-8
    title: "UTF-8 Unicode"

  Latin1:
    target: https://en.wikipedia.org/wiki/ISO/IEC_8859-1
    title: "Latin-1 ISO 8859-1"

  STOMP:
    target: https://stomp.github.io
    title: Simple Text Oriented Messaging Protocol

  RAET:
    target: https://github.com/RaetProtocol/raet
    title: Reliable Asynchronous Event Transport

  Affinity:
    target: https://crd.lbl.gov/assets/Uploads/Nathan-NDM14.pdf
    title: Analysis of the Effect of Core Affinity on High-Throughput Flows
    date: 2014-11-16

  SEMVER:
    target: https://semver.org
    title: Semantic Versioning


--- abstract
The Composable Event Streaming Representation (CESR) is a dual text-binary encoding format that has the unique property of text-binary concatenation composability. This composability property enables the round trip conversion en-masse of concatenated primitives between the text domain and binary domain while maintaining the separability of individual primitives. This enables convenient usability in the text domain and compact transmission in the binary domain. CESR primitives are self-framing. CESR supports self-framing group codes that enable stream processing and pipelining in both the text and binary domains. CESR supports composable text-binary encodings for general data types as well as suites of cryptographic material. Popular cryptographic material suites have compact encodings for efficiency while less compact encodings provide sufficient extensibility to support all foreseeable types. CESR streams also support interleaved JSON, CBOR, and MGPK serializations. CESR is a universal encoding that uniquely provides dual text and binary domain representations via composable conversion. The CESR protocol is used by other protocols such as KERI {{KERI}}.


--- middle

# Introduction

One way to better secure Internet communications is to use cryptographically verifiable primitives and data structures both inside messages and in support of messaging protocols. Cryptographically verifiable primitives provide essential building blocks for zero-trust computing and networking architectures. Traditionally cryptographic primitives including but not limited to digests, salts, seeds (private keys), public keys, and digital signatures have been largely represented in some type of binary encoding. This limits their usability in domains or protocols that are human-centric or equivalently that only support {{ASCII}} text-printable characters, {{RFC20}}. These domains include source code,  documents, system logs, audit logs, Ricardian contracts, and human-readable text documents of many types {{JSON}}{{RFC4627}}.

Generic binary-to-text, {{Bin2Txt}}, or simply textual encodings such as Base64 {{RFC4648}}, do not provide any information about the type or size of the underlying cryptographic primitive. Base64 only provides "value" information. More recently {{Base58Check}} was developed as a fit-for-purpose textual encoding of cryptographic primitives for shared distributed ledger applications that in addition to value may include information about the type and in some cases the size of the underlying cryptographic primitive, {{WIF}}. But each application may use a non-interoperable encoding of type and optionally size. Interestingly because a binary encoding may include as a subset some codes that are in the text-printable compatible subset of {{ASCII}} such as ISO Latin-1, {{Latin1}} or UTF-8, {{UTF8}}, one may *serendipitously* find, for a given cryptographic primitive, a text-printable type code from a binary code table such as the table {{MCTable}} from {{MultiCodec}} for {{IPFS}}. Indeed some {{Base58Check}} applications take advantage of the binary MultiCodec tables but only used *serendipitous* text-compatible type codes. *Serendipitous* text encodings that appear in binary code tables, do not, however, work in general for any size or type. So the *Serendipitous* approach is not universally applicable and is no substitute for a true textual encoding protocol for cryptographic primitives.

In general, there is no standard text-based encoding protocol that provides universal type, size, and value encoding for cryptographic primitives. Providing this capability is the primary motivation for the encoding protocol defined herein.

Importantly, a textual encoding that includes type, size, and value is self-framing. A self-framing text primitive may be parsed without needing any additional delimiting characters. Thus a stream of concatenated primitives may be individually parsed without the need to encapsulate the primitives inside textual delimiters or envelopes. Thus a textual self-framing encoding provides the core capability for a streaming text protocol like {{STOMP}} or {{RAET}}. Although a first-class textual encoding of cryptographic primitives is the primary motivation for the CESR protocol defined herein, CESR is sufficiently flexible and extensible to support other useful data types, such as integers of various sizes, floating-point numbers, date-times as well as generic text. Thus this protocol is generally useful to encode data structures of all types into text not merely those that contain cryptographic primitives.

Textual encodings have numerous usability advantages over binary encodings. The one advantage, however, that a binary encoding has over text is compactness. An encoding protocol that has the property we call *text-binary concatenation composability* or more succinctly ***composability*** enables both the usability of text and the compactness of binary. ***Composability*** may be the most uniquely innovative and useful feature of the encoding protocol defined herein.

## Composability

*Composability* as defined here is short for *text-binary concatenation composability*. An encoding has *composability* when any set of self-framing concatenated primitives expressed in either the text domain or binary domain may be converted as a group to the other domain and back again without loss. Essentially, *composability* provides round-trippable lossless conversion between text and binary representations of any set of concatenated primitives when converted as a set not merely individually. The property enables a stream processor to safely convert en-masse a stream of text primitives to binary for compact transmission that may be safely converted back to text en-masse by a stream processor at the other end for further processing or archival storage. The addition of group framing codes as independently composable primitives enables hierarchical compositions. Such a hierarchically composable encoding protocol enables pipelining (multiplexing and de-multiplexing) of complex streams in either text or compact binary. This allows management at scale for high-bandwidth applications that benefit from core affinity off-loading of streams {{Affinity}}.

## Abstract Domain Representations

The cryptographic primitives defined here (i.e. CESR) inhabit three different domains each with a different representation.  The first domain we call streamable text or *text* and is denoted as ***T***. The second domain we call streamable binary or *binary* and is denoted as ***B***. Composability is defined between the *T* and *B* domains. The third domain we call *raw* and is denoted as ***R***. The third domain is special because primitives in this domain are represented by a pair or two-tuple of values namely *(text code, raw binary)* or `(code, raw)` for short. The *text code* element of the *R* domain pair is a string of one or more text characters that provides the type and size information for the encoded primitive when in the *T* domain. The raw binary element is composed of bytes. The actual use of cryptographic primitives happens in the *R* domain using the *raw binary* element of the `(code, raw)` pair. Cryptographic primitive values are usually represented as strings of bytes that represent very large integers. Cryptographic libraries typically assume that the inputs and outputs of their functions will be such strings of bytes. The *raw binary* element of the *R* domain pair is such a string of bytes. The CESR protocol, however, is not limited to merely encoding cryptographic primitives but any primary data type (numbers, text, datetimes, lists, maps) may be encoded in a composable way.

A given primitive in the *T* domain is denoted with `t`.  A member of an indexed set of primitives in the *T* domain is denoted with `t[k]`. Likewise, a given primitive in the *B* domain is denoted with `b`. A member of an indexed set of primitives in the *B* domain is denoted with `b[k]`. Similarly, a given primitive in the *R* domain is denoted with `r`. A member of an indexed set of primitives in the *R* domain is denoted with  `r[k]`.

### Transformations Between Domains

Although the composability property mentioned in the previous section only applies to conversions back and forth between the *T*, and *B*, domains, conversions between the *R*, and *T* domains, as well as conversions between the *R* and *B* domains are also defined and supported by the protocol as described in detail in this section. As a result, there is a total of six transformations, one in each direction, between the three domains.

Let `T(B)` denote the abstract transformation function from the *B* domain to the *T* domain. This is the dual of `B(T)` below.

Let `B(T)` denote the abstract transformation function from the *T* domain to the *B* domain. This is the dual of `T(B)` above.

Let `T(R)` denote the abstract transformation function from the *R* domain to the *T* domain. This is the dual of `R(T)` below.

Let `R(T)` denote the abstract transformation function from the *T* domain to the *R* domain. This is the dual of `T(R)` above.

Let `B(R)` denote the abstract transformation function from the *R* domain to the *B* domain. This is the dual of `R(B)` below.

Let `R(B)` denote the abstract transformation function from the *B* domain to the *R* domain. This is the dual of `B(R)` above.

Given these transformations, we can complete a circuit of transformations that starts in any of the three domains and then crosses over the other two domains in either direction. For example, starting in the *R* domain we can traverse a circuit that crosses into the *T* and *B* domains and then crosses back into the *R* domain as follows:

~~~text
R->T(R)->T->B(T)->B->R(B)->R
~~~

Likewise, starting in the *R* domain we can traverse a circuit that crosses into the *B* and *T* domains and then crosses back into the *R* domain as follows:

~~~text
R->B(R)->B->T(B)->T->R(T)->R
~~~

### Concatenation Composability Property

Let `+` represent concatenation. Concatenation is associative and may be applied to any two primitives or any two groups or sets of concatenated primitives. For example:

~~~text
t[0] + t[1] + t[2] + t[3] = (t[0] + t[1]) + (t[2] + t[3])
~~~

If we let `cat(x[k])` denote the concatenation of all elements of a set of indexed primitives `x[k]` where each element is indexed by a unique value of `k`. Given the indexed representation, we can express the transformation between domains of a concatenated set of primitives as follows:

Let `T(cat(b[k]))` denote the concrete transformation of a given concatenated set of primitives, `cat(b[k])` from the *B* domain to the *T* domain.

Let `B(cat(t[k]))` denote the concrete transformation of a given concatenated set of primitives, `cat(t[k])` from the *T* domain to the *B* domain.

The *concatenation composability property* or *composability* for short, between *T* and *B* is expressed as follows:

Given a set of primitives `b[k]` and `t[k]` and transformations `T(B)` and `B(T)` such that `t[k] = T(b[k])` and `b[k] = B(t[k])` for all `k`, then `T(B)` and `B(T)` are jointly concatenation composable if and only if,

~~~text
T(cat(b[k]))=cat(T(b[k])) and B(cat(t[k]))=cat(B(t[k])) for all k.
~~~

Basically, *composability* (over concatenation) means that the transformation of a set (as a whole) of concatenated primitives is equal to the concatenation of the set of individually transformed primitives.

For example, suppose we have two primitives in the text domain, namely, `t[0]` and `t[1]` that each transforms, respectively, to primitives in the binary domain, namely, `b[0]` and `b[1]`. The transformation duals, `B(T)` and `T(B)`, are composable if and only if,

~~~text
B(t[0] + t[1]) = B(t[0]) + B(t[1]) = b[0] + b[1]
~~~

and

~~~text
T(b[0] + b[1]) = T(b[0]) + T(b[1]) = t[0] + t[1].
~~~

The *composability* property defined above allows us to create arbitrary compositions of primitives via concatenation in either the *T* or *B* domain and then convert the composition en masse to the other domain and then de-concatenate the result without loss. The self-framing property of the primitives enables de-concatenation.

The *composability* property is an essential building block for streaming in either domain. The use of framing primitives that count or group other primitives enables multiplexing and demultiplexing of arbitrary groups of primitives for pipelining and/or on or offloading of streams. The text domain representation of a stream enables better usability (readability), and the binary domain representation of a stream enables better compactness. In addition, pipelined hierarchical composition codes allow efficient conversion or off-loading for concurrent processing of composed (concatenated) groups of primitives in a stream without having to individually parse each primitive before off-loading.

# Concrete Domain Representations

Text, *T*, domain representations in CESR use only the characters from the URL and filename safe variant of the IETF RFC-4648 Base64 standard {{RFC4648}}. Unless otherwise indicated, all references to Base64 {{RFC4648}} in this document imply the URL and filename safe variant. The URL and filename safe variant of Base64 uses in order the 64 characters `A to Z`, `a to z`, `0 to 9`,  `-`, and `_` to encode 6 bits of information. In addition, Base64 uses the `=` character for padding, but CESR does not use the `=` character for any purpose because all CESR-encoded primitives are composable.

Notable is the fact that Base64 {{RFC4648}} by itself does not satisfy the composability property and must employ pad characters to ensure one-way convertibility between binary and text.

In CESR, however, both *T* and *B* domain representations include a prepended framing code prefix that is structured in such a way as to ensure composability.

Suppose, for example, we wish to use Base64 characters in the text domain and binary bytes in the binary domain. For the sake of example, we will call these, respectively, naive text and naive binary encodings and domains. Recall that a byte encodes 8 bits of information and a Base64 character encodes 6 bits of information. Furthermore, suppose that we have three primitives denoted `a`, `b`, and `c` in the naive binary domain with lengths of 1, 2, and 3 bytes, respectively.

In the following diagrams, we denote each byte in a naive binary primitive with zero-based most significant bit first indices.  For example, `a1` is bit one from `a`, `a0` is bit zero, and `A0` for byte zero, `A1` for byte 1, etc.

The byte and bit-level diagrams for `a` are shown below, where we use `A` to denote its bytes:

~~~text
|           A0          |
|a7:a6:a5:a4:a3:a2:a1:a0|
~~~

Likewise for `b` below:

~~~text
|           B1          |           B0          |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
~~~

And finally, for `c` below:

~~~text
|           C2          |           C1          |           C0          |
|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|
~~~

## Conversions

When doing a naive Base64 conversion of a naive binary primitive, one Base64 character represents only six bits from a given byte. In the following diagrams, each character of a Base64 conversion is denoted using zero-based indices, with the most significant character first.

Therefore encoding `a` in Base64 requires at least two Base64 characters because the zeroth character only captures the six bits from the first byte, and another character is needed to capture the other two bits. The convention in Base64 uses a Base64 character where the non-coding bits are zeros. This is diagrammed as follows:

~~~text
|           A0          |
|a7:a6:a5:a4:a3:a2:a1:a0|z3:z2:z1:z0|
|        T1       |        T0       |
~~~
where `aX` represents a bit from `A0` and `zX` represents a zeroed pad bit, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string.

Naive Base64 encoding always pads each individual conversion of a string of bytes to an even multiple of four characters. This provides a property that is not true composability but does ensure that multiple distinct concatenated conversions from binary to Base64 text are separable. It may be described as a sort of one-way composability. So with pad characters, denoted by replacing the spaces with `=` characters, the Base64 conversion of `a` is as follows:

~~~text
|           A0          |
|a7:a6:a5:a4:a3:a2:a1:a0|z3:z2:z1:z0|
|        T3       |        T2       |========P1=======|========P0=======|
~~~
where `aX` represents a bit from `a`, `AX` represents a byte from `a`,  `zX` represents a zeroed pad bit, `PX` represents a trailing pad character, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string. We see that Base64 conversion effectively left shifts `a` by four bits plus two pad characters. In other words, the Base64 conversion of `a` is no longer right-aligned with respect to the trailing Base64 character.

Likewise, `b` requires at least three Base64 characters to capture all of its sixteen bits of information as follows:

~~~text
|           B1          |           B0          |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|z1:z0|
|        T2       |        T1       |        T0       |
~~~
where `bX` represents a bit from `b`, `BX` represents a byte from `b`, `zX` represents a zeroed pad bit,  and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string.
Alignment on a four-character (24-bit) boundary requires one pad character this becomes:

~~~text
|           B1          |           B0          |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|z1:z0|
|        T2       |        T1       |        T0       |========P0=======|

~~~
where `bX` represents a bit from `b`, `BX` represents a byte from `b`, `zX` represents a zeroed pad bit, `PX` represents a trailing pad character,  and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string. We see that Base64 conversion effectively left shifts `a` by two bits plus one pad character. In other words, the Base64 conversion of `b` is no longer right-aligned with respect to the trailing Base64 character.

Finally, `c`  requires exactly four Base64 characters to capture all of its twenty-four bits of information. There are no pad characters required.

~~~text
|           C2          |           C1          |           C2          |
|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|
|        T3       |        T2       |        T1       |        T0       |
~~~
where `cX` represents a bit from `c`, `CX` represents a byte from `c`,  and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string. There are no bit shifts because there are no pad bits nor pad characters needed, and the resulting Base64 conversion is right aligned with respect to the trailing Base64 character.

Suppose now we concatenate `a + b` into a three-byte composition in the naive binary domain before Base64 encoding the concatenated whole. We have the following:

~~~text
|           A0          |           B1          |           B0          |
|a7:a6:a5:a4:a3:a2:a1:a0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|        T3       |        T2       |        T1       |        T0       |
~~~

We see that the least significant two bits of `A0` are encoded into the same character, `T2` as the four most significant four bits of `B1`. Therefore, a text-domain parser would be unable to cleanly de-concatenate on a character-by-character basis the conversion of `a + b` into separate text-domain primitives. Therefore, standard (naive) binary to Base64 conversion does not satisfy the composability constraint.

Suppose instead we start in the text domain with primitives `u` and `v` of lengths 1 and 3 characters, respectively.
If we concatenate these two primitives as `u + v` in the text domain and then convert them as a whole to naive binary. We have the following:

~~~text
|        U0       |        V2       |        V1       |        V0       |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|           B2          |           B1          |           B0          |
~~~

We see that all six bits of information in `U0` is included in `B2` along with the least significant two bits of information in `V2`. Therefore a binary domain parser is unable to cleanly de-concatenate on a byte-by-byte basis the conversion of `u + v` into separate binary domain primitives. Therefore, standard (naive) Base64 to binary conversion does not satisfy the composability constraint.

Indeed, the composability property is only satisfied if each primitive in the *T* domain is an integer multiple of four Base64 characters (24 bits) and each primitive in the *B* domain is an integer multiple of three bytes (24 bits). Each of either four Base64 text characters or three binary bytes captures twenty-four bits of information. Twenty-four is the least common multiple of six and eight. Therefore in order to cleanly capture integer multiples of twenty-four bits of information, primitive lengths MUST be integer multiples of either four Base64 text characters or three binary bytes in their respective domains. Given the constraint of alignment on 24-bit boundaries in either text or binary domains is satisfied, the conversion of concatenated primitives in one domain never results in the same byte or character in the converted domain sharing bits from two adjacent primitives. This constraint of 24-bit alignment, therefore, satisfies the *composability* property.

To elaborate, when converting streams made up of concatenated primitives back and forth between the *T* and *B* domains, the converted results will not align on byte or character boundaries at the end of each primitive unless the primitives themselves are integer multiples of twenty-four bits of information. In other words, all primitives must be aligned on twenty-four-bit boundaries to satisfy the composability property. This means that the length of any primitive in the *B* domain MUST be an integer multiple of three binary bytes with a minimum length of three binary bytes. Likewise, this means that the length of any primitive in the *T* domain MUST be an integer multiple of 4 Base64 characters with a minimum length of four Base64 characters.

## Stable Text Type Codes

There are many coding schemes that could satisfy the composability constraint of alignment on 24-bit boundaries. The main reason for using a *T* domain-centric encoding is higher usability, readability, or human friendliness. Indeed a primary design goal of CESR is to select an encoding approach that provides high usability, readability, or human friendliness in the *T* domain. This type of usability goal is simply not realizable in the *B* domain. The B domain's purpose is merely to provide convenient compactness at scale. We believe usability in the *T* domain is maximized when the type portion of the prepended framing code is *stable* or *invariant*. Stable type coding makes it much easier to recognize primitives of a given type when debugging source, reading messages, or documents in the *T* domain that include encoded primitives. This is true even when those primitives have different lengths or values. For primitive types that have fixed lengths, i.e. all primitives of that type have the same length, stable type coding aids not only visual type but visual size recognition.

The usability of stable type coding is maximized when the type portion appears first in the framing code. Stability also requires that for a given type, the type coding portion must consume a fixed integer number of characters in the *T* domain. To clarify, as used here, stable type coding in the *T* domain never shares information bits with either length or value coding in any given framing code character and appears first in the framing code. Stable type coding in the *T* domain translates to stable type coding in the *B* domain except that the type coding portion of the framing code may not respect byte boundaries. This is an acceptable tradeoff because binary-domain parsing tools easily accommodate bit fields and bit shifts while text-domain parsing tools do not. Generally, text-domain parsing tools only process whole characters. This is another reason to impose a stability constraint on the *T* domain type coding instead of the *B* domain.

## Stable Value Encoding

A secondary usability constraint is recognizable or readable stable value coding in the text, *T*, domain. Not all primitives benefit from stable value coding. Any representation of a value that is a long random string of characters is essentially unreadable or recognizable versus some other representation. Bit shifts of the value, as long as they are static, do not change the readability. This is not true, however of values that are small numbers. Base64 encodings of small numbers are readable. for example, the numerical sequence of decimal numbers, `0, 1, 2`, is recognizable as the sequence of Base64 characters, `A, B, C`. Thus, all else equal, readable stable value encodings also contribute to usability, at least in some cases.

## Code Characters and Lead Bytes

There are two ways to provide the required alignment on 24-bit boundaries to satisfy the composability property. One is to post-pad, with trailing pad characters, `=`, the text domain encoding to ensure that the *T* domain primitive has a total size (length) that is an integer multiple of 4. This is what naive Base64 encoding does. The other way is to pre-pad leading bytes of zeros to the raw binary value before conversion to Base64 to ensure the total size of the raw binary value with pre-pad bytes is an integer multiple of 3 bytes. This ensures that the size in characters of the Base64 conversion of the pre-padded raw binary is an integer multiple of 4 characters.

Given the second way, there is one of two options that depend on the specific code. In the first option, an appropriate number of text characters that result from the conversion of a porting of the leading pre-pad zero bytes are replaced with the appropriate number of code characters. In the second option, the code characters are pre-pended to the conversion with leading zeros intact. In the second option, the length of the pre-pended type code MUST also, thereby, be an integer multiple of 4 characters. In either option, the total length of the *T* domain primitive with code is an integer multiple of 4 characters.

The first way may be more compact in some cases than the second. The second way may be easier to compute in some cases. The most significant advantage of the second way is that the value portion of is stable and more readable both in the text, *T*, domain and in the, *B*, binary domain because the value portion is not shifted by the Base64 conversion as it is with the first way.

In order to avoid confusion with the use of the term `pad character`, when pre-padding with bytes that are not replaced later, we use the term `lead bytes`. The term pad may be confusing not merely because both ways use a type of padding but it is also true that the number of pad characters when padding post-conversion equals the number of lead bytes when padding pre-conversion.

Suppose for example the raw binary value is 32 bytes in length. The next higher integer multiple of 3 is 33 bytes. Thus 1 additional leading pad byte is needed to make the size (length in byte) of raw binary an integer multiple of 3. The 1 lead byte makes that combination a total of 33 bytes in length. The resultant Base64 converted value will be 44 characters in length, which is an integer multiple of 4 characters. In contrast, recall that when we convert a 32-byte raw binary value to Base64 the converted value will have 1 trailing pad character. In both cases, the resultant length in Base64 is 44 characters.

Similarly, a 64-byte raw binary value needs 2 lead bytes to make the combination 66 bytes in length where 66 is the next integer multiple of 3 greater than 64. When converted the result is 88 characters in length. The number of pad characters added on the result of the Base64 conversion of a 64-byte raw binary is also 2.

In summary, there are two possibilities for CESR's coding scheme to ensure a composable 24-bit alignment. The first is to add trailing pad characters post-conversion. The second is to add leading pad bytes pre-conversion. Because of the greater readability of the value portion of both the fully qualified text, *T*, or fully qualified binary, *B*, domain representations, the second approach was chosen for CESR.

## Multiple Code Table Approach

The design goals for CESR framing codes include minimizing the framing code size for the most frequently used (most popular) codes while also supporting a sufficiently comprehensive set of codes for all foreseeable current and future applications. This requires a high degree of both flexibility and extensibility. We believe this is best achieved with multiple code tables each with a different coding scheme that is optimized for a different set of features instead of a single one-size-fits-all scheme. A specification that supports multiple coding schemes may appear on the surface to be much more complex to implement but careful design of the coding schemes can reduce implementation complexity by using a relatively simple single integrated parse and conversion table. Parsing in any given domain given stable type codes may then be implemented with a single function that simply reads the appropriate type selector in the table to know how to parse and convert the rest of the primitive.

# Text Coding Scheme Design

## Text Code Size
Recall from above, that the R domain representation is a pair`(text code, raw binary)`. The text code is stable and begins with one or more Base64 characters that provide the primitive type and may also include one or more additional characters that provide the length. The actual usable cryptographic material is provided by the *raw binary* element.

The corresponding *T* domain representation of this pair is created by first prepending leading pad bytes of zeros to the *raw binary* element. This result is then converted to Base64. Depending on the code, either the frontmost characters that result from the Base64 conversion of leading pad bytes of zeros are replaced with the text code element of appropriate size in characters, or an appropriately sized text code element is prepended to the conversion without replacing any characters.

Recall that when the length of a given naive binary string is not an integer multiple of three bytes, standard Base64 conversion software appends one or two pad characters to the resultant Base64 conversion.

With standard Base64 conversion that employs pad characters, the text domain representation that results from the individual conversion of a set of binary strings when concatenated in the text domain after conversion and stripping off pad characters is not necessarily equivalent to the text domain representation that results from converting en masse to text the concatenation of the same set of binary strings and then stripping off pad characters. In the latter case, knowledge of the set of binary strings is lost because the resultant conversion may have bits from two binary bytes concatenated in one text character. Restated, the problem with standard Base64 is that it does not preserve byte boundaries after the en-masse conversion of concatenated binary strings. Consequently, standard (naive) Base64 does not provide two-way or true composability as defined above.

To elaborate, the number of pad characters appended with standard Base64 encoding is a function of the length of the binary string. Let *N* be the length in bytes of the binary string. When `N mod 3 = 1`, then there are 8 bits in the remainder that must be encoded into Base64. Recall from the examples above that a single byte (8 bits) requires two Base64 characters. The first encodes 6 bits and the second the remaining 2 bits for a total of 8 bits. The last character is selected such that its non-coding 4 bits are zero. Thus two additional pad characters are required to pad out the resulting conversion so that its length is an integer multiple of 4 Base64 characters. Furthermore when
`N mod 3 = 1`, then the addition of 2 more zeroed bytes to the length of the binary string such that `M = N + 2` would result in `M mod 3 = 0` or equivalently `N + 2 mod 3 = 0`.

Similarly, when `N mod 3 = 2`, then there are two bytes (16 bits) in the remainder that must be encoded into Base64. Recall from the examples above that two bytes (16 bits) require three Base64 characters. The first two encode 6 bits each (for 12 bits) and the third encodes the remaining 4 bits for a total of 16. The last character is selected such that its non-coding 2 bits are zero. Thus one additional trailing pad character is required to pad out the resulting conversion so that its length is an integer multiple of 4 characters. Furthermore when
`N mod 3 = 2`, then the addition of 1 more byte of zeros added to the length of the binary string such that `M = N + 1` would result in `M mod 3 = 0` or equivalently `N + 2 mod 3 = 0`. Thus the number of leading pre-pad zeroed bytes needed to align the binary string on a 24-bit boundary is the same as the number of trailing pad characters needed to align the converted Base64 text string on a 24-bit boundary.

Finally, when `N mod 3 = 0` then the binary string is aligned on a 24-bit boundary and no trailing pad characters are required to ensure the length of the Base64 conversion is an integer multiple of 4 characters. Likewise, no leading pad bytes are required to ensure the length of the binary string is an integer multiple of 3 bytes.

Thus, in all three cases, the number of trailing post-pad characters, if any, needed to align the converted Base64 text string on a 24-bit boundary is the same as the number of leading pre-pad bytes, if any, needed to align the binary string on a 24-bit boundary.

The number of required trailing Base64 post-pad characters or equivalently the number of leading pre-pad zeroed bytes to ensure 24 bit alignment may be computed with the following formula:

`ps = (3 - (N mod 3)) mod 3)`, where `ps` is the pad size (pre-pad bytes or post-pad characters) and `N` is the size in bytes of the binary string.

Recall that composability is provided here by prepending text codes that are of the appropriate length to ensure 24-bit boundaries in both the *T* and the corresponding *B* domain. The advantage of this approach is that naive Base64 software tooling may be used to convert back and forth between the *T* and *B* domains, i.e. `T(B)` is naive Base64 encode, and `B(T)` is naive Base64 decode. In other words, CESR primitives are compatible with existing Base64 (RFC-4648) tooling. Whereas new software tooling is needed for conversions between the *R* and *T* domains, e.g. `T(R)` and `R(T)` and the *R* and *B* domains, e.g. `B(R)` and `R(B)`.

The pad size computation is also useful for computing the size of the text codes. Because true composability also requires that the *T* domain value MUST be an integer multiple of 4 characters in length the size of the text code MUST also be a function of the pad size, `ps`, and hence the length of the raw binary element, `N`. Thus the size of the text code in Base64 characters is a function of the equivalent pad size determined by the length `N mod 3` of the raw binary value. If we let *M* be a non-negative integer-valued variable then we have three cases:

|   Pad Size   |  Code Size  |
|:------------:|------------:|
|0|4•M|
|1|4•M + 1|
|2|4•M + 2|

The minimum code sizes are 1, 2, and 4 characters for pad sizes of 1, 2, and 0 characters with *M* equaling 0, 0, and 1 respectively. By increasing *M* we can have larger code sizes for a given pad size.

## Pre-Padding Before Conversion.

Returning to the examples above, let's observe what happens when we pre-pad the binary strings with zeroed leading pad bytes of the appropriate length given by `ps = (3 - (N mod 3)) mod 3)` where `ps` is the number of leading pad bytes and `N` is the length of the raw binary string before padding is prepended.

### One Byte

For the one byte raw binary string `a`, `ps` is two. The pre-padded conversion results in the following:

~~~text
|           Z1          |           Z0          |           A0          |
|z7:z6:z5:z4:z3:z2:z1:z0|z7:z6:z5:z4:z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
|        T3       |        T2       |        T1       |        T0       |
~~~
where  `ZX` represents a zeroed pre-pad byte, `zX` represents a zeroed pre-pad bit, `AX` represents a byte from `a`, `aX` represents a bit from `a`, and `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `a`.

Noteworthy is that the first two (i.e. `ps`) characters of the conversion, namely, `T3T2` does not include any bits of information from `a`. This also means that `T3T2` can be modified after conversion without impacting the appearance or value of the converted `a` that appears solely in `T1T0`, i.e. there is no overlap. Moreover, the resulting Base64 conversion of `a` is right aligned with respect to the trailing Base64 character. This means that one can "read" and understand the numerical values for `a` from such an unshifted Base64 conversion.  This also means that a text-based parser on a character-by-character basis can cleanly process `T3T2` separate from the Base64 encoding of `a` that appears in `T1T0`. Given this separation we could replace `T3T2` with  two character Base64 textual type code `C1C0` as follows:

~~~text
|           Z1          |           Z0          |           A0          |
|z7:z6:z5:z4:z3:z2:z1:z0|z7:z6:z5:z4:z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
|        S1       |        S0       |        T1       |        T0       |
|s5:s4:s3:s2:s1:s0|s5:s4|s3:s2:s1:s0|z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
~~~
where  `ZX` represents a zeroed pre-pad byte, `zX` represents a zeroed pre-pad bit, `AX` represents a byte from `a`, `aX` represents a bit from `a`,  `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `a`, `SX` represents a Base64 code character replacing one of the `TX`, and `sX` is a code bit. The resultant four-character Base64 encoded primitive would be `C1C0T1T0`.

When `C1C0T1T0` is converted back to binary from Base64, the result would be as follows:

~~~text
|        S1       |        S0       |        T1       |        T0       |
|s5:s4:s3:s2:s1:s0|s5:s4|s3:s2:s1:s0|z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
|           U1          |        U0             |           A0          |
~~~

where  `CX` represents a Base64 code character replacing one of the `TX`, `cX` is a code bit, `UX` represents a byte from the converted code char, which may include zeroed bits, `zX` represents a zeroed pre-pad bit, `AX` represents a byte from `a`, `aX` represents a bit from `a`, and `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `a`.

Stripping off `U1U0` leaves `a` in its original state. Noteworthy is that the code characters (only) are effectively left shifted 4 bits after conversion. The code characters `S1S0` can be recovered as the first two characters that are obtained from simply converting `U1O0` only back to Base64.


## Two Byte

For the two-byte raw binary string `b`, `ps` is one. The pre-padded conversion results in the following:

~~~text
|           Z0          |           B1          |           B0          |
|z7:z6:z5:z4:z3:z2:z1:z0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|        T3       |        T2       |        T1       |        T0       |
~~~
where  `ZX` represents a zeroed pre-pad byte, `zX` represents a zeroed pre-pad bit, `BX` represents a byte from `b`, `bX` represents a bit from `b`, and `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `b`.

Noteworthy is that the first one (i.e., `ps`) character of the conversion, namely, `T3`, does not include any bits of information from `b`. This also means that `T3` can be modified after conversion without impacting the appearance or value of the converted `b` that appears solely in `T2T1T0`, i.e., there is no overlap. Moreover, the resulting Base64 conversion of `b` is right aligned with respect to the trailing Base64 character. This means that one can "read" and understand the numerical values for `b` from such an unshifted Base64 conversion.  This also means that a text-based parser on a character-by-character basis can cleanly process `T3` separate from the Base64 encoding of `b` that appears in `T2T1T0`. Given this separation, we could replace `T3` with one character Base64 textual type code `C0` as follows:

~~~text
|           Z1          |           B1          |           B0          |
|z7:z6:z5:z4:z3:z2:z1:z0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|        S0       |        T2       |        T1       |        T0       |
|s5:s4:s3:s2:s1:s0|z1:z0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
~~~
where  `ZX` represents a zeroed pre-pad byte, `zX` represents a zeroed pre-pad bit, `BX` represents a byte from `b`, `bX` represents a bit from `b`,  `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `b`, `SX` represents a Base64 code character replacing one of the `TX`, and `sX` is a code bit,. The resultant four-character Base64 encoded primitive would be `S0T2T1T0`.

When `S0T2T1T0` is converted back to binary from Base64, the result would be as follows:

~~~text
|        S0       |        T2       |        T1       |        T0       |
|s5:s4:s3:c2:cs:s0|z1:z0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|           U0          |           U1          |           A0          |
~~~
where  `SX` represents a Base64 code character replacing one of the `TX`, `sX` is a code bit, `UX` represents byte from converted code char which may include zeroed bits, `zX` represents a zeroed pre-pad bit, `BX` represents a byte from `b`, `bX` represents a bit from `b`, and `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `b`.

Stripping off `U0` leaves `b` in its original state. Noteworthy is that the code character (only) is effectively left shifted 4 bits after conversion. The code character `S0` can be recovered as the first character obtained from simply converting `U0` only to Base64.

### Three Byte

For the three-byte raw binary string `c`, `ps` is zero. So pre-padding is not needed.
~~~text
|           C2          |           C1          |           C2          |
|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|
|        T3       |        T2       |        T1       |        T0       |
~~~
where `cX` represents a bit from `c`, `CX` represents a byte from `c`,  and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string. There are no bit shifts because there are no pad bits nor pad characters needed, and the resulting Base64 conversion is right aligned with respect to the trailing Base64 character.

Without pad characters, however, there is no room to hold a type code. Consequently, any text type code is just prepended to the conversion. The prepended type code MUST be an integer multiple of four Base64 characters. Let `S3S2S1S0` be the type code, then the full primitive with code and converted raw binary is given by the eight-character Base64 string `S3S2S1S0T3T2T1T0`.

When `S3S2S1S0T3T2T1T0` is converted back to binary, there is no overlap or bit shifting because both the code and raw binary `c` are each separately aligned on twenty-four-bit boundaries.

### Examples

Suppose, for example, that we wish to encode two-byte raw binary numbers into CESR using the pre-pad approach described above. In order to achieve twenty-four-bit alignment, the pre-pad size for two-byte numbers is 1 byte. As described above, this means the minimally sized text code is 1 Base64 character. Suppose that the text code is `M` (Base64).  The following table provides examples of encoding the different two-byte raw binary values in the three domains: raw, text, and binary. Recall that the raw-domain is expressed by a tuple of (code, raw) where the code is Base64 text and the raw is the raw binary value without code. For readability, raw binary values are represented in hexadecimal notation.

|  Raw |  Text  |  Binary |
|------------:|------------:|------------:|
| ("M", 0x0000) | "MAAA" | 0x300000 |
| ("M", 0x0001) | "MAAB" | 0x300001 |
| ("M", 0xffff) | "MP__" | 0x30ffff |

With this approach, both the binary-domain and text-domain representations are as compact as possible for a fully qualified primitive that satisfies the composability property. The text domain representation has a stable readable code and a stable readable value. The binary domain is value right aligned. The text domain representation consists of 4 text printable characters from the Base64 set of characters, and the binary domain representation consists of 3 bytes. Both are parsable in each domain along character/byte boundaries respectively. A parser reads the first character/byte. It then processes that value to get an index into a lookup table that it uses to find how many remaining characters/bytes to extract from the stream. This makes the primitive self-framing.


## Count, Group, or Framing Codes

As mentioned above, one of the primary advantages of composable encoding is that special framing codes can be specified to support groups of primitives. Grouping enables pipelining. Other suitable terms for these special *framing codes* are *group codes* or *count codes* for short. These are suitable terms because these framing codes can be used to count characters, primitives in a group, or groups of primitives in a larger group when parsing and offloading a stream of CESR primitives.

A count code is it's own composable primitive, and its length, therefore, MUST be an integer multiple of four characters in the text domain or equivalently an integer multiple of three bytes in the binary domain. To clarify, a count code is primitive that does not include a raw binary value, only its text code. Because a count code's raw binary element value is empty and its length is an integer multiple of four characters (three bytes), its pad size is always 0.

To elaborate, we can use count codes as separators to better organize a stream of primitives or to interleave non-native (non-CESR) serializations. Count codes enable the grouping together of any combination of primitives, groups of primitives, or non-native serializations to optimize pipelining and offloading.

## Interleaved Non-CESR Serializations

As mentioned above, one extremely useful property of CESR is that special count codes enable CESR to be interleaved with other serializations. For example, Many applications use JSON {{JSON}}{{RFC4627}}, CBOR {{CBOR}}{{RFC8949}}, or MsgPack (MGPK) {{MGPK}} to serialize flexible self-describing data structures based on field maps, also known as dictionaries or hash tables. With respect to field map serializations, CESR primitives may appear in two different contexts. The first context is as a delimited text primitive inside of a field map serialization. The delimited text may be either the key or value of a (key, value) pair. The second context is as a standalone serialization that is interleaved with field map serializations in a stream. Special CESR count codes enable support for the second context of interleaving standalone CESR with other serializations.

## Cold Start Stream Parsing Problem

After a cold start, a stream processor looks for framing information to know how to parse groups of elements in the stream. If that framing information is ambiguous then the parser may become confused and require yet another cold start. While processing a given stream, a parser may become confused, especially if a portion of the stream is malformed in some way. This usually requires flushing the stream and forcing a cold start to resynchronize the parser to subsequent stream elements. Better yet is a re-synchronization mechanism that does not require flushing the in-transit buffers but merely skipping to the next well-defined stream element boundary in order to execute a cold start. Good cold start re-synchronization is essential to robust performant stream processing.

For example, in TCP a cold start usually means closing and then reopening the TCP connection. This flushes the TCP buffers and sends a signal to the other end of the stream that may be interpreted as a restart or cold start. In UDP each packet is individually framed but a stream may be segmented into multiple packets so a cold start may require an explicit ack or nack to force a restart.

Special CESR count codes support re-synchronization at each boundary between interleaved CESR and other serializations like JSON, CBOR, or MGPK


### Performant Resynchronization with Unique Start Bits

Given the popularity of three specific serializations, namely, JSON, CBOR, and MGPK, more fine-grained serialization boundary detection for interleaving CESR may be highly beneficial for both performance and robustness reasons. One way to provide this is by selecting the count code start bits such that there is always a unique (mutually distinct) set of start bits at each interleaved boundary between CESR, JSON, CBOR, and MGPK.

Furthermore, it may also be highly beneficial to support in-stride switching between interleaved CESR text-domain streams and CESR binary domain streams. In other words, the start bits for count (framing) codes in both the *T* domain (Base64) and the *B* domain should be unique. This would provide the analogous equivalent of a UTF Byte Order Mark (BOM) {{BOM}}. Recall that a BOM enables a parser of UTF-encoded documents to determine if the UTF codes are big-endian or little-endian {{BOM}}. In the CESR case, an analogous feature would enable a stream parser to know if a count code, along with its associated counted or framed group of primitives, is expressed in the *T* or *B* domain. Together these impose the constraint that the boundary start bits for interleaved text CESR, binary CESR, JSON, CBOR, and MGPK be mutually distinct.

Amongst the codes for map objects in the JSON, CBOR, and MGPK, only the first three bits are fixed and not dependent on mapping size. In JSON, a serialized mapping object always starts with `{`. This is encoded as `0x7b`. the first three bits are `0b011`. In CBOR, the first three bits of the major-type of its serialized mapping object are `0b101`. In MGPK (MsgPack), there are three different mapping object codes. The *FixMap* code starts with `0b100`. Both the *Map16* code and *Map32* code start with `0b110`.

So we have the set of four used starting tritets (3 bits) in numeric order of `0b011`, `0b100`, `0b101`, and `0b110`. This leaves four unused tritets, namely, `0b000`, `0b001`, `0b010`, and `0b111`, that are potential candidates for the CESR count (framing) code start bits. In Base64, there are two codes that satisfy our constraints. The first is the dash character, `-`, encoded as `0x2d`. Its first three bits are `0b001`. The second is the underscore character,`_`, encoded as `0x5f`. Its first three bits are `0b010`. Both of these are distinct from the starting tritets of any of the JSON, CBOR, and MGPK encodings above. Moreover, the starting tritet of the corresponding binary encodings of `-` and `_` is `0b111`, which is also distinct from all the others. To elaborate, Base64 uses `-` in position 62 or `0x3E` (hex) and uses `_` in position 63 or `0x3F` (hex), both of which have starting tritet of `0b111`

This gives us two different Base64 characters, `-` and `_`, that can be used for the first character of any framing (count) code in the *T* domain. This also means we can have two different classes of framing (count) codes. Using framing codes in this way also provides a BOM-like capability that enables a parser to determine if the framing code itself is expressed in either the *T* or *B* domain. To clarify, if a stream frame (group) starts with the tritet `0b111`, then the stream frame is *B* domain CESR, and a stream parser would thereby know how to convert the first sextet of the stream frame to determine which of the two framing codes is being used, `0x3E` or `0x3F`. If, on the other hand, the framing code starts with either of the tritets `0b001` or `0b010`, then the framing code is expressed in the *T* domain, and a stream parser likewise would thereby know how to convert the first character (octet) of the framing code to determine which framing code is being used for that frame. Otherwise, if a stream starts with  `0b011`, then it's JSON, with either `0b100` or `0b110`, then it's MGPK, and with `0b101`, then it's CBOR.

This is summarized in the following table:


|   Starting Tritet   |  Serialization  | Character |
|:------------:|:------------:|:------------:|
|0b000|Unused||
|0b001|CESR *T* Domain Count (Group) Code|`-`|
|0b010|CESR *T* Domain Op Code|`_`|
|0b011|JSON|`{`|
|0b100|MGPK||
|0b101|CBOR||
|0b110|MGPK||
|0b111|CESR *B* Domain Count Code or Op Code||


### Stream Parsing Rules

Given this set of tritets (3 bits), we can express a requirement for a well-formed stream start and restart.

Each stream MUST start (restart) with one of five tritets:

1) A framing count (group) code in CESR *T* domain
2) A framing count (group) code in CESR *B* Domain.
3) A JSON encoded mapping.
4) A CBOR encoded Mapping.
5) A MGPK encoded mapping.


A parser merely needs to examine the first tritet (3 bits) of the first byte of the stream start to determine which one of the five it is. When the first tritet is a framing code then the remainder of the framing code itself will include the additional information needed to parse the attached group (frame). When the first tritet indicates its JSON, CBOR, or MGPK, then the mapping's first field must be a version string that provides the additional information needed to fully parse the associated encoded field map serialization.

The stream MUST resume with a frame starting byte that begins with one of the 5 tritets, either another framing code expressed in the *T* or *B* domain or a new JSON, CBOR, or MGPK encoded mapping.

This provides an extremely compact and elegant stream parsing formula that generalizes not only support for CESR composability but also support for interleaved CESR with three of the most popular hash map serializations.


## Compact Fixed Size Codes

As mentioned above, CESR uses a multiple-code table design that enables both size-optimized text codes for the most popular primitive types and extensible universal support for all other primitive types. Modern cryptographic suites support limited sets of raw binary primitives with fixed (not variable) sizes. The design aesthetic is based on the understanding that there is minimally sufficient cryptographic strength and more cryptographic strength is just wasting computation and bandwidth. Cryptographic strength is measured in bits of entropy which also corresponds to the number of trials that must be attempted to succeed in a brute-force attack. The accepted minimum for cryptographic strength is 128 bits of entropy or equivalently `2**128` (2 raised to the 128th power) brute force trials. The size in bytes of a given raw binary primitive for a given modern cryptographic suite is usually directly related to this minimum strength of 128 bits (16 bytes).
For example, the raw binary primitives from the well-known {{NaCL}} ECC (Elliptic Curve Cryptography) library all satisfy this 128-bit strength goal. In particular, the digital signing public key raw binary primitives for EdDSA are 256 bits (32 bytes) in length because well-known algorithms can reduce the number of trials to brute force invert an ECC public key to get the private key by the square root of the number of scalar multiplications which is also related to the size of both the private key and public key coordinates (discrete logarithm problem {{DLog}}). Therefore 256 bit (32-byte) ECC keys are needed to achieve 128 bits of cryptographic strength. In general, the size of a given raw binary primitive is typically some multiple of 128 bits of cryptographic strength. This is also true for the associated EdDSA raw binary signatures which are 512 bits (64 bytes) in length.

Similar scale factors exist for cryptographic digests. A standard default Blake3 digest is 256 bits (32 bytes) in length in order to get 128 bits of cryptographic strength. This is also true of SHA3-256. Indeed the sweet spots for modern cryptographic raw primitive lengths are 32 bytes for many digests as well as EdDSA public and private keys as well as ECDSA private keys. Likewise, 64 bytes is the sweet spot for EdDSA and ECDSA-secp256k1 signatures and 64-byte variants of the most popular digests. Therefore optimized text code tables for these two sweet spots (32 and 64 bytes) would be highly advantageous.

A 32-byte raw binary value has a pad size of 1 character.

~~~text
(3 - (32 mod 3)) mod 3) = 1
~~~

Therefore the minimal text code size is 1 character for 32-byte raw binary cryptographic material and all other raw binary material values whose pad size is 1 character.

A 64-byte raw binary value has a pad size of 2 characters.

~~~text
(3 - (64 mod 3)) mod 3) = 2
~~~

Therefore the minimal text code size is 2 characters for 64-byte raw binary cryptographic material and all other raw binary material values whose pad size is 1 character. For example, a 16-byte raw binary value also has a pad size of 2 characters.

For all other cryptographic material values whose pad size is 0, such as the 33-byte ECDSA public keys then, the minimum size text code is 4 characters. So the minimally sized text code tables are 1, 2, and 4 characters, respectively.

Given that a given cryptographic primitive type has a known fixed raw binary size, then we can efficiently encode that primitive type and size with just the type information. The size is given by the type.

So, for example, an Ed25519 (EdDSA) raw public key is always 32 bytes so knowing that the type is `Ed25519 public key` implies the size of 32 bytes and a pad size of 1 character that, therefore, may be encoded with a 1 character text code. Likewise, an Ed25519 (EdDSA) signature is always 64 bytes, so knowing that the type is  `Ed25519 signature` implies the size of 64 bytes and a pad size of 2 characters that, therefore, may be encoded with a 2-character text code.

## Code Table Selectors

In order to efficiently parse a stream of primitives with types from multiple text code tables, the first character in the text code must determine which code table to use, either a default code table or a code table selector character when not the default code table. Thus the 1 character text code table must do double duty. It must provide selectors for the different text code tables and also provide type codes for the most popular primitives that have a pad size of 1 that appears as the default code table. There are 64 Base64 characters (64 values). We only need 12 tables to support all the codes and code formats needed for the foreseeable future. Therefore only 12 of those characters need to be dedicated as code table selectors, which leaves 52 characters that may be used for the 1 character type codes in the default table. This gives a total of 13 type code tables consisting of the dual purpose 1 character type or selector code table and 12 other tables.

As described above, the selector characters for the framing or count code tables that best support interleaved JSON, CBOR, and MGPK are `-` and `_`. We use the numerals `0` through `9` to each serve as a selector for the other tables. That leaves the letters `A` to `Z` and `a` to `z` as single character selectors. This provides 52 unique type codes for fixed-length primitive types with raw binary values that have a pad size of 1.

To clarify, the first character of any primitive is either a selector or a 1-character code type. The characters `0` through `9`, `-`, and `_` are selectors that select a given code table and indicate the number of remaining characters in the text code.

## Small Fixed Raw Size Tables

There are two special tables that are dedicated to the most popular fixed-size raw binary cryptographic primitive types. These are the most compact, so they optimize bandwidth but only provide a small number of total types. In both of these, the text code size equals the number of pad characters, i.e. the pad size.

### One Character Fixed Raw Size Table

The one character type code table does not have a selector character per se but uses as type codes the non-selector characters `A` - `Z` and `a` - `z`. This provides 52 unique type codes for fixed-size raw binary values with a pad size of 1.

### Two-Character Fixed Raw Size Table
The two-character type code table uses the selector `0` as its first character. The second character is the type code. This provides 64 unique type codes for fixed-size raw binary values that have a pad size of 2.

## Large Fixed Raw Size Tables
The three tables in this group are for large fixed raw-size primitives. These three tables use 0, 1, or 2 lead bytes as appropriate for a pad size of 0, 1, or 2 for a given fixed raw binary value. The text code size for all three tables is 4 characters. The selector character not only encodes the table but also implicitly encodes the number of lead bytes. The 3 remaining characters is each type code in each table, providing 262,144 unique types. This should provide enough type codes to accommodate all fixed raw-size primitive types for the foreseeable future.

### Large Fixed Raw Size Table With 0 Lead Bytes
This table uses `1` as its first character or selector. The remaining 3 characters provide the type of each code. Only fixed-size raw binaries with a pad size of 0 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values (`262144 = 64**3`) for fixed-size raw binary primitives with a pad size of 0.

### Large Fixed Raw Size Table With 1 Lead Byte
This table uses `2` as its first character or selector. The remaining 3 characters provide the type of each code. Only fixed-size raw binaries with a pad size of 1 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values (`262144 = 64**3`). Together with the 52 values from the 1-character code table above, there are 262,196 type codes for fixed-size raw binary primitives with a pad size of 1.

### Large Fixed Raw Size Table With 2 Lead Bytes
This table uses `3` as its first character or selector. The remaining 3 characters provide the type of each code. Only fixed-size raw binaries with a pad size of 2 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values (`262144 = 64**3`). Together with the 64 values from the 2-character code table above (selector `0`), there are 262,208 type codes for fixed-size raw binary primitives with a pad size of 2.

## Small Variable Raw Size Tables
Although many primitives have fixed raw binary sizes, especially those for modern cryptographic suites such as keys, signatures, and digests, there are other primitives that benefit from variable sizings such as either encrypted material or legacy cryptographic material types found in the GPG or OpenSSL libraries (like RSA). Furthermore, CESR is meant to support not merely cryptographic material types but other basic types, such as generic text strings and numbers. These basic non-cryptographic types may also benefit from variable-size codes.

The three tables in this group are for small variable raw-size primitives. These three tables use 0, 1, or 2 lead bytes as appropriate given the pad size of 0, 1, or 2 for a given variable size raw binary value. The text code size for all three tables is 4 characters.
The first character is the selector, the second character is the type, and the last two characters provide the size of the value as a Base64 encoded integer. The number of unique type codes in each table is, therefore, 64. A given type code is repeated in each table for the same type so that all that differs between codes of the same type in each table is the number of lead bytes needed to align a given variable length on a twenty-four-bit boundary. To clarify, what is different in each table is the number of lead bytes needed to achieve twenty-four-bit alignment for a given variable length. Thus, the selector not only encodes for which type table but also implicitly encodes the number of lead bytes. The variable size is measured in quadlets of 4 characters each in the *T* domain and equivalently in triplets of 3 bytes each in the *B* domain. Thus computing the number of characters when parsing or off-loading in the *T* domain means multiplying the variable size by 4. Computing the number of bytes when parsing or off-loading in the *B* domain means multiplying the variable size by 3. The two Base64 size characters provide value lengths in quadlets/triplets from 0 to 4095 (`64**2 -1`). This corresponds to value lengths of up to 16,380 characters (`4095 • 4`) or 12,285 bytes (`4095 • 3`).

### Small Variable Raw Size Table With 0 Lead Bytes
This table uses `4` as its first character or selector. The second character provides the type. The final two characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 0 are encoded with this table. The 1-character type code provides a total of 64 unique type code values. The maximum length of the value provided by the 2 size characters is 4095 quadlets of characters in the *T* domain and triplets of bytes in the *B* domain. All are raw binary primitives with a pad size of 0 that each includes 0 lead bytes.

### Small Variable Raw Size Table With 1 Lead Byte
This table uses `5` as its first character or selector. The second character provides the type. The final two characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 1 are encoded with this table. The 1-character type code provides a total of 64 unique type code values. The maximum length of the value provided by the 2 size characters is 4095 quadlets of characters in the *T* domain and triplets of bytes in the *B* domain. All are raw binary primitives with a pad size of 1 that each includes 1 lead byte.

### Small Variable Raw Size Table With 2 Lead Bytes
This table uses `6` as its first character or selector. The second character provides the type. The final two characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 0 are encoded with this table. The 1-character type code provides a total of 64 unique type code values. The maximum length of the value provided by the 2 size characters is 4095 quadlets of characters in the *T* domain and triplets of bytes in the *B* domain. All are raw binary primitives with a pad size of 2 that each includes 2 lead bytes.

## Large Variable Raw Size Tables
Many legacy cryptographic libraries such as OpenSSL and GPG support any variable-sized primitive for keys, signatures, and digests such as RSA. Although this approach is often criticized for providing too much flexibility, many legacy applications depend on this degree of flexibility. Consequently, these large variable raw size tables provide a sufficiently expansive set of tables with enough types and sizes to accommodate all the legacy cryptographic libraries as well as all the variable-sized non-cryptographic raw primitive types for the foreseeable future.

The three tables in this group are for large variable raw size primitives. These three large variable raw size tables use 0, 1, or 2 lead bytes as appropriate for the associated pad size of 0, 1, or 2 for a given variable-sized raw binary value. The text code size for all three tables is 8 characters. As a special case, the first 62 entries in these tables represent that same crypto suite type as the 62 entries in the small variable raw size tables above. This allows one type to use a smaller 4-character text code when the raw size is small enough.

The first character is the selector, the next three characters provide the type, and the last four characters provide the size of the value as a Base64 encoded integer. With 3 characters for each unique type code, each table provides 262,144 unique type codes. This should be enough type codes to accommodate all fixed raw size primitive types for the foreseeable future.  A given type code is repeated in each table for the same type. What is different for each table is the number of lead bytes needed to align a given length on a twenty-four bit boundary. The selector not only encodes the table but also implicitly encodes the number of lead bytes. The variable size is measured in quadlets of 4 characters each in the *T* domain and equivalently in triplets of 3 bytes each in the *B* domain. Thus computing the number of characters when parsing or off-loading in the *T* domain means multiplying the variable size by 4. Likewise computing the number of bytes when parsing or off-loading in the *B* domain means multiplying the variable size by 3. The four Base64 size characters provide value lengths in quadlets/triplets from 0 to 16,777,215 (`64**4 -1`). This corresponds to value lengths of up to 67,108,860 characters (`16777215 • 4`) or 50,331,645 bytes (`16777215 • 3`).


### Large Variable Raw Size Table With 0 Lead Bytes
This table uses `7` as its first character or selector. The next three characters provide the type. The final four characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 0 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values. The maximum length of the value provided by the 4 size characters is 16,777,215 quadlets of characters in the *T* domain and triplets of bytes in the *B* domain. All are raw binary primitives with pad size of 0 that each includes 0 lead bytes.

### Large Variable Raw Size Table With 1 Lead Byte
This table uses `8` as its first character or selector. The next three characters provide the type. The final four characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 1 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values. The maximum length of the value provided by the 4 size characters is 16,777,215 quadlets of characters in the *T* domain and triplets of bytes in the *B* domain. All are raw binary primitives with a pad size of 1 that each includes 1 lead byte.

### Large Variable Raw Size Table With 2 Lead Bytes
This table uses `9` as its first character or selector. The next three characters provide the type. The final four characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 2 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values. The maximum length of the value provided by the 4 size characters is 16,777,215 quadlets of characters in the *T* domain and triplets of bytes in the *B* domain. All are raw binary primitives with a pad size of 2 that each includes 2 lead bytes.

## Count (Framing) Code Tables

There may be as many at 13 count code tables, but only three are currently specified. These three are the small count, four-character table, the large count, eight-character table, and the eight character protocol genus and version table. Because count codes only count quadlets/triplets or the number of primitives or groups of primitives, count codes have no value component but have only type and size components. Because primitives are already guaranteed to be composable, count codes do not need to account for pad size as long as the count code itself is aligned on a 24-bit boundary. The count code type indicates the type of primitive or group being counted and the size indicates either how many of that type are in the group or the number of quadlets/triplets consumed by that group. Both count code tables use the first two characters as a nested set of selectors. The first selector uses`-` as the initial selector for count codes. The next character is either a selector for another count code table or is the type for the small count code table. When the second character is numeral `0` - `9` or the letters `-` or `_` then it is a secondary count code table selector. When the second character is a letter in the range `A` - `Z` or `a` - `z` then it is a unique count code type. This gives a total of 52 single-character count code types.

### Small Count Code Table
Codes in the small count code table are each four characters long. The first character is the selector `-`. The second character is the count code type. the last two characters are the count size as a Base64 encoded integer. The count code type MUST be a letter `A` - `Z` or `a` - `z`. If the second character is not a letter but is a numeral `0` - `9` or `-` or `_` then it is a selector for a different count code table. The set of letters provides 52 unique count codes. A two-character size provides counts from 0 to 4095 (`64**2 - 1`).

### Large Count Code Table
Codes in the large count code table are each 8 characters long. The first two characters are the selectors `-`0. The next two characters are the count code type. the last four characters are the count size as a Base64 encoded integer. With two characters for type, there are 4096 unique large-count code types. A four-character size provides counts from 0 to 16,777,215 (`64**4 - 1`).

## Protocol Genus and Version Table
The protocol genus/version table is special because its codes modify the following count code group. A protocol genus and version code itself does not provide a count of the following quadlets or triplets but modifies the protocol genus and version of all the following count codes until another protocol and genus count code is provided. Consequently, a protocol genus and version code MUST only appear at the top level of any count group. In other words, a protocol genus and version code MUST NOT be nested inside any other count code.

The purpose of this table is twofold. Firstly it allows CESR to be used for different protocols and protocol stacks, where each protocol may have its own dedicated set of code tables. The only table that all protocols must share is the protocol genus and version table (protocol table for short) in the set count code tables. All other entries in all other tables may vary by protocol. Secondly, for a given protocol genus, a protocol genus and version code provides the version of that given protocol's table set. This allows versioning of the CESR code tables for a given protocol.

The format for a protocol genus and version code is as follows: `--GGGVVV` where `GGG` represents the protocol genus and `VVV` is the version of that protocol genus. The genus uses three Base64 characters for a possible total of 262,144 different protocol genera. The version also uses three Base64 characters which can be used in a protocol genus-specific manner. One suggested approach is to use one character for each of the three components of a semantic version of the form `major.minor.patch` {{SEMVER}}. This provides 64 major versions, where each major version has up to 64 minor versions, and where each minor version in turn, has up to 64 patches.

## OpCode Tables
The `_` selector is reserved for the yet-to-be-defined opcode table or tables. Opcodes are meant to provide stream processing instructions that are more general and flexible than simply concatenated primitives or groups of primitives. A yet-to-be-determined stack-based virtual machine could be executed using a set of opcodes that provides primitive, primitive group, or stream processing instructions. This would enable highly customizable uses for CESR.

## Selector Codes and Encoding Schemes

### Encoding Scheme Table

The following table summarizes the *T* domain coding schemes by selector code for the 13 code tables defined above.

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


### Encoding Scheme Symbols
The following table defines the meaning of the symbols used in the encoding scheme table

|  Symbol |  Description  |
|:---:|:----:|
|   |   |
| `*` | selector-code character also provides the type |
| `$` | type-code character from  subset of Base64  `[A-Z,a-z,0-9,-,_]` |
| `%` | lead byte where pre-converted binary includes the number of lead bytes shown |
| `#` | Base64 digit as part of a base 64 integer. When part of primitive determines the number of following quadlets or triplets. When part of a count code determines the count of the following primitives or groups of primitives |
| `&` | Base64 value characters that represent the converted raw binary value.  The actual number of characters is determined by the prepended text code.  Shown is the minimum number of value characters. |
| `TBD` | to be determined, reserved for future  |



## Parse Tables

Text domain parsing can be simplified by using a parse size table. A text domain parser uses the first character selector code to look up the hard-size (stable) portion of the text code. The parse then extracts hard-size characters from the text stream. These characters form an index for the parse size table, which includes a set of sizes for the remainder of the primitive. Using these sizes for a given code allows a parser to extract and convert a given primitive. In the binary domain, the same text parse table may be used, but each size value represents a multiple of a sextet of bits instead of Base64 characters. Example entries from that table are provided below. Two of the rows may always be calculated given the other 4 rows, so the table need only have 4 entries in each row. Thus all basic primitives may be parsed with one parse size table.

### Selector Code Size Table

|  selector |  hs  |
|:---------:|:----:|
|           |      |
|       `B` |   1  |
|       `0` |   2  |
|       `5` |   2  |
|           |      |

### Parse Size Table

Below is a snippet from the Parse Size Table for some example codes

| hard sized index |  hs  |  ss  |  vs  |  fs  |  ls  |  ps  |
|:---------:|:----:|:----:|:----:|:----:|:----:|:----:|
|           |      |      |      |      |      |      |
|       `B` |   1  |   0  |  43\* |  44  |  0   |  1   |
|      `0B` |   2  |   0  |  86\* |  88  |  0   |  2\*  |
|      `5A` |   2  |   2  |  \#   |   \#  |  1   |  1\*  |
|           |      |      |      |      |      |      |

### Parse Table Symbols

|  Symbol |  Description  |
|:---:|:----:|
|   |   |
| `*` | entry's size may be calculated from other sizes |
| `#` | entry's size may be calculated from extracted code characters given by other sizes |
|   |      |


### Parse Part Sizes

The following table includes both labels of parts shown in the columns in the Parse Size table as well as parts that may be derived from the Parse Table parts or from transformations,

|  Label |  Description  |
|:----:|:----:|
| ***hs*** | hard size in chars (fixed) part of code size   |
| ***ss*** | soft size in chars, (variable) part of code size  |
| ***os*** | other size in chars, when soft part includes two variable values   |
| *ms* | main size in chars, (derived) when soft part provides two variable values where *ms = ss - os*  |
| *cs* | code size in chars (derived value), where where *cs = hs + ss*  |
| ***vs*** | value size in chars |
| ***fs*** | full size in chars where *fs = hs + ss + vs* |
| ***ls*** | lead size in bytes to pre-pad raw binary bytes |
| ***ps*** | pad size in chars Base64 encoded |
| *rs* | raw size in bytes (derived) of binary value where *rs is derived from `R(T)` |
| *bs* | binary size in bytes (derived) where where *bs = ls + rs* |


## Special Context-Specific Code Tables

The set of tables above provides the basic or master encoding schemes. These coding schemes constitute the basic or master set of code tables. This basic or master set, however, may be extended with context-specific code tables. The context in which a primitive occurs may provide an additional implicit selector that is not part of the actual explicit text code. This allows context-specific coding schemes that would otherwise conflict with the basic or master encoding schemes and tables.

### Indexed Codes

Currently, there is only one context-specific coding scheme, that is, for indexed signatures. A common use case is thresholded multi-signature schemes. A threshold-satisficing subset of signatures belonging to an ordered set or list of public keys may be provided as part of a stream of primitives. One way to compactly associated each signature with its public key is to include in the text code for that signature the index into the ordered set of public keys.

A popular signature raw binary size is 64 bytes which has a pad size of 2. This gives two code characters for a compact text code. The first character is the selector and type code. The second character is the Base64 encoded integer index.  By using a similar dual selector type code character scheme as above, where the selectors are the numbers `0-9` and `-` and `_`. Then there are 52 type codes given by the letters `A- Z` and `a-z`. The index has 64 values which support up to 64 members in the public key list. A selector can be used to select a large text code with more characters dedicated to larger indices. Some applications of CESR, like KERI, have the need for dual-indexed signatures (i.e., each signature has two indices) in order to support pre-rotation with partial or reserved participants in a rotation. With partial rotation, a given signature may contribute to the signing threshold for two different thresholds, each on two different lists of keys where the associated key may appear at a different location in each list. For 64-byte signatures, the Ed25519 and ECDSA secp256k1 schemes have entries in the table. For dual-indexed codes, the next larger code size that aligns a 64-byte signature on a 24-bit boundary is 6 characters. The table provides entries for dual-indexed 64-byte signatures. The code includes one selector character, one type character, and two each of two character indices.

A new signature scheme based on Ed448 with 114-byte signatures is also supported. These signatures have a pad size of zero so require a four-character text code. The first character is the selector `0`, the second character is the type with 64 values, and the last two characters each provide a one-character index as a Base64 encoded integer with 64 different values. A big version code consumes eight characters with one character for the selector, one for the type, and three characters for each of the dual indices.

#### Indexed Code Table

The associated indexed schemes are provided in the following table.

|  Selector | Type Chars | Index Chars | Ondex Chars | Code Size | Lead Bytes | Pad Size |     Format    |
|:---------:|:----------:|:-----------:|:-----------:|:---------:|:----------:|:--------:|--------------:|
|           |            |             |             |           |            |          |               |
|`[A-Z,a-z]`|  `1*`      |     1       |      0      |      2    |      0     |      2   |         `$#&&`|
|     `0`   |   1        |     1       |      1      |      4    |      0     |      0   |     `0$##&&&&`|
|     `2`   |   1        |     2       |      2      |      6    |      0     |      2   |   `0$####&&&&`|
|     `3`   |   1        |     3       |      3      |      6    |      0     |      0   | `0$######&&&&`|



#### Encoding Scheme Symbols

The following table defines the meaning of the symbols used in the Indexed Code table

|  Symbol |  Description  |
|:---:|:----:|
|   |   |
| `*` | selector-code character also provides the type |
| `$` | type-code character from  subset of Base64  `[A-Z,a-z,0-9,-,_]` |
| `%` | lead byte where pre-converted binary includes the number of lead bytes shown |
| `#` | Base64 digit as part of a base 64 integer. When part of primitive determines the number of following quadlets or triplets. When part of a count code determines the count of following primitives or groups of primitives |
| `&` | Base64 value characters that represent the converted raw binary value.  The actual number of characters is determined by the prepended text code.  Shown is the minimum number of value characters. |
| `TBD` | to be determined, reserved for future  |



The appendix contains the master code table with the concrete codes.

# Appendix: KERI Protocol Stack Code Tables

## Code Table Entry Policy

The policy for placing entries into the tables in general is in order of first needed first-entered basis. In addition, the compact code tables prioritize  entries that satisfy the requirement that the associated cryptographic operations maintain at least 128 bits of cryptographic strength. This precludes the entry of many weak cryptographic suites into the compact tables. CESR's compact code table includes only best-of-class cryptographic operations along with common non-cryptograpic primitive types. At the time of this writing, there is the expectaion that NIST will soon approve standardized post-quantum resistant cryptographic operations. When that happens, codes for the most appropriate post-quantum operations will be added. For example, Falcon appears to be one of the leading candidates with open source code already available.


## Master Code Table
This master table includes both the primitive and count code types. The types are separated by headers. The table has 5 columns. These are as follows:

1) The Base64 stable (hard) text code itself.
2) A description of what is encoded or appended to the code.
3) The length in characters of the code.
4) the length in characters of the count portion of the code
5) The length in characters of the fully qualified primitive, including code and its appended material or the number of elements in the group. This is empty when variable length.



|   Code     | Description                       | Code Length | Count Length | Total Length |
|:----------:|:----------------------------------|:-----------:|:------------:|:------------:|
|            |    **Basic One Character Codes**  |             |              |              |
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
|            |  **Basic Two Character Codes**    |             |              |              |
|    `0A`    | Random salt, seed, private key, or sequence number of length 128 bits |      2      |              |      24      |
|    `0B`    | Ed25519 signature                 |      2      |              |      88      |
|    `0C`    | ECDSA secp256k1 signature         |      2      |              |      88      |
|    `0D`    | Blake3-512 Digest                 |      2      |              |      88      |
|    `0E`    | Blake2b-512 Digest                |      2      |              |      88      |
|    `0F`    | SHA3-512 Digest                   |      2      |              |      88      |
|    `0G`    | SHA2-512 Digest                   |      2      |              |      88      |
|    `0H`    | Long value of length 32 bits      |      2      |              |       8      |
|            |  **Basic Four Character Codes**   |             |              |              |
|   `1AAA`   | ECDSA secp256k1 non-transferable prefix public verification key   |      4      |              |      48      |
|   `1AAB`   | ECDSA secp256k1 public verification or encryption key |      4      |              |      48      |
|   `1AAC`   | Ed448 non-transferable prefix public verification key |      4      |              |      80      |
|   `1AAD`   | Ed448 public verification key     |      4      |              |      80      |
|   `1AAE`   | Ed448 signature                   |      4      |              |      156     |
|   `1AAF`   | Tag Base64 4 chars or 3 byte number  |      4      |              |      8       |
|   `1AAG`   | DateTime Base64 custom encoded 32 char ISO-8601 DateTime |      4      |              |      36      |
|   `1AAH`   | X25519 100 char b64 Cipher of 24 char qb64 Salt |      4      |              |      100      |
|            |  **Small Variable Raw Size Codes**   |             |              |              |
|   `4A`     | String Base64 Only Lead Size 0      |      4      |      2        |            |
|   `5A`     | String Base64 Only Lead Size 1      |      4      |      2        |            |
|   `6A`     | String Base64 Only Lead Size 2      |      4      |      2        |            |
|   `4B`     | Bytes Lead Size 0                  |      4      |      2        |            |
|   `5B`     | Bytes Lead Size 1                  |      4      |      2        |            |
|   `6B`     | Bytes Lead Size 2                  |      4      |      2        |            |
|            |  **Large Variable Raw Size Codes**   |             |              |              |
|   `7AAA`   | String Big Base64 Only Lead Size 0 |      8      |      4        |            |
|   `8AAA`   | String Big Base64 Only Lead Size 1 |      8      |      4        |            |
|   `7AAA`   | String Big Base64 Only Lead Size 2 |      8      |      4        |            |
|   `7AAB`   | Bytes Big Lead Size 0              |      8      |      4        |            |
|   `8AAB`   | Bytes Big Lead Size 1              |      8      |      4        |            |
|   `9AAB`   | Bytes Big Lead Size 2              |      8      |      4        |            |
|            |  **Counter Four Character Codes** |             |              |              |
|   `-A##`   | Count of attached qualified Base64 indexed controller signatures |      4      |       2      |       4      |
|   `-B##`   | Count of attached qualified Base64 indexed witness signatures    |      4      |       2      |       4      |
|   `-C##`   | Count of attached qualified Base64 nontransferable identifier receipt couples  pre+sig |      4      |       2      |       4      |
|   `-D##`   | Count of attached qualified Base64 transferable identifier receipt quadruples  pre+snu+dig+sig   |      4      |       2      |       4      |
|   `-E##`   | Count of attached qualified Base64 first seen replay couples fn+dt |      4      |       2      |       4      |
|   `-F##`   | Count of attached qualified Base64 transferable indexed sig groups pre+snu+dig + idx sig group|      4      |       2      |       4      |
|   `-V##`   | Count of total attached grouped material qualified Base64 4 char quadlets |      4      |       2      |       4      |
|            |  **Counter Eight Character Codes** |             |              |              |
| `-0V#####` | Count of total attached grouped material qualified Base64 4 char quadlets |      8      |       5      |       8      |
|            |    **Protocol Genus Version Codes**    |             |              |             |
|`--AAA###`  | KERI ACDC protocol stack  version      |      8      |       5      |       8     |



## Indexed Code Table

|   Code    | Description                            | Code Length | Index Length | Ondex Length | Total Length |
|:-------- :|:---------------------------------------|:-----------:|:------------:|:------------:|:------------:|
|           |    **Indexed Two Character Codes**     |             |              |              |              |
|    `A#`   | Ed25519 indexed signature both same    |      2      |       1      |      0       |      88      |
|    `B#`   | Ed25519 indexed signature current only |      2      |       1      |      0       |      88      |
|    `C#`   | ECDSA secp256k1 indexed sig both same  |      2      |       1      |      0       |      88      |
|    `D#`   | ECDSA secp256k1 indexed sig curr only  |      2      |       1      |      0       |      88      |
|           |    **Indexed Four Character Codes**    |             |              |              |              |
|   `0A##`  | Ed448 indexed signature  dual          |      4      |       1      |       1      |      156     |
|   `0B##`  | Ed448 indexed signature current only   |      4      |       1      |       1      |      156     |
|           |    **Indexed Six Character Codes**     |             |              |              |              |
| `2A####`  | Ed25519 indexed sig big dual           |      6      |       2      |       2      |      92      |
| `2B####`  | Ed25519 indexed sig big current only   |      6      |       2      |       2      |      92      |
| `2C####`  | ECDSA secp256k1 indexed sig big dual   |      6      |       2      |       2      |      92      |
| `2D####`  | ECDSA secp256k1 idx sig big curr only  |      6      |       2      |       2      |      92      |
|           |    **Indexed Eight Character Codes**   |             |              |              |              |
|`3A######` | Ed448 indexed signature  big dual      |      8      |       3      |       3      |      160     |
|`3B######` | Ed448 indexed signature  big curr only |      8      |       3      |       3      |      160     |



### Examples
The tables above include complex groups that maybe composed of other groups. For example, consider the counter attachment group
with code `-F##` where `##` is replaced by the two-character Base64 count of the number of complex groups.
This is known as the TransIndexedSigGroups counter.  Within the complex group are one or more attached
groups where each group consists of a triple pre+snu+dig
followed by a ControllerIdxSigs group that in turn, consists of a counter code `-A##` followed by one or more
indexed signature primitives.
The following example details how a complex nested group may appear.

The example has only one group that includes nested groups. The example is annotated with comments, spaces, and line feeds for clarity.

~~~text
-FAB     # Trans Indexed Sig Groups counter code 1 following group
E_T2_p83_gRSuAYvGhqV3S0JzYEF2dIa-OCPLbIhBO7Y    # trans prefix of signer for sigs
-EAB0AAAAAAAAAAAAAAAAAAAAAAB    # sequence number of est event of signer's public keys for sigs
EwmQtlcszNoEIDfqD-Zih3N6o5B3humRKvBBln2juTEM      # digest of est event of signer's public keys for sigs
-AAD     # Controller Indexed Sigs counter code 3 following sigs
AA5267UlFg1jHee4Dauht77SzGl8WUC_0oimYG5If3SdIOSzWM8Qs9SFajAilQcozXJVnbkY5stG_K4NbKdNB4AQ         # sig 0
ABBgeqntZW3Gu4HL0h3odYz6LaZ_SMfmITL-Btoq_7OZFe3L16jmOe49Ur108wH7mnBaq2E_0U0N0c5vgrJtDpAQ    # sig 1
ACTD7NDX93ZGTkZBBuSeSGsAQ7u0hngpNTZTK_Um7rUZGnLRNJvo5oOnnC1J2iBQHuxoq8PyjdT3BHS2LiPrs2Cg  # sig 2

ToDo more examples



~~~


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Security Considerations

TODO Security

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

The keripy development team, the KERI community, and the ToIP ACDC working group.
