## Table types
<!-- file name: table-types.md -->

The tables in CESR consist of:
* Two major types for raw CESR primitives:
	* fixed-length raw size primitives 
	* variable-length raw size primitives
* Count code tables for grouping primitives
* Protocol genus and version tables for use in differentiating between genus and versions of protocols encoded in CESR
* Opcode tables that are not yet specified but may be used for advanced decentralized applications in the future
* Some special context specific code tables (and space for future tables) for use in various contexts, like easy indexing into arrays of primitives

The sections below explain these table types, the code selectors that differentiate these types, and how to use these tables to decode a given CESR stream.

### Tables of codes for fixed-length raw sizes  

#### Small fixed-length raw-size tables

There are two special tables that are dedicated to the most popular fixed-size raw binary Cryptographic Primitive types. These are the most compact, so they optimize bandwidth but only provide a small number of total types. In both of these, the text code size equals the number of pad characters, i.e., the pad size.

##### One-character fixed-length raw-size table

The one-character type code table does not have a selector character per se but uses as type codes the non-selector characters `A` - `Z` and `a` - `z`. This provides 52 unique type codes for fixed-size raw binary values with a pad size of 1.

##### Two-character fixed-length raw size table

The two-character type code table uses the selector `0` as its first character. The second character is the type code. This provides 64 unique type codes for fixed-size raw binary values that have a pad size of 2.

#### Large fixed-length raw-size tables

The three tables in this group are for large fixed raw-size Primitives. These three tables use 0, 1, or 2 lead bytes as appropriate for a pad size of 0, 1, or 2 for a given fixed raw binary value. The text code size for all three tables is 4 characters. The selector character not only encodes the table but also implicitly encodes the number of lead bytes. The 3 remaining characters are each type code in each table, providing 262,144 unique types. This should provide enough type codes to accommodate all fixed raw-size Primitive types for the foreseeable future.

##### Large fixed-length raw-size table with 0 Lead Bytes

This table uses `1` as its first character or selector. The remaining 3 characters provide the type of each code. Only fixed-size raw binaries with a pad size of 0 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values (`262144 = 64**3`) for fixed-size raw binary Primitives with a pad size of 0.

##### Large fixed-length raw-size table with 1 lead byte

This table uses `2` as its first character or selector. The remaining 3 characters provide the type of each code. Only fixed-size raw binaries with a pad size of 1 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values (`262144 = 64**3`). Together with the 52 values from the 1-character code table above, there are 262,196 type codes for fixed-size raw binary Primitives with a pad size of 1.

##### Large fixed-length raw-size table with 2 lead bytes

This table uses `3` as its first character or selector. The remaining 3 characters provide the type of each code. Only fixed-size raw binaries with a pad size of 2 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values (`262144 = 64**3`). Together with the 64 values from the 2-character code table above (selector `0`), there are 262,208 type codes for fixed-size raw binary Primitives with a pad size of 2.

### Tables for codes with variable-length raw-sizes  

#### Small variable-length raw-size tables

Although many Primitives have fixed raw binary sizes, especially those for modern cryptographic suites such as keys, signatures, and digests, there are other Primitives that benefit from variable sizings such as either encrypted material or legacy cryptographic material types found in the GNU Privacy Guard (GPG) or Open Secure Sockets Layer (OpenSSL) libraries (like the Rivest-Shamir-Adleman (RSA) algorithm. Furthermore, CESR is meant to support not merely cryptographic material types but other basic types, such as generic text strings and numbers. These basic non-cryptographic types may also benefit from variable-size codes.

The three tables in this group are for small variable raw-size Primitives. These three tables use 0, 1, or 2 lead bytes as appropriate given the pad size of 0, 1, or 2 for a given variable size raw binary value. The text code size for all three tables is 4 characters.  The first character is the selector, the second character is the type, and the last two characters provide the size of the value as a Base64 encoded integer.

The number of unique type codes in each table is, therefore, 64. A given type code is repeated in each table for the same type so that all that differs between codes of the same type in each table is the number of lead bytes needed to align a given variable length on a 24-bit boundary. To clarify, what is different in each table is the number of lead bytes needed to achieve 24-bit alignment for a given variable length. Thus, the selector not only encodes for which type table but also implicitly encodes the number of lead bytes. The variable size is measured in Quadlets of 4 characters each in the ‘T’ domain and equivalently in triplets of 3 bytes each in the ‘B’ domain. Thus, computing the number of characters when parsing or off-loading in the ‘T’ domain means multiplying the variable size by 4. Computing the number of bytes when parsing or off-loading in the ‘B’ domain means multiplying the variable size by 3. The two Base64 size characters provide value lengths in Quadlets/triplets from 0 to 4095 (`64**2 -1`). This corresponds to value lengths of up to 16,380 characters (`4095 * 4`) or 12,285 bytes (`4095 * 3`).

##### Small variable-length raw-size table with 0 lead bytes

This table uses `4` as its first character or selector. The second character provides the type. The final two characters provide the size of the value in Quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 0 are encoded with this table. The 1-character type code provides a total of 64 unique type code values. The maximum length of the value provided by the 2 size characters is 4095 Quadlets of characters in the ‘T’ domain and triplets of bytes in the ‘B’ domain. All are raw binary Primitives with a pad size of 0 that each includes 0 lead bytes.

##### Small variable-length raw-size table with 1 lead byte

This table uses `5` as its first character or selector. The second character provides the type. The final two characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 1 are encoded with this table. The 1-character type code provides a total of 64 unique type code values. The maximum length of the value provided by the 2 size characters is 4095 quadlets of characters in the ‘T’ domain and triplets of bytes in the ‘B’ domain. All are raw binary Primitives with a pad size of 1 that each includes 1 lead byte.

##### Small variable-length raw-size table with 2 lead bytes

This table uses `6` as its first character or selector. The second character provides the type. The final two characters provide the size of the value in quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 0 are encoded with this table. The 1-character type code provides a total of 64 unique type code values. The maximum length of the value provided by the 2 size characters is 4095 quadlets of characters in the ‘T’ domain and triplets of bytes in the ‘B’ domain. All are raw binary Primitives with a pad size of 2 that each includes 2 lead bytes.

#### Large variable-length raw-size tables 

Many legacy cryptographic libraries such as OpenSSL and GPG support any variable-sized Primitive for keys, signatures, and digests such as RSA. Although this approach is often criticized for providing too much flexibility, many legacy applications depend on this degree of flexibility. Consequently, these large variable raw size tables provide a sufficiently expansive set of tables with enough types and sizes to accommodate all the legacy cryptographic libraries as well as all the variable-sized non-cryptographic raw Primitive types for the foreseeable future.

::: issue
https://github.com/trustoverip/tswg-cesr-specification/issues/14
:::

The three tables in this group are for large variable raw size Primitives. These three large variable raw size tables use 0, 1, or 2 lead bytes as appropriate for the associated pad size of 0, 1, or 2 for a given variable-sized raw binary value. The text code size for all three tables is 8 characters. As a special case, the first 62 entries in these tables represent that same crypto suite type as the 62 entries in the small variable raw size tables above. This allows one type to use a smaller 4-character text code when the raw size is small enough. The first character is the selector, the next three characters provide the type, and the last four characters provide the size of the value as a Base64 encoded integer. 

With 3 characters for each unique type code, each table provides 262,144 unique type codes. This should be enough type codes to accommodate all fixed raw size Primitive types for the foreseeable future.  A given type code is repeated in each table for the same type. What is different for each table is the number of lead bytes needed to align a given length on a 24-bit boundary. The selector not only encodes the table but also implicitly encodes the number of lead bytes. The variable size is measured in quadlets of 4 characters each in the ‘T’ domain and equivalently in triplets of 3 bytes each in the B’ domain. Thus, computing the number of characters when parsing or off-loading in the ‘T’ domain means multiplying the variable size by 4. Likewise, computing the number of bytes when parsing or off-loading in the ‘B’ domain means multiplying the variable size by 3. The four Base64 size characters provide value lengths in Quadlets/triplets from 0 to 16,777,215 (`64**4 -1`). This corresponds to value lengths of up to 67,108,860 characters (`16777215 * 4`) or 50,331,645 bytes (`16777215 * 3`).

##### Large variable-length raw-size table with 0 lead bytes

This table uses `7` as its first character or selector. The next three characters provide the type. The final four characters provide the size of the value in Quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 0 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values. The maximum length of the value provided by the 4 size characters is 16,777,215 Quadlets of characters in the ‘T’ domain and triplets of bytes in the ‘B’ domain. All are raw binary Primitives with pad size of 0 that each includes 0 lead bytes.

##### Large variable-length raw-size table with 1 lead byte

This table uses `8` as its first character or selector. The next three characters provide the type. The final four characters provide the size of the value in Quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 1 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values. The maximum length of the value provided by the 4 size characters is 16,777,215 Quadlets of characters in the ‘T’ domain and triplets of bytes in the ‘B’ domain. All are raw binary Primitives with a pad size of 1 that each includes 1 lead byte.

##### Large variable-length raw-size table with 2 lead bytes

This table uses `9` as its first character or selector. The next three characters provide the type. The final four characters provide the size of the value in Quadlets/triplets as a Base64 encoded integer. Only raw binaries with a pad size of 2 are encoded with this table. The 3-character type code provides a total of 262,144 unique type code values. The maximum length of the value provided by the 4 size characters is 16,777,215 Quadlets of characters in the ‘T’ domain and triplets of bytes in the ‘B’ domain. All are raw binary Primitives with a pad size of 2 that each includes 2 lead bytes.

### Count Code tables

There may be as many at 13 Count Code tables, but only three are specified currently. These three are:
* The small count, four-character table
* The large count, eight-character table
* The eight-character protocol genus and version table. 

Each Count Code shall be aligned on a 24-bit boundary. Count Codes have no value component but have only type and size components. The size component counts Quadlets/triplets in the following group. Moreover, because Primitives are already guaranteed to be composable, Count Codes do not need to account for pad size as long as the Count Code is aligned on a 24-bit boundary. The Count Code type indicates the type of Primitive or group being counted. When the code supports variable-sized primitives, the size indicates how many Quadlets/triplets are consumed by that group. When the code does not support variable size primitives, i.e., the converted raw part is empty, then the size may be used to convey special Base64 values more compactly. 

Count Code tables use the first two characters as a nested set of selectors. The first selector uses the `-` character for the initial selector. The next character is either a selector for another Count Code table or is the type for the small Count Code table. When the second character is numeral `0` - `9` or the letters `-` or `_`, then it is a secondary Count Code table selector. When the second character is a letter in the range `A` - `Z` or `a` - `z`, then it is a unique single-character Count Code. This results in a total of 52 single-character Count Codes.

All Count Codes except the genus/version code table (see below) are pipelineable because they count the number of Quadlets/triplets in the count group. A Quadlet is four Base64 characters in the Text domain. A triplet is three B2 bytes in the Binary domain. The count is invariant in either Domain. This allows a stream parser to extract the count number of characters/bytes in a group from the Stream without parsing the group's contents. By making all Count Codes pipelineable, the Stream parser can be optimized in a granular way. This includes granular core affinity.

##### Small Count Code table

Codes in the small Count Code table are each four characters long. The first character is the selector `-`. The second character is the Count Code type. the last two characters are the count size as a Base64 encoded integer. The Count Code type must be a letter `A` - `Z` or `a` - `z`. If the second character is not a letter but is a numeral `0` - `9` or `-` or `_`, then it is a selector for a different Count Code table. The set of letters provides 52 unique Count Codes. A two-character size provides counts from 0 to 4095 (`64**2 - 1`).

##### Large Count Code table

Codes in the large Count Code table are each 8 characters long. The first two characters are the selectors `-0`. The next character is the Count Code type. the last five characters are the count size as a Base64 encoded integer. With one character for type, there are 64 unique large-Count Code types. A five-character size provides counts from 0 to 1,073,741,823 (`64**5 - 1`). These correspond to groups of size `1,073,741,823 * 4 = 4,294,967,292` characters or `1,073,741,823 * 3 = 3,221,225,469` bytes.

### Protocol genus and version table

The protocol genus/version table is special because its codes modify the Count Code groups that appear at the top level of the stream or the Count code groups that appear inside special enclosing Count Code groups. A protocol genus and version code itself does not provide a count of the following Quadlets or triplets but modifies the protocol genus and Version of all the following Count Codes that either appear at the top level until another protocol and genus Count Code is provided or are inside a special enclosing Count Code group. There are three general-purpose special enclosing Count Codes that allow an embedded genus/version table code. These are defined below. These are special because they must be universal across all genera of Count Code tables.

The purpose of the protocol genus/version table is twofold. First, it allows CESR to be used for different protocols and protocol stacks, where each protocol may have its own dedicated set of code tables. The only table that all protocols must share is the protocol genus and version table (protocol table for short) and. All other entries in all other tables may vary by protocol. Secondly, for a given protocol genus, a protocol genus and version code provide the Version of that given protocol's table set. This allows versioning of the CESR code tables for a given protocol.

##### Protocol and genus version table
The format for a protocol genus and version code is as follows: `--GGGVVV` where `GGG` represents the protocol genus and `VVV` is the Version of that protocol genus. The genus uses three Base64 characters for a possible total of 262,144 different protocol genera. The next three characters, `VVV`, provide in Base64 notation the major and minor version numbers of the Version of the protocol genus. The first `V` character provides the major version number, and the final two `VV` characters provide the minor version number. For example, `CAA` indicates major version 2 and minor version 00 or in dotted-decimal notation, i.e., `2.00`. Likewise, `CAQ` indicates major version 2 and minor version decimal 16 or in dotted-decimal notation `1.16`. The version part supports up to 64 major versions with 4096 minor versions per major version.

Any addition of a new code to the code table is backward breaking in at least one direction, so it is a feature change in at least one direction. New implementations with the new codes can accept streams from old implementations, but old ones will break if they receive the new ones. A major change means a code's meaning has changed. This means it breaks in both directions, i.e., sender and receiver. A minor change happens when a code is added; this only breaks backward compatibility when a new sender sends to an old receiver, but a new sender will still correctly process a stream sent from an old receiver. Since code additions will be commonly compared to code changes, it is beneficial to have more room for minor vs. major versions.



### OpCode tables

##### Op Code table
The `_` selector is reserved for the yet-to-be-defined opcode table or tables. Opcodes are meant to provide Stream processing instructions that are more general and flexible than simply concatenated Primitives or groups of Primitives. A yet-to-be-determined stack-based virtual machine could be executed using a set of opcodes that provides Primitive, groups of Primitives, or Stream processing instructions. This would enable highly customizable uses for CESR.


### Summary of Selector code tables and encoding scheme design

#### Encoding scheme table

The following table summarizes the ‘T’ domain coding schemes by selector code for the 15 code tables defined in the sections above:

##### Encoding Scheme Table

| Table | Universal Selector | Selector | Type Chars | Value Size Chars | Code Size | Lead Bytes | Pad Size | Format |
|:-------------------------|:---------:|:---------:|:----:|:---:|:---:|:---:|:---:|--------------:|
|  |           |           |      |     |     |     |     |               |
| 1-char fixed |`[A-Z,a-z]`|           | `1*` |  0 or special |  1  |  0  |  1  |         `$&&&`|
| 2-char fixed |     `0`   |           |   1  |  0 or special |  2  |  0  |  2  |         `*$&&`|
| large fixed 0-char lead byte |     `1`   |           |   3  |  0 or special |  4  |  0  |  0  |     `*$$$&&&&`|
| large fixed 1-char lead byte |     `2`   |           |   3  |  0 or special |  4  |  1  |  1  |     `*$$$%&&&`|
| large fixed 2-char lead byte |     `3 `  |           |   3  |  0 or special |  4  |  2  |  2  |     `*$$$%%&&`|
| small var 0-char lead byte |     `4`   |           |   1  |  2  |  4  |  0  |  0  |     `*$##&&&&`|
| small var 1-char lead byte |     `5`   |           |   1  |  2  |  4  |  1  |  1  |     `*$##%&&&`|
| small var 2-char lead byte |     `6`   |           |   1  |  2  |  4  |  2  |  2  |     `*$##%%&&`|
| large var 0-char lead byte |     `7`   |           |   3  |  4  |  8  |  0  |  0  | `*$$$####&&&&`|
| large var 1-char lead byte |     `8`   |           |   3  |  4  |  8  |  1  |  1  | `*$$$####%&&&`|
| large var 2-char lead byte |     `9`   |           |   3  |  4  |  8  |  2  |  2  | `*$$$####%%&&`|
| small cnt code |     `-`   |`[A-Z,a-z]`| `1*` |  0  |  4  |  0  |  0  |         `*$##`|
| large code cnt|     `-`   |     `0`   |  1 |  0 |  8  |  0  |  0  |     `**$#####`|
| proto + genus |     `-`   |     `-`   |   1 |  0  |  8  |  0  |  0  |     `**$$$###`|
| other cnt codes |     `-`   |     `[1-9,_]`   |   TBD |  TBD  |  TBD  |  TBD  |  TBD  |     `**`|
| op codes |     `_`   |           | TBD | TBD | TBD | TBD | TBD |            `*`|

Special fixed-size codes can convey values in the value size part of the code. This enables more compact encoding of small special values like field tags, types, or versions. In this case, the Code Size must equal the size of the Selector, Type, and Value Size parts summed together. This means the converted raw part must be empty. In that case, a fixed-sized code but with a non-empty Value Size, the value of the Value Size part may have special meaning.

##### Encoding scheme symbols

The following table defines the meaning of the symbols used in the encoding scheme table `Format` column above:

##### Encoding Scheme Symbols Table

| Symbol | Description |
|:---:|:-----|
|   |   |
| `*` | selector-code character also provides the type |
| `$` | type-code character from subset of Base64 `[A-Z, a-z,0-9,-,_]` |
| `%` | lead byte where pre-converted binary includes the number of lead bytes shown |
| `#` | Base64 digit as part of a base 64 integer. When part of Primitive determines the number of following Quadlets or triplets. When part of a Count Code determines the count of the following Primitives or groups of Primitives |
| `&` | Base64 value characters that represent the converted raw binary value.  The actual number of characters is determined by the prepended text code.  Shown is the minimum number of value characters. |
| `TBD` | to be determined, reserved for future use |


### Special context-specific code tables

The set of tables above provides the basic or master encoding schemes. These coding schemes constitute the basic or master set of code tables. This basic or master set, however, may be extended with context-specific code tables. The context in which a Primitive occurs may provide an additional implicit selector that is not part of the actual explicit text code. This allows context-specific coding schemes that would otherwise conflict with the basic or master encoding schemes and tables.

#### Indexed codes

Currently, there is only one context-specific coding scheme for indexed signatures. A common use case is thresholded multi-signature schemes. A threshold-satisficing subset of signatures belonging to an ordered set or list of public keys may be provided as part of a Stream of Primitives. One way to compactly associate each signature with its public key is to include the index into the ordered set of public keys in the text code for that signature.

A popular signature raw binary size is 64 bytes, with a pad size of 2. This gives two code characters for a compact text code. The first character is the selector and type code. The second character is the Base64 encoded integer index.  Using a similar dual selector type code character scheme as above, the selectors are the numbers `0-9` and `-` and `_`. Then there are 52 type codes given by the letters `A- Z` and `a-z`. The index has 64 values which support up to 64 members in the public key list. A selector can select a large text code with more characters dedicated to larger indices. Some applications of CESR, like KERI, need dual-indexed signatures (i.e., each signature has two indices) to support pre-rotation with partial or reserved participants in a rotation. With partial rotation, a given signature may contribute to the signing threshold for two different thresholds, each on two different lists of keys where the associated key may appear at a different location in each list. For 64-byte signatures, the Ed25519 and ECDSA secp256k1 schemes have entries in the table. For dual-indexed codes, the next larger code size that aligns a 64-byte signature on a 24-bit boundary is 6 characters. The table provides entries for dual-indexed 64-byte signatures. The code includes one selector character, one type character, and two each of two-character indices.

A new signature scheme based on Ed448 with 114-byte signatures is also supported. These signatures have a pad size of zero, so they require a four-character text code. The first character is the selector `0`, the second character is the type with 64 values, and the last two characters each provide a one-character index as a Base64 encoded integer with 64 different values. A big Version code consumes eight characters with one character for the selector, one for the type, and three for each of the dual indices.

##### Indexed code table

The associated indexed schemes are provided in the following table:

| Selector | Type Chars | Index Chars | Ondex Chars | Code Size | Lead Bytes | Pad Size |     Format    |
|:---------:|:----------:|:-----------:|:-----------:|:---------:|:----------:|:--------:|--------------:|
|           |            |             |             |           |            |          |               |
|`[A-Z,a-z]`|  `1*`      |     1       |      0      |      2    |      0     |      2   |         `$#&&`|
|     `0`   |   1        |     1       |      1      |      4    |      0     |      0   |     `0$##&&&&`|
|     `2`   |   1        |     2       |      2      |      6    |      0     |      2   |   `2$####&&&&`|
|     `3`   |   1        |     3       |      3      |      6    |      0     |      0   | `3$######&&&&`|

##### Encoding scheme format symbol table

The following table defines the meaning of the symbols used in the Indexed Code table:

| Symbol |  Description  |
|:---:|:----:|
|   |   |
| `*` | selector-code character also provides the type |
| `$` | type-code character from subset of Base64 `[A-Z,a-z,0-9,-,_]` |
| `%` | lead byte where pre-converted binary includes the number of lead bytes shown |
| `#` | Base64 digit as part of a base 64 integer. When part of Primitive determines the number of following Quadlets or triplets. When part of a Count Code determines the count of following Primitives or groups of Primitives |
| `&` | Base64 value characters that represent the converted raw binary value.  The actual number of characters is determined by the prepended text code.  Shown is the minimum number of value characters. |
| `TBD` | to be determined, reserved for future |

### Parsing via table design

Text domain parsing can be simplified by using a parse size table. A Text domain parser uses the first character selector code to look up the hard-size (Stable) portion of the text code. The parser then extracts hard-size characters from the text Stream. These characters form an index for the parse size table, which includes a set of sizes for the remainder of the Primitive. Using these sizes for a given code allows a parser to extract and convert a given Primitive. In the Binary domain, the same text parse table may be used, but each size value represents a multiple of a sextet of bits instead of Base64 characters. Example entries from that table are provided below. Two of the rows may always be calculated given the other 4 rows, so the table need only have 4 entries in each row. Thus, all basic Primitives may be parsed with one parse size table.

#### Table 1

Selector code size table

| selector | hs  |
|:---------:|:----:|
|           |      |
|       `B` |   1  |
|       `0` |   2  |
|       `5` |   2  |
|           |      |

#### Table 2 

Parse size table

Below is a snippet from the parse size table for some example codes:

| hard sized index |  hs  |  ss  |  vs  |  fs  |  ls  |  ps  |
|:---------:|:----:|:----:|:----:|:----:|:----:|:----:|
|           |      |      |      |      |      |      |
|       `B` |   1  |   0  |  43\* |  44  |  0   |  1   |
|      `0B` |   2  |   0  |  86\* |  88  |  0   |  2\*  |
|      `5A` |   2  |   2  |  \#   |   \#  |  1   |  1\*  |
|           |      |      |      |      |      |      |

#### Table 3

Parse table symbols

| Symbol | Description  |
|:---:|:----:|
|   |   |
| `*` | entry's size may be calculated from other sizes |
| `#` | entry's size may be calculated from extracted code characters given by other sizes |
|   |      |

#### Table 4

Parse part sizes

The following table includes both labels of parts shown in the columns in the parse size table as well as parts that may be derived from the parse table parts or from transformations,

| Label | Description |
|:----:|:----:|
| ‘hs’ | hard size in chars (fixed) part of code size   |
| ‘ss’ | soft size in chars, (Variable) part of code size |
| ‘os’ | other size in chars, when soft part includes two Variable values   |
| ‘ms’ | main size in chars, (derived) when soft part provides two Variable values where ms = ss – os |
| ‘cs’ | code size in chars (derived value), where cs = hs + ss  |
| ‘vs’ | value size in chars |
| ‘fs’ | full size in chars where fs = hs + ss + vs |
| ‘ls’ | lead size in bytes to pre-pad raw binary bytes |
| ‘ps’ | pad size in chars Base64 encoded |
| ‘rs’ | raw size in bytes (derived) of binary value where rs is derived from `R(T)` |
| ‘bs’ | binary size in bytes (derived) where bs = ls + rs |




[//]: # (\backmatter)


[//]: # (# KERI Protocol Stack Code Tables {#sec:annexA .informative})