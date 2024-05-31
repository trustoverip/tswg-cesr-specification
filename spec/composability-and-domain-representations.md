## Composability and Domain representations
<!-- file name: composability-and-domain-representations.md -->

### Composability

An encoding has Composability when any set of self-framing concatenated Primitives expressed in either the Text domain or Binary domain may be converted as a group to the other Domain and back again without loss. Essentially, Composability provides round-trippable lossless conversion between Text and Binary domain representations of any set of concatenated Primitives when converted as a set not merely individually. The property enables a Stream processor to safely convert en-masse a Stream in the Text domain to an equivalent Stream in the Binary domain for compact transmission that may be safely converted back to Text domain en-masse by a Stream processor at the other end for further processing or archival storage. The use of Count Codes as independently composable groups enables hierarchical compositions. Such a hierarchically composable encoding protocol enables pipelining (multiplexing and de-multiplexing) of complex Streams in either text or compact binary. This allows management at scale for high-bandwidth applications that benefit from core affinity off-loading of Streams [[17]].

### Abstract Domain representations

The Cryptographic Primitives defined in CESR inhabit three different Domains each with a different representation.  The first Domain is called Text for streamable text and is denoted as ‘T’. The second Domain is called Binary for streamable binary and is denoted as ‘B’. Composability is defined between the ‘T’ and ‘B’ domains. The third Domain is called Raw, which is non-streamable binary, and is denoted as ‘R’. The third Domain is special because Primitives in this Domain are represented by a pair or two-tuple of values namely (text code, raw binary) or `(code, raw)` for short. The text code element of the ‘R’ domain pair is a string of one or more text characters that provides the type and size information for the encoded Primitive when in the ‘T’ domain. The raw binary element is composed of bytes. The actual use of Cryptographic Primitives happens in the ‘R’ domain using the raw binary element of the `(code, raw)` pair. Cryptographic Primitive values are usually represented as strings of bytes that represent very large integers. Cryptographic libraries typically assume that the inputs and outputs of their functions will be such strings of bytes. The raw binary element of the ‘R’ domain pair is such a string of bytes. The CESR protocol, however, is not limited to merely encoding Cryptographic Primitives but any primary data type (numbers, text, datetimes, lists, maps) may be encoded in a composable way.

A given Primitive in the ‘T’ domain is denoted with `t`.  A member of an indexed set of Primitives in the ‘T’ domain is denoted with `t[k]`. Likewise, a given Primitive in the ‘B’ domain is denoted with `b`. A member of an indexed set of Primitives in the ‘B’ domain is denoted with `b[k]`. Similarly, a given Primitive in the ‘R’ domain is denoted with `r`. A member of an indexed set of Primitives in the R’ domain is denoted with `r[k]`.

#### Transformations between Domains

Although the Composability property mentioned in the previous section only applies to conversions back and forth between the ‘T’, and ‘B’, domains, conversions between the ‘R’, and ‘T’ domains, as well as conversions between the ‘R’ and ‘B’ domains, also are defined and supported by the protocol as described in detail in this section. As a result, there is a total of six transformations, one in each direction, among the three Domains.

Let `T(B)` denote the abstract transformation function from the ‘B’ domain to the ‘T’ domain. This is the dual of `B(T)` below.

Let `B(T)` denote the abstract transformation function from the ‘T’ domain to the ‘B’ domain. This is the dual of `T(B)` above.

Let `T(R)` denote the abstract transformation function from the ‘R’ domain to the ‘T’ domain. This is the dual of `R(T)` below.

Let `R(T)` denote the abstract transformation function from the ‘T’ domain to the ‘R’ domain. This is the dual of `T(R)` above.

Let `B(R)` denote the abstract transformation function from the ‘R’ domain to the ‘B’ domain. This is the dual of `R(B)` below.

Let `R(B)` denote the abstract transformation function from the ‘B’ domain to the ‘R’ domain. This is the dual of `B(R)` above.

Given these transformations, a complete a circuit of transformations can be completed that starts in any of the three Domains and then crosses over the other two Domains in either direction. 

##### Examples of circuits of transformations

###### Example 1

Starting in the ‘R’ domain, a circuit that crosses into the ‘T' and ‘B’ domains can be traversed and then crossed back into the ‘R’ domain as follows:

```text
R->T(R)->T->B(T)->B->R(B)->R
```

##### Example 2

Likewise, starting in the ‘R’ domain, a circuit that crosses into the ‘B’ and ‘T’ domains and then crossed back into the ‘R’ domain as follows:

```text
R->B(R)->B->T(B)->T->R(T)->R
```

#### Concatenation composability property

Let `+` represent concatenation. Concatenation is associative and may be applied to any two Primitives or any two groups or sets of concatenated Primitives. For example:

```text
t[0] + t[1] + t[2] + t[3] = (t[0] + t[1]) + (t[2] + t[3])
```

If we let `cat(x[k])` denote the concatenation of all elements of a set of indexed Primitives `x[k]` where each element is indexed by a unique value of `k`. Given the indexed representation, the transformation can be expressed between Domains of a concatenated set of Primitives as follows:

Let `T(cat(b[k]))` denote the concrete transformation of a given concatenated set of primitives, `cat(b[k])` from the B domain to the T domain.

Let `B(cat(t[k]))` denote the concrete transformation of a given concatenated set of Primitives, `cat(t[k])` from the T domain to the B domain.

The concatenation Composability property or Composability for short, between T and B is expressed as follows:

Given a set of Primitives `b[k]` and `t[k]` and transformations `T(B)` and `B(T)` such that `t[k] = T(b[k])` and `b[k] = B(t[k])` for all `k`, then `T(B)` and `B(T)` are jointly concatenation composable if and only if,

```text
T(cat(b[k]))=cat(T(b[k])) and B(cat(t[k]))=cat(B(t[k])) for all k.
```

Basically, Composability (over concatenation) means that the transformation of a set (as a whole) of concatenated Primitives is equal to the concatenation of the set of individually transformed Primitives.

For example, suppose there are two Primitives in the Text domain, namely, `t[0]` and `t[1]` that each transforms, respectively, to primitives in the Binary domain, namely, `b[0]` and `b[1]`. The transformation duals, `B(T)` and `T(B)`, are composable if and only if,

```text
B(t[0] + t[1]) = B(t[0]) + B(t[1]) = b[0] + b[1]
```

and

```text
T(b[0] + b[1]) = T(b[0]) + T(b[1]) = t[0] + t[1].
```

The Composability property defined above allows us to create arbitrary compositions of primitives via concatenation in either the T or B domain and then convert the composition en masse to the other domain and then de-concatenate the result without loss. The self-framing property of the primitives enables de-concatenation.

The Composability property is an essential building block for streaming in either Domain. The use of framing Primitives that count or group other Primitives enables multiplexing and demultiplexing of arbitrary groups of Primitives for pipelining and/or on or offloading of streams. The Text domain representation of a Stream enables better usability (readability), and the Binary domain representation of a Stream enables better compactness. In addition, pipelined hierarchical composition codes allow efficient conversion or off-loading for concurrent processing of composed (concatenated) groups of Primitives in a Stream without having to individually parse each Primitive before off-loading.

### Concrete Domain representations

The Text, ‘T’, domain representations in CESR use only the characters from the URL and filename safe variant of the IETF RFC-4648 Base64 standard [[spec: RFC4648]]. Unless otherwise indicated, all references to Base64 [[spec: RFC4648]] in this document imply the URL and filename safe variant. The URL and filename safe variant of Base64 uses in order the 64 characters `A to Z`, `a to z`, `0 to 9`, `-`, and `_` to encode 6 bits of information. In addition, Base64 uses the `=` character for padding, but CESR does not use the `=` character for any purpose because all CESR-encoded Primitives are composable.

The fact that Base64 [[spec: RFC4648]] by itself does not satisfy the Composability property is notable and must employ pad characters to ensure one-way convertibility between the Binary domain and the Text domain.

In CESR, however, both ‘T’ and ‘B’ domain representations include a prepended Framing Code prefix that is structured in such a way as to ensure Composability.

Suppose that Base64 characters are used in the Text domain and binary bytes are used in the Binary domain, called respectively, naive text and naive binary encodings and Domains. Recall that a byte encodes 8 bits of information and a Base64 character encodes 6 bits of information. Furthermore, suppose that there are three Primitives denoted `a`, `b`, and `c` in the naive Binary domain with lengths of 1, 2, and 3 bytes, respectively.

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
```

#### Conversions

When doing a naive Base64 conversion of a naive binary Primitive, one Base64 character represents only six bits from a given byte. In the following diagrams, each character of a Base64 conversion is denoted using zero-based indices, with the most significant character first.

Therefore, encoding `a` in Base64 requires at least two Base64 characters because the zeroth character only captures the six bits from the first byte, and another character is needed to capture the other two bits. The convention in Base64 uses a Base64 character where the non-coding bits are zeros. This is diagrammed as follows:

```text
|           A0          |
|a7:a6:a5:a4:a3:a2:a1:a0|z3:z2:z1:z0|
|        T1       |        T0       |
```
where `aX` represents a bit from `a`, `AX` represents a byte from `a`, `zX` represents a zeroed pad bit, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string.

Naive Base64 encoding always pads each individual conversion of a string of bytes to an even multiple of four characters. This provides a property that is not true Composability but does ensure that multiple distinct concatenated conversions from binary to Base64 text are separable. It may be described as a sort of one-way composability. So, with pad characters, denoted by replacing the spaces with `=` characters, the Base64 conversion of `a` is as follows:

```text
|           A0          |
|a7:a6:a5:a4:a3:a2:a1:a0|z3:z2:z1:z0|
|        T3       |        T2       |========P1=======|========P0=======|
```
where `PX` represents a trailing pad character. We see that Base64 conversion effectively left shifts `a` by four bits plus two pad characters. In other words, the Base64 conversion of `a` is no longer right-aligned with respect to the trailing Base64 character.

Likewise, `b` requires at least three Base64 characters to capture all of its sixteen bits of information as follows:

```text
|           B1          |           B0          |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|z1:z0|
|        T2       |        T1       |        T0       |
```
where `bX` represents a bit from `b`, `BX` represents a byte from `b`, `zX` represents a zeroed pad bit, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string.

Alignment on a four-character (24-bit) boundary requires one pad character this becomes:

```text
|           B1          |           B0          |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|z1:z0|
|        T2       |        T1       |        T0       |========P0=======|

```
where `PX` represents a trailing pad character. We see that Base64 conversion effectively left shifts `b` by two bits plus one pad character. In other words, the Base64 conversion of `b` is no longer right-aligned with respect to the trailing Base64 character.

Finally, `c` requires exactly four Base64 characters to capture all of its twenty-four bits of information. There are no pad characters required.

```text
|           C0          |           C1          |           C2          |
|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|c7:c6:c5:c4:c3:c2:c1:c0|
|        T3       |        T2       |        T1       |        T0       |
```
where `cX` represents a bit from `c`, `CX` represents a byte from `c`, and `TX` represents a non-pad character from the converted Base64 text representing one hextet of information from the converted binary string. There are no bit shifts because there are no pad bits nor pad characters needed, and the resulting Base64 conversion is right aligned with respect to the trailing Base64 character.

Suppose `a + b` now is concatenated into a three-byte composition in the naive Binary domain before Base64 encoding the concatenated whole. 

```text
|           A0          |           B1          |           B0          |
|a7:a6:a5:a4:a3:a2:a1:a0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|        T3       |        T2       |        T1       |        T0       |
```

The least significant two bits of `A0` are encoded into the same character, `T2`, as the most significant four bits of `B1`. Therefore, a Text domain parser would be unable to cleanly de-concatenate on a character-by-character basis the conversion of `a + b` into separate Text domain Primitives. Therefore, standard (naive) binary to Base64 conversion does not satisfy the Composability constraint.

Starting instead in the Text domain with Primitives `u` and `v` of lengths 1 and 3 characters, respectively, these two Primitives can be concatenated as `u + v` in the Text domain and then converted as a whole to naive binary. 

```text
|        U0       |        V2       |        V1       |        V0       |
|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|b7:b6:b5:b4:b3:b2:b1:b0|
|           B2          |           B1          |           B0          |
```

All six bits of information in `U0` are included in `B2` along with the least significant two bits of information in `V2`. Therefore, a Binary domain parser is unable to cleanly de-concatenate on a byte-by-byte basis the conversion of `u + v` into separate Binary domain Primitives. Therefore, standard (naive) Base64 to binary conversion does not satisfy the Composability constraint.

The Composability property is satisfied only if each Primitive in the ‘T’ domain is an integer multiple of four Base64 characters (24 bits) and each Primitive in the ‘B’ domain is an integer multiple of three bytes (24 bits). Each of either four Base64 text characters or three binary bytes captures 24 bits of information. Twenty-four is the least common multiple of six and eight. Therefore, in order to cleanly capture integer multiples of twenty-four bits of information, Primitive lengths must be integer multiples of either four Base64 text characters or three binary bytes in their respective Domains. Given that the constraint of alignment on 24-bit boundaries in either Text domain or Binary domain is satisfied, the conversion of concatenated Primitives in one Domain never results in the same byte or character in the converted Domain sharing bits from two adjacent Primitives. This constraint of 24-bit alignment, therefore, satisfies the Composability property.

To elaborate, when converting Streams made up of concatenated Primitives back and forth between the ‘T’ and ‘B’ domains, the converted results will not align on byte or character boundaries at the end of each Primitive unless the Primitives themselves are integer multiples of twenty-four bits of information. In other words, all Primitives must be aligned on 24-bit boundaries to satisfy the Composability property. This means that the length of any Primitive in the ‘B’ domain must be an integer multiple of three binary bytes with a minimum length of three binary bytes. Likewise, this means that the length of any Primitive in the ‘T’ domain must be an integer multiple of four Base64 characters with a minimum length of four Base64 characters.

#### Stable Framing Codes in the text domain

There are many coding schemes that could satisfy the Composability constraint of alignment on 24-bit boundaries. The main reason for using a ‘T’ domain-centric encoding is higher usability, readability, or human friendliness. A primary design goal of CESR is to select an encoding approach that provides high usability, readability, or human friendliness in the ‘T’ domain. This type of usability goal simply is not realizable in the ‘B’ domain. The ‘B’ domain's purpose is merely to provide convenient compactness at scale. Usability in the ‘T’ domain is maximized when the type portion of the prepended Framing Code and its postpended value are Stable, i.e., ‘invariant’. 

##### Stable type encoding

Stable type coding makes it much easier to recognize Primitives of a given type when debugging source, reading Messages, or documents in the ‘T’ domain that include encoded Primitives. This is true even when those Primitives have different lengths or values. For Primitive types that have fixed lengths, i.e., all Primitives of that type have the same length, Stable type coding aids visual type and visual size recognition. Stable type coding means that the leading characters that determine the type do not change when any other portion of the primitive changes.

The usability of Stable type coding is maximized when the type portion appears first in the Framing Code. Stability also requires that for a given type, the type coding portion must consume a fixed integer number of characters in the ‘T’ domain. To clarify, as used here, Stable type coding in the ‘T’ domain never shares information bits with either length or value coding in any given Framing Code character and appears first in the Framing Code. Stable type coding in the ‘T’ domain translates to Stable type coding in the ‘B’ domain except that the type coding portion of the Framing Code may not respect byte boundaries. This is an acceptable tradeoff because binary-domain parsing tools easily accommodate bit fields and bit shifts while text-domain parsing tools do not. Generally, Text domain parsing tools only process whole characters. This is another reason to impose a stability constraint on the ‘T’ domain type coding instead of the ‘B’ domain.

##### Stable value encoding

A secondary usability constraint is recognizable or readable Stable value coding in the Text, ‘T’, domain. Stable value encoding means that the trailing Base64 characters that encode the primitive value are right aligned. This means one can manually confirm values are the same. Not all Primitives benefit from Stable value coding. Any representation of a value that is a long random string of characters is essentially unreadable or recognizable versus some other representation. Consequently, bit shifts of the value that result in leading or trailing zero pads, as long as they are static, do not change the readability. This is not true, however, of values that are small numbers. Base64 encodings of small numbers are readable. for example, the numerical sequence of decimal numbers, `0, 1, 2`, is recognizable as the sequence of Base64 characters, `A, B, C`. Thus, all else equal, readable Stable value encodings also contribute to usability, at least in some cases.  The combination of Stable leading type encoding and Stable trailing value encoding means that any zero padding must appear in the middle of the Primitive, after the type code, but before the value.

#### Code characters and lead bytes

There are two ways to provide the required alignment on 24-bit boundaries to satisfy the Composability property. One is to post-pad (with trailing pad characters, `=`) the Text domain encoding to ensure that the ‘T’ domain Primitive has a total size (length) that is an integer multiple of 4. This is what naive Base64 encoding does. The other way is to pre-pad leading bytes of zeros to the raw binary value before conversion to Base64 to ensure the total size of the raw binary value with pre-pad bytes is an integer multiple of 3 bytes. This ensures that the size in characters of the Base64 conversion of the pre-padded raw binary is an integer multiple of 4 characters. This means that, effectively, value padding shows up as a mid-pad relative to the full Primitive with a prepended type code.

Given pre-padded values, one of two options is available that depends on the specific code. In the first option, an appropriate number of text characters that result from the conversion of a porting of the leading pre-pad zero bytes are replaced with the appropriate number of code characters. In the second option, the code characters are pre-pended to the conversion with leading zeros intact. In the second option, the length of the pre-pended type code must also, thereby, be an integer multiple of 4 characters. In either option, the total length of the ‘T’ domain Primitive with code is an integer multiple of 4 characters.

The first option may be more compact in some cases than the second. The second option may be easier to compute in some cases. The most significant advantage of the second option is that the value portion is Stable and more readable both in the Text, ‘T’, domain and in the Binary, ‘B’, domain because the value portion is not shifted by the Base64 conversion as it is with the first option.

In order to avoid confusion with the use of the term 'pad character' when pre-padding with bytes that are not replaced later, the term 'lead bytes' is used. The term pad may be confusing not merely because both ways use a type of padding, but it is also true that the number of pad characters when padding post-conversion equals the number of lead bytes when padding pre-conversion.

Suppose that the raw binary value is 32 bytes in length. The next higher integer multiple of 3 is 33 bytes. Thus 1 additional leading pad byte is needed to make the size (length in byte) of raw binary an integer multiple of 3. The 1 lead byte makes that combination a total of 33 bytes in length. The resultant Base64 converted value will be 44 characters in length, which is an integer multiple of 4 characters. In contrast, recall that when a 32-byte raw binary value is converted to Base64, the converted value will have 1 trailing pad character. In both cases, the resultant length in Base64 is 44 characters.

Similarly, a 64-byte raw binary value needs 2 lead bytes to make the combination 66 bytes in length, where 66 is the next integer multiple of 3 greater than 64. When converted, the result is 88 characters in length. The number of pad characters added on the result of the Base64 conversion of a 64-byte raw binary is also 2.

In summary, there are two possibilities for CESR's coding scheme to ensure a composable 24-bit alignment. The first is to add trailing pad characters post-conversion. The second is to add leading pad bytes pre-conversion. Because of the greater readability of the value portion of both the fully qualified Text, ‘T’, or fully qualified Binary, ‘B’, domain representations, the second approach was chosen for CESR.

#### Multiple code table approach

The design goals for CESR Framing Codes include minimizing the Framing Code size for the most frequently used (most popular) codes while also supporting a sufficiently comprehensive set of codes for all foreseeable current and future applications. This requires a high degree of both flexibility and extensibility. This is best achieved with multiple code tables with a different coding scheme optimized for a different set of features instead of a single one-size-fits-all scheme. A specification that supports multiple coding schemes may appear on the surface to be much more complex to implement, but careful design of the coding schemes can reduce implementation complexity by using a relatively simple single integrated parse and conversion table. Parsing in any given Domain given Stable type codes may then be implemented with a single function that simply reads the appropriate type selector in the table to know how to parse and convert the rest of the Primitive.