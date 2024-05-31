## Text coding scheme design
<!-- file name: text-coding-scheme-design.md -->

### Text code size

Recall that the ‘R’ domain representation is a pair`(text code, raw binary)`. The text code is Stable and begins with one or more Base64 characters that provide the Primitive type and may also include one or more additional characters that provide the length. The actual usable cryptographic material is provided by the raw binary element.

The corresponding ‘T’ domain representation of this pair is created by first prepending leading pad bytes of zeros to the raw binary element. This result is then converted to Base64. Depending on the code, either the frontmost characters that result from the Base64 conversion of leading pad bytes of zeros are replaced with the text code element of appropriate size in characters, or an appropriately sized text code element is prepended to the conversion without replacing any characters.

Recall that when the length of a given naive binary string is not an integer multiple of three bytes, standard Base64 conversion software appends one or two pad characters to the resultant Base64 conversion.

With standard Base64 conversion that employs pad characters, the Text domain representation that results from the individual conversion of a set of binary strings when concatenated in the Text domain after conversion and stripping off pad characters is not necessarily equivalent to the Text domain representation that results from converting en-masse to text the concatenation of the same set of binary strings and then stripping off pad characters. In the latter case, knowledge of the set of binary strings is lost because the resultant conversion may have bits from two binary bytes concatenated in one text character. Restated, the problem with standard Base64 is that it does not preserve byte boundaries after the en-masse conversion of concatenated binary strings. Consequently, standard (naive) Base64 does not provide two-way or true Composability as defined above.

To elaborate, the number of pad characters appended with standard Base64 encoding is a function of the length of the binary string. Let `N` be the length in bytes of the binary string. When `N mod 3 = 1`, then there are 8 bits in the remainder that must be encoded into Base64. Recall from the examples above that a single byte (8 bits) requires two Base64 characters. The first encodes 6 bits and the second the remaining 2 bits for a total of 8 bits. The last character is selected such that its non-coding 4 bits are zero. Thus, two additional pad characters are required to pad out the resulting conversion so that its length is an integer multiple of 4 Base64 characters. Furthermore, when `N mod 3 = 1`, then 2 more zeroed bytes added to the length of the binary string such that `M = N + 2` would result in `M mod 3 = 0` or equivalently `N + 2 mod 3 = 0`.

Similarly, when `N mod 3 = 2`, then there are two bytes (16 bits) in the remainder that must be encoded into Base64. Recall from the examples above that two bytes (16 bits) require three Base64 characters. The first two encode 6 bits each (for 12 bits), and the third encodes the remaining 4 bits for a total of 16. The last character is selected such that its non-coding 2 bits are zero. Thus, one additional trailing pad character is required to pad out the resulting conversion so that its length is an integer multiple of 4 characters. Furthermore, when `N mod 3 = 2`, then the addition of 1 more byte of zeros added to the length of the binary string such that `M = N + 1` would result in `M mod 3 = 0` or equivalently `N + 2 mod 3 = 0`. Thus, the number of leading pre-pad zeroed bytes needed to align the binary string on a 24-bit boundary is the same as the number of trailing pad characters needed to align the converted Base64 text string on a 24-bit boundary.

Finally, when `N mod 3 = 0`, then the binary string is already aligned on a 24-bit boundary and no trailing pad characters are required to ensure the length of the Base64 conversion is an integer multiple of 4 characters. Likewise, no leading pad bytes are required to ensure the length of the binary string is an integer multiple of 3 bytes.

Thus, in all three cases, the number of trailing post-pad characters, if any, needed to align the converted Base64 text string on a 24-bit boundary is the same as the number of leading pre-pad bytes, if any, needed to align the binary string on a 24-bit boundary.

The number of required trailing Base64 post-pad characters or, equivalently the number of leading pre-pad zeroed bytes to ensure 24-bit alignment may be computed with the following formula:

`ps = (3 - (N mod 3)) mod 3)`, where `ps` is the pad size (pre-pad bytes or post-pad characters) and `N` is the size in bytes of the binary string.

Recall that Composability is provided here by prepending text codes that are of the appropriate length to ensure 24-bit boundaries in both the ‘T’ and the corresponding ‘B’ domain. The advantage of this approach is that naive Base64 software tooling may be used to convert back and forth between the ‘T’ and ‘B’ domains, i.e., `T(B)` is naive Base64 encode, and `B(T)` is naive Base64 decode. In other words, CESR Primitives are compatible with existing Base64 (RFC-4648) tooling. Whereas new software tooling is needed for conversions between the ‘R’ and ‘T’ domains, e.g., `T(R)` and `R(T)` and the ‘R’ and ‘B’ domains, e.g., `B(R)` and `R(B)`.

The pad size computation is also useful for computing the size of the text codes. Because true Composability also requires that the ‘T’ domain value must be an integer multiple of 4 characters in length, the size of the text code also must be a function of the pad size, `ps`, and hence the length of the raw binary element, `N`. Thus, the size of the text code in Base64 characters is a function of the equivalent pad size determined by the length `N mod 3` of the raw binary value. 

#### Example of pad size computation

Let `M` be a non-negative integer-valued variable then:

|   Pad Size   | Code Size |
|:------------:|------------:|
|0|4•M|
|1|4•M + 1|
|2|4•M + 2|

The minimum code sizes are 4, 1, and 2 characters for pad sizes of 0, 1, and 2 characters with minimum `M` equaling 1, 0, and 0, respectively. By increasing `M`, there are larger code sizes for a given pad size.

### Pre-padding before conversion

Returning to the examples above, observe what happens when the binary strings are pre-padded with zeroed leading pad bytes of the appropriate length given by `ps = (3 - (N mod 3)) mod 3)` where `ps` is the number of leading pad bytes and `N` is the length of the raw binary string before padding is prepended.

#### Pre-padding a one-byte value

For the one-byte raw binary string `a`, `ps` is two. The pre-padded conversion results in the following:

```text
|           Z1          |           Z0          |           A0          |
|z7:z6:z5:z4:z3:z2:z1:z0|z7:z6:z5:z4:z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
|        T3       |        T2       |        T1       |        T0       |
```
where `ZX` represents a zeroed pre-pad byte, `zX` represents a zeroed pre-pad bit, `AX` represents a byte from `a`, `aX` represents a bit from `a`, and `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `a`.

It is noteworthy that the first two (i.e., `ps`) characters of the conversion, namely `T3T2`, do not include any bits of information from `a`. This also means that `T3T2` can be modified after conversion without impacting the appearance or value of the converted `a` that appears solely in `T1T0`, i.e., there is no overlap. Moreover, the resulting Base64 conversion of `a` is right aligned with respect to the trailing Base64 character. This means that the numerical values for `a` from such an unshifted Base64 conversion can be ‘read’ and understood.  This also means that a text-based parser on a character-by-character basis can cleanly process `T3T2` separate from the Base64 encoding of `a` that appears in `T1T0`. Given this separation, `T3T2` can be replaced with two-character Base64 textual type code `S1S0` as follows:

```text
|           Z1          |           Z0          |           A0          |
|z7:z6:z5:z4:z3:z2:z1:z0|z7:z6:z5:z4:z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
|        S1       |        S0       |        T1       |        T0       |
|s5:s4:s3:s2:s1:s0|s5:s4|s3:s2:s1:s0|z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
```
where `ZX` represents a zeroed pre-pad byte, `zX` represents a zeroed pre-pad bit, `AX` represents a byte from `a`, `aX` represents a bit from `a`,  `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `a`, `SX` represents a Base64 code character replacing one of the `TX`, and `sX` is a code bit. The resultant four-character Base64 encoded Primitive would be `S1S0T1T0`.

When `S1S0T1T0` is converted back to binary from Base64, the result would be as follows:

```text
|        S1       |        S0       |        T1       |        T0       |
|s5:s4:s3:s2:s1:s0|s5:s4:s3:s2:s1:s0|z3:z2:z1:z0|a7:a6:a5:a4:a3:a2:a1:a0|
|           U1          |           U0          |           A0          |
```

where `CX` represents a Base64 code character replacing one of the `TX`, `cX` is a code bit, `UX` represents a byte from the converted code char, which may include zeroed bits, `zX` represents a zeroed pre-pad bit, `AX` represents a byte from `a`, `aX` represents a bit from `a`, and `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `a`.

Stripping off `U1U0` leaves `a` in its original state. It is noteworthy that the code characters (only) are effectively left shifted 4 bits after conversion. The code characters `S1S0` can be recovered as the first two characters that are obtained from simply converting `U1U0` only back to Base64.

#### Pre-padding a two-byte value

For the two-byte raw binary string `b`, `ps` is one. The resultant four-character Base64 encoded Primitive would be `S0T2T1T0`.

When `S0T2T1T0` is converted back to binary from Base64, the result would be as follows:

```text
|        S0       |        T2       |        T1       |        T0       |
|s5:s4:s3:s2:s1:s0|z1:z0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|           U0          |           B1          |           B0          |
```
where `SX` represents a Base64 code character replacing one of the `TX`, `sX` is a code bit, `UX` represents byte from converted code char which may include zeroed bits, `zX` represents a zeroed pre-pad bit, `BX` represents a byte from `b`, `bX` represents a bit from `b`, and `TX` represents a Base64 character that results from the Base64 conversion of the pre-padded `b`.

Stripping off `U0` leaves `b` in its original state. It is noteworthy is that the code character (only) is effectively left shifted 2 bits after conversion. The code character `S0` can be recovered as the first character obtained from simply converting `U0` only to Base64.

#### Pre-padding a three-byte value

For the three-byte raw binary string `c`, `ps` is zero. So pre-padding is not needed.
```text
|        T3       |        T2       |        T1       |        T0       |
|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|
|           C2          |           C1          |           C2          |
```
where `cX` represents a bit from `c`, `CX` represents a byte from `c`, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string. There are no bit shifts because there are no pad bits nor pad characters needed, and the resulting Base64 conversion is right aligned with respect to the trailing Base64 character.

Without pad characters, however, there is no room to hold a type code. Consequently, any text type code is just prepended to the conversion. The prepended type code must be an integer multiple of four Base64 characters. Let `S3S2S1S0` be the type code, then the full Primitive with code and converted raw binary is given by the eight-character Base64 string `S3S2S1S0T3T2T1T0`.

When `S3S2S1S0T3T2T1T0` is converted back to binary, there is no overlap or bit shifting because both the code and raw binary `c` are each separately aligned on twenty-four-bit boundaries.

#### Examples of pre-padding

Suppose that two-byte raw binary numbers are to be encoded into CESR using the pre-pad approach described above. In order to achieve 24-bit alignment, the pre-pad size for two-byte numbers is 1 byte. As described above, this means the minimally sized text code is 1 Base64 character. Suppose that the text code is `M` (Base64).  The following table provides examples of encoding the different two-byte raw binary values in the three Domains: Raw, Text, and Binary. Recall that the Raw domain is expressed by a tuple of (code, raw) where the code is Base64 text and the raw is the raw binary value without code. For readability, raw binary values are represented in hexadecimal notation.

|  Raw |  Text  |  Binary |
|------------:|------------:|------------:|
| ("M", 0x0000) | "MAAA" | 0x300000 |
| ("M", 0x0001) | "MAAB" | 0x300001 |
| ("M", 0xffff) | "MP__" | 0x30ffff |

With this approach, both the Binary domain and Text domain representations are as compact as possible for a fully qualified Primitive that satisfies the Composability property. The Text domain representation has a Stable readable code and a Stable readable value. The Binary domain is value right aligned. The Text domain representation consists of 4 text printable characters from the Base64 set of characters, and the Binary domain representation consists of 3 bytes. Both can be parsed in each Domain along character/byte boundaries, respectively. A parser reads the first character/byte and then processes that value to get an index into a lookup table that it uses to find how many remaining characters/bytes to extract from the Stream. This makes the Primitive self-framing.

### Count or Group Framing Codes

As mentioned above, one of the primary advantages of composable encoding is that special Framing Codes can be specified to support groups of Primitives. Grouping enables pipelining. Other suitable terms for these special Framing Codes are Group Codes or Count Codes for short. These are suitable terms because these Framing Codes can be used to count characters or bytes in a group of Primitives when parsing and offloading a Stream of CESR Primitives. A group of Primitives may be recursively composed into a group of groups.

A Count Code is its own composable Primitive, and its length, therefore, shall be an integer multiple of four characters in the Text domain or, equivalently, an integer multiple of three bytes in the Binary domain. To clarify, a Count Code is a special Primitive that does not include a raw binary value, only its text code. Because a Count Code's raw binary element value is empty and its length is an integer multiple of four characters (three bytes), its pad size is always 0.

To elaborate, Count Codes can be used as separators to better organize a Stream of Primitives or to interleave non-native (non-CESR) serializations. Count Codes enable grouping any combination of Primitives, groups of Primitives, or non-native serializations to optimize pipelining and offloading.

### Interleaved non-CESR serializations

As mentioned above, one extremely useful property of CESR is that special Count Codes enable CESR to be interleaved with other serializations. Many applications use JSON [[spec: RFC4627]] [[spec: RFC4627]], CBOR [[spec: RFC8949]] [[spec: RFC8949]], or MessagePack (MGPK) [[3]] to serialize flexible self-describing data structures based on field maps, also known as dictionaries or hash tables. With respect to field map serializations, CESR Primitives may appear in two different contexts. The first context is as a delimited text Primitive inside of a field map serialization. The delimited text may be either the key or value of a (key, value) pair. The second context is a standalone serialization interleaved with field map serializations in a stream. Special CESR Count Codes enable support for the second context of interleaving standalone CESR with other serializations.

### Cold start Stream parsing problem

After a cold start, a Stream processor looks for framing information to know how to parse groups of elements in the Stream. If that framing information is ambiguous, then the parser may become confused and require yet another cold start. While processing a given Stream, a parser may become confused, especially if a portion of the Stream is malformed in some way. This usually requires flushing the Stream and forcing a cold start to resynchronize the parser to subsequent Stream elements. A re-synchronization mechanism that does not require flushing the in-transit buffers but merely skipping to the next well-defined Stream element boundary in order to execute a cold start is a better option. Good cold start re-synchronization is essential to robust performant Stream processing.

For example, in Transmission Control Protocol (TCP), a cold start usually means closing and then reopening the TCP connection. This flushes the TCP buffers and sends a signal to the other end of the Stream that may be interpreted as a restart or cold start. In the User Datagram Protocol (UDP), each packet is framed individually, but a Stream may be segmented into multiple packets.  So, a cold start may require an explicit ack or nack to force a restart.

Special CESR Count Codes support re-synchronization at each boundary between interleaved CESR and other serializations (like JSON, CBOR, or MGPK).

#### Performant resynchronization with unique start bits

A CESR Stream parser supports three specific interleaved serializations, namely, JSON, CBOR, and MGPK. To make the parser more performant and robust, fine-grained serialization boundary detection may be highly beneficial for interleaving these serializations in a CESR stream. One way to provide this is by selecting the Count Code start bits such that there is always a unique (mutually distinct) set of start bits at each interleaved boundary between CESR, JSON, CBOR, and MGPK.

Furthermore, it may also be highly beneficial to support in-stride switching between interleaved CESR text-domain Streams and CESR Binary domain Streams. In other words, the start bits for Count Codes in both the ‘T’ domain and the ‘B’ domain should be unique. This would provide the analogous equivalent of a UTF Byte Order Mark (BOM) [[4]]. Recall that a BOM enables a parser of UTF-encoded documents to determine if the UTF codes are big-endian or little-endian [[4]]. In the CESR case, an analogous feature would enable a Stream parser to know if a Count Code, along with its associated counted group of Primitives, is expressed in the ‘T’ or ‘B’ domain. Together these impose the constraint that the boundary start bits for interleaved text CESR, binary CESR, JSON, CBOR, and MGPK be mutually distinct.

Among the codes for map objects in JSON, CBOR, and MGPK, only the first three bits are fixed and not dependent on mapping size. 
* In JSON, a serialized mapping object always starts with `{`. This is encoded as `0x7b`. the first three bits are `0b011`. 
* In CBOR, the first three bits of the major type of its serialized mapping object are `0b101` corresponding to the bits denoting map "Major Type 5" from the CBOR spec.
* In MGPK, there are three different mapping object codes. The FixMap code starts with `0b100`. Both the Map16 and Map32 codes start with `0b110`.

Therefore, the JSON, CBOR, and MGPK encodings consume four starting Tritets (3 bits) that are in numeric order `0b011`, `0b100`, `0b101`, and `0b110`. This leaves four unused Tritets, namely, `0b000`, `0b001`, `0b010`, and `0b111`. These latter are potential candidates for the CESR Count Code start bits. In Base64, there are two codes that satisfy the constraints. The first is the dash character, `-`, encoded as `0x2d`. Its first three bits are `0b001`. The second is the underscore character, `_`, encoded as `0x5f`. Its first three bits are `0b010`. Both of these are distinct from the starting Tritets of any of the JSON, CBOR, and MGPK encodings above. Moreover, the starting Tritet of the corresponding binary encodings of `-` and `_` is `0b111`, which is also distinct from all the others. To elaborate, Base64 uses `-` in position 62 or `0x3E` (hex) and uses `_` in position 63 or `0x3F` (hex), both of which have starting Tritet of `0b111`

Consequently, two different Base64 characters, `-` and `_`, can be used for the first character of any Count Code in the ‘T’ domain. This also means there can be two different classes of Count Codes. Using Count Codes in this way also provides a BOM-like capability that enables a parser to determine if the Count Code itself is expressed in either the ‘T’ or ‘B’ domain. To clarify, if a Stream group starts with the Tritet `0b111`, then the Stream frame is ‘B’ domain CESR, and a Stream parser would thereby know how to convert the first sextet of the Stream group to determine which of the two Count Codes is being used, `0x3E` or `0x3F`. If, on the other hand, the Count Code starts with either of the Tritets `0b001` or `0b010`, then the Count Code is expressed in the ‘T’ domain, and a Stream parser likewise would thereby know how to convert the first character (octet) of the Count Code to determine which Count Code is being used for that group. Otherwise, if a Stream starts with `0b011`, then it is JSON. If it starts with `0b101`, then it is CBOR. If it starts with either `0b100` or `0b110`, then it is MGPK.

This is summarized in the following table:

##### Table 1

|   Starting Tritet   | Serialization | Character |
|:------------:|:------------:|:------------:|
|0b000|Unused| |
|0b001|CESR ‘T’ domain Count Code|`-`|
|0b010|CESR ‘T’ domain Op Code|`_`|
|0b011|JSON|`{`|
|0b100|MGPK (FixMap)| |
|0b101|CBOR (Map "Major Type 5")| |
|0b110|MGPK (Map16, Map32)| |
|0b111|CESR ‘B’ domain Count Code or Op Code| |

::: note 
The above table implies a normative requirement that all serializations of MGPK, CBOR, JSON in CESR be top level field maps.  Serializations of these formats that aren't top level field maps are undefined and will most likely lead to a stream that won't decode.
:::

#### Stream parsing rules

Given this set of Tritets (3 bits), a well-formed Stream start and restart requirement can be expressed.

Each Stream must start (restart) with one of seven cases:

1. A Count Code in CESR ‘T’ domain
2. A Count Code in CESR ‘B’ domain.
3. An Op code in the CESR ‘T’ domain
4. An Op code in the CESR ‘B’’ domain
5. A JSON encoded mapping.
6. A CBOR encoded mapping.
7. A MGPK encoded mapping.

A parser merely needs to examine the first Tritet (3 bits) of the first byte of the Stream start to determine which one of the seven it is. When the first Tritet is a Count Code, then the remainder of the Count Code itself will include the additional information needed to parse the attached group. When the first Tritet indicates, its JSON, CBOR, or MGPK, the mapping's first field must be a Version String that provides the additional information needed to parse the associated encoded field map serialization fully. See the Version String annex for the detailed syntax of the information that may be extracted with a regular expression search, given that the first Tritet indicates the following bytes in the stream belong to a JSON, CBOR, or MGPK field map serialization.

The Stream must resume with a frame starting byte that begins with one of the 7 Tritets, either another Count Code expressed in the ‘T’ or ‘B’ domain or a new JSON, CBOR, or MGPK encoded mapping.

This provides an extremely compact and elegant Stream parsing formula that generalizes support not only for CESR Composability but also for interleaved CESR with three of the most popular field map serializations.

### Compact fixed-size codes

As mentioned above, CESR uses a multiple-code table design that enables both size-optimized text codes for the most popular Primitive types and extensible universal support for all other Primitive types. Modern cryptographic suites support limited sets of raw binary Primitives with fixed (not variable) sizes. The design aesthetic is based on the understanding that there is minimally sufficient cryptographic strength and more cryptographic strength just wastes computation and bandwidth. Cryptographic strength is measured in bits of entropy, which also corresponds to the number of trials that must be attempted to succeed in a brute-force attack. The accepted minimum for cryptographic strength is 128 bits of entropy or equivalently `2**128` (2 raised to the 128th power) brute force trials. The size in bytes of a given raw binary Primitive for a given modern cryptographic suite is usually directly related to this minimum strength of 128 bits (16 bytes).

For example, the raw binary Primitives from the well-known [[6]] ECC (Elliptic Curve Cryptography) library all satisfy this 128-bit strength goal. In particular, the digital signing public key raw binary Primitives for EdDSA are 256 bits (32 bytes) in length because well-known algorithms can reduce the number of trials to brute force invert an ECC public key to get the private key by the square root of the number of scalar multiplications which is also related to the size of both the private key and public key coordinates (discrete logarithm problem [[5]]). Therefore, 256-bit (32-byte) ECC keys are needed to achieve 128 bits of cryptographic strength. In general, the size of a given raw binary Primitive is typically some multiple of 128 bits of cryptographic strength. This is also true for the associated EdDSA raw binary signatures which are 512 bits (64 bytes) in length.

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

For all other cryptographic material values whose pad size is 0, such as the 33-byte ECDSA public keys then, the minimum size text code is 4 characters. So, the minimally sized text code tables are 1, 2, and 4 characters, respectively.

Given that a given Cryptographic Primitive type has a known fixed raw binary size, then that Primitive type and size can be encoded efficiently with just the type information. The size is given by the type.

For example, an Ed25519 (EdDSA) raw public key is always 32 bytes, so knowing that the type is `Ed25519 public key` implies the size of 32 bytes and a pad size of 1 character that, therefore, may be encoded with a 1-character text code. Likewise, an Ed25519 (EdDSA) signature is always 64 bytes, so knowing that the type is `Ed25519 signature` implies the size of 64 bytes and a pad size of 2 characters that, therefore, may be encoded with a 2-character text code.

### Code table selectors

In order to parse a Stream of Primitives with types from multiple text code tables efficiently, the first character in the text code must determine which code table to use, either a default code table or a code table selector character when not the default code table. Thus, the 1-character text code table must do double duty. It must provide selectors for the different text code tables and also provide type codes for the most popular Primitives that have a pad size of 1 that appears as the default code table. There are 64 Base64 characters (64 values). Only 12 tables are needed to support all the codes and code formats needed for the foreseeable future. Therefore, only 12 of those characters need to be dedicated as code table selectors, which leaves 52 characters that may be used for the 1-character type codes in the default table. This gives a total of 13 type code tables consisting of the dual purpose 1 character type or selector code table and 12 other tables.

As described above, the selector characters for the Count Code tables that best support interleaved JSON, CBOR, and MGPK are `-` and `_`. The numerals `0` through `9` are used each to serve as a selector for the other tables. That leaves the letters `A` to `Z` and `a` to `z` as single character selectors. This provides 52 unique type codes for fixed-length Primitive types with raw binary values that have a pad size of 1.

To clarify, the first character of any Primitive is either a selector or a 1-character code type. The characters `0` through `9`, `-`, and `_` are selectors that select a given code table and indicate the number of remaining characters in the text code.