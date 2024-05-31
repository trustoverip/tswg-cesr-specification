## Annex A
### Code table entry policy 

The policy for placing entries into the tables in general is in order of first needed first-entered basis. In addition, the compact code tables prioritize entries that satisfy the requirement that the associated cryptographic operations maintain at least 128 bits of cryptographic strength. This precludes the entry of many weak cryptographic suites into the compact tables. CESR's compact code table includes only best-of-class cryptographic operations along with common non-Cryptographic Primitive types. At the time of this writing, there is the expectation that the National Institute of Standards and Technology (NIST) soon will approve standardized post-quantum resistant cryptographic operations. When that happens, codes for the most appropriate post-quantum operations will be added. For example, Falcon appears to be one of the leading candidates with open-source code already available.

### Table format

The tables below have the following format:

Each table has 5 columns. These are as follows:

1) The Base64 Stable (hard) text code itself.
2) A description of what is encoded or appended to the code.
3) The length in characters of the code.
4) The length in characters of the count portion of the code.
5) The length in characters of the fully qualified Primitive, including code and its appended material or the number of elements in the group. This is empty when variable length.

### Universal Code tables

All code tables for every protocol genus/version shall implement the following tables:

#### Universal Code table genus/version codes

|   Code     | Description                       | Code Length | Count Length | Total Length |
|:----------:|:----------------------------------|:-----------:|:------------:|:------------:|
|            |  Universal Genus Version Codes |      |        |      |
|`--AAA###` | KERI/ACDC stack code table at genus `AAA` |      8      |       3\*      |       8     |

::: note Count Length of genus/version code
\* This isn't a count of items on the stream like others in the count code tables below.  Instead its the length of the characters wherein the total number of KERI/ACDC stack code genuses could exist (denoted by `###`).
:::

#### Universal Code table genus/version codes that allow genus/version override

All genera shall have the following codes in their Count Code table. Should the first Group Code embedded in each of these groups be a genus/version code, then the parser shall switch code tables to the code table given by that genus/version code. One of the codes in the following table supports this genus/version override. No other codes support this feature and are characterized as non-overrideable codes.

The presence of a genus/version count code that appears as the first element within the framed material of any non-overrideable count code (universal or not) has no special meaning as an override to the stream parser. In other words, the parser only treats the genus/version count code, especially as an override, when it appears as the first count code within the framed material of an overrideable universal count code. Otherwise, there is no special override meaning to the parser. To elaborate, the parser's interpretation of a genus/version code's presence as the first element of the framed material of a non-overrideable count code depends on the framed material context in which it appears.

For example, suppose some application uses a list (a universal but non-overrideable count code) with a genus/version code as its first element. From the perspective of the stream parser, the genus/version count code's appearance as the list's first element has no special override semantics, i.e., its presence provides no special override meaning to the parser.


|   Code     | Description                       | Code Length | Count Length | Total Length |
|:----------:|:----------------------------------|:-----------:|:------------:|:------------:|
|            |  Count Codes |     |        |       |
|            |  Universal Count Codes that allow genus/version override |       |        |        | 
|   `-A##`   | Generic pipeline group up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0A#####` | Generic pipeline group up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-B##`   | Message + attachments group up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0B#####` | Message + attachments group up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-C##`   | Attachments only group up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0C#####` | Attachments only group up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |

#### Universal Code table genus/version codes that do not allow genus/version override

All genera shall have the following codes in their Count Code table.

|   Code     | Description                       | Code Length | Count Length | Total Length |
|:----------:|:----------------------------------|:-----------:|:------------:|:------------:|
|            |  Universal Count Codes that do not allow genus/version override |     |      |    | 
|   `-D##`   | Datagram Stream Segment up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0D#####` | Datagram Stream Segment up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-E##`   | ESSR wrapper signable up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0E#####` | ESSR wrapper signable up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-F##`   | CESR native message top-level fixed field signable up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0F#####` | CESR native message top-level fixed field signable up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-G##`   | CESR native message top-level field map signable up to 4,095 quadlets/triplets    |      4      |       2      |       4      |
| `-0G#####` | CESR native message top-level field map signable up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-H##`   | Generic field map mixed types up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0H#####` | Generic field map mixed types up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-I##`   | Generic list mixed types up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0I#####` | Generic list mixed types up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |


### KERI/ACDC protocol stack tables
These tables are specific to the KERI/ACDC protocol genus.

#### KERI/ACDC protocol genus version table

|   Code     | Description                       | Code Length | Count Length | Total Length |
|:----------:|:----------------------------------|:-----------:|:------------:|:------------:|
|            |  Universal Genus Version Codes |      |        |      |
|`--AAABAA` | KERI/ACDC protocol stack code table at genus `AAA` and Version `1.00` |      8      |             |       8     |
|`--AAACAA` | KERI/ACDC protocol stack code table at genus `AAA` and Version `2.00` |      8      |             |       8     |

::: note Count Length of KERI protocol genus/version codes
Unlike the code in the Universal Code Selector Table above, these represent *specific* instantiations of the protocol genus codes so their count lengths are 0.
:::

#### Master code table for genus/version `--AAACAA` (KERI/ACDC protocol stack Version 2.00)

This master table includes both the Primitive and Count Code types for the KERI/ACDC protocol stack. This table only provides the codes for the KERI/ACDC protocol stack code table genus `AAA` at Version 2.00 given by the genus/version code = `--AAACAA` KERI/ACDC 2.00.  It is anticipated that the code tables for the KERI/ACDC/TSP protocol stack will not change much in the future after 2.00. Hopefully, there will never be a Version 3.00 because 2.00 was designed properly.

This master table includes both the Primitive and Count Code types. The types are separated by headers. 


|   Code     | Description                       | Code Length | Count Length | Total Length |
|:----------:|:----------------------------------|:-----------:|:------------:|:------------:|
|            | Count Codes |     |        |       |
|            |  Universal Genus Version Codes |      |        |      |
|`--AAABAA`  | KERI/ACDC protocol stack code table at genus `AAA` and Version `1.00`\* |     8      |             |       8     |
|`--AAACAA` | KERI/ACDC protocol stack code table at genus `AAA` and Version `2.00`\* |      8     |             |       8     |
|            |  Universal Count Codes that allow genus/version override |       |        |        | 
|   `-A##`   | Generic pipeline group up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0A#####` | Generic pipeline group up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-B##`   | Message + attachments group up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0B#####` | Message + attachments group up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-C##`   | Attachments only group up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0C#####` | Attachments only group up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|            |  Universal Count Codes that do not allow genus/version override |     |      |    | 
|   `-D##`   | Datagram Stream Segment up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0D#####` | Datagram Stream Segment up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-E##`   | ESSR wrapper signable up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0E#####` | ESSR wrapper signable up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-F##`   | CESR native message top-level fixed field signable up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0F#####` | CESR native message top-level fixed field signable up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-G##`   | CESR native message top-level field map signable up to 4,095 quadlets/triplets    |      4      |       2      |       4      |
| `-0G#####` | CESR native message top-level field map signable up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-H##`   | Generic field map mixed types up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0H#####` | Generic field map mixed type  up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-I##`   | Generic list mixed types up to 4,095 quadlets/triplets   |      4      |       2      |       4      |
| `-0I#####` | Generic list mixed types up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|            |  Genus Specific Count Codes |             |             |              | 
|   `-J##`   | Indexed controller signature group up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0J#####` | Indexed controller signature group up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-K##`   | Indexed witness signature group up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0K#####` | Indexed witness signature group up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-L##`   | Nontransferable identifier receipt couples pre+sig up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0L#####` | Nontransferable identifier receipt couples pre+sig up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-M##`   | Transferable identifier receipt quadruples pre+snu+dig+sig up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0M#####` | Transferable identifier receipt quadruples pre+snu+dig+sig up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-N##`   | First seen replay couples fnu+dt up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0N#####` | First seen replay couples fnu+dt up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-O##`   | Transferable indexed sig group pre+snu+dig+idx-controller-sig-groups up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0O#####` | Transferable indexed sig group pre+snu+dig+idx-controller-sig-groups up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-P##`   | Transferable last indexed sig group pre+idx-controller-sig-groups up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0P#####` | Transferable last indexed sig group pre+idx-controller-sig-groups up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-Q##`   | Issuer/Delegator/Transaction event seal source couple snu+dig up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0Q#####` | Issuer/Delegator/Transaction event seal source couple snu+dig up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-R##`   | Anchoring event seal source triple pre+snu+dig up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0R#####` | Anchoring event seal source triple pre+snu+dig up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-S##`   | Pathed material group path+mixed-types up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0S#####` | Pathed material group path+mixed-types up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-T##`   | SAD path signature group path+idx-controller-sig-groups up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0T#####` | SAD path signature group path+idx-controller-sig-groups up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-U##`   | SAD root path signature group rootpath+path-sig-groups up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0U#####` | SAD root path signature group rootpath+path-sig-groups up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-V##`   | Digest seal singles `dig` up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0V#####` | Digest seal singles `dig` up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-W##`   | Merkle Tree Root seal singles `rdig` up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0W#####` | Merkle Tree Root seal singles `rdig` up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-X##`   | Backer registrar identifier seal couples `brid+dig` up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0X#####` | Backer registrar identifier seal couples `brid+dig` up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-Y##`   | Last event seal source singles  `aid+dig` up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0Y#####` | Last event seal source singles  `aid+dig` up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|   `-Z##`   | ESSR (TSP) Payload `version+messagtype+...` up to 4,095 quadlets/triplets |      4      |       2      |       4      |
| `-0Z#####` | ESSR (TSP) Payload `version+messagtype+...` up to 1,073,741,823 quadlets/triplets |      8      |       5      |       8      |
|            |  Operation Codes   |             |              |              |
|   `_`      |      Reserved TBD  |             |              |              |
|            |  Primitive Matter Codes  |             |              |              |
|            |  Basic One Character Codes  |             |              |              |
|     `A`    | Seed of Ed25519 private key       |      1      |              |      44      |
|     `B`    | Ed25519 non-transferable prefix public verification key |      1      |              |      44      |
|     `C`    | X25519 public encryption key, may be converted from Ed25519 public key    |      1      |              |      44      |
|     `D`    | Ed25519 public verification key   |      1      |              |      44      |
|     `E`    | Blake3-256 Digest                 |      1      |              |     44       |
|     `F`    | Blake2b-256 Digest                |      1      |              |      44      |
|     `G`    | Blake2s-256 Digest                |      1      |              |      44      |
|     `H`    | SHA3-256 Digest                   |      1      |              |      44      |
|     `I`    | SHA2-256 Digest                   |      1      |              |      44      |
|     `J`    | Seed of ECDSA secp256k1 private key |      1      |              |      44      |
|     `K`    | Seed of Ed448 private key         |      1      |              |      76      |
|     `L`    | X448 public encryption key        |      1      |              |      76      |
|     `M`    | Short number 2-byte b2            |      1      |              |       4      |
|     `N`    | Big number 8-byte b2              |      1      |              |       12     |
|     `O`    | X25519 private decryption key/seed may be converted from Ed25519 key/seed     |      1      |              |       44     |
|     `P`    | X25519 124 char qb64 Cipher of 44 char qb64 Seed     |      1      |              |      124     |
|     `Q`    | ECDSA secp256r1 256-bit random Seed for private key    |      1      |              |      44     |
|     `R`    | Tall 5-byte b2 number |      1      |              |      8     |
|     `S`    | Large 11-byte b2 number    |      1      |              |      16     |
|     `T`    | Great 14-byte b2 number |      1      |              |      20     |
|     `U`    | Vast 17-byte b2 number    |      1      |              |      24     |
|     `V`    | Label1 1 bytes for label lead size 1 |      1      |              |      4    |
|     `W`    | Label2 2 bytes for label lead size 0 |      1      |              |      4    |
|     `X`    | Tag3 3 B64 encoded chars for special values |      1      |       3       |      4    |
|     `Y`    | Tag7 7 B64 encoded chars for special values  |      1      |        7      |      8   |
|     `Z`    | Blinding factor 256 bits, Cryptographic strength deterministically generated from random salt |      1      |              |      44 |         
| Basic Two Character Codes    |             |              |              |
|    `0A`    | Random salt, seed, nonce, private key, or sequence number of length 128 bits |      2      |              |      24      |
|    `0B`    | Ed25519 signature                 |      2      |              |      88      |
|    `0C`    | ECDSA secp256k1 signature         |      2      |              |      88      |
|    `0D`    | Blake3-512 Digest                 |      2      |              |      88      |
|    `0E`    | Blake2b-512 Digest                |      2      |              |      88      |
|    `0F`    | SHA3-512 Digest                   |      2      |              |      88      |
|    `0G`    | SHA2-512 Digest                   |      2      |              |      88      |
|    `0H`    | Long number 4-byte b2      |      2      |              |       8      |
|    `0I`    | ECDSA secp256r1 signature      |      2      |              |       88      |
|    `0J`    | Tag1 1 B64 encoded char + 1 prepad for special values |      2      |       2       |      4    |
|    `0K`    | Tag2 2 B64 encoded chars for for special values  |      2      |       2       |       4     |
|    `0L`    | Tag5 5 B64 encoded chars + 1 prepad for for special values     |      2      |        6      |       8     |
|    `0M`    | Tag6 6 B64 encoded chars for for special values  |      2      |      6        |       8     |
|    `0N`    | Tag9 9 B64 encoded chars + 1 prepad for special values |      2      |       10       |       12     |
|    `0O`    | Tag10 10 B64 encoded chars for special values |      2      |      10        |       12     |
|            |  Basic Four Character Codes   |             |              |              |
|   `1AAA`   | ECDSA secp256k1 non-transferable prefix public verification key   |      4      |              |      48      |
|   `1AAB`   | ECDSA secp256k1 public verification or encryption key |      4      |              |      48      |
|   `1AAC`   | Ed448 non-transferable prefix public verification key |      4      |              |      80      |
|   `1AAD`   | Ed448 public verification key     |      4      |              |      80      |
|   `1AAE`   | Ed448 signature                   |      4      |              |      156     |
|   `1AAF`   | Label3 3 bytes for label lead size 0 |      4      |              |      8       |
|   `1AAG`   | DateTime Base64 custom encoded 32 char ISO-8601 DateTime |      4      |              |      36      |
|   `1AAH`   | X25519 100 char b64 Cipher of 24 char qb64 Salt |      4      |              |      100      |
|   `1AAI`   | ECDSA secp256r1 verification key non-transferable, basic derivation |      4      |              |      48      |
|   `1AAJ`   | ECDSA secp256r1 verification or encryption key, basic derivation |      4      |              |     48     |
|   `1AAK`   | Null None or empty value |      4      |              |      4      |
|   `1AAL`   | No falsey Boolean value |      4      |              |      4      |
|   `1AAM`   | Yes truthy Boolean value|      4      |              |      4      |
|   `1AAN`   | Tag4 4 B64 encoded chars for special values |      4      |       4       |      8      |
|   `1AAO`   | Tag8 8 B64 encoded chars for special values |      4      |       8       |      12      |
|            |  Variable Raw Size Codes  |             |              |              |
|   `4A`     | String Base64 Only Lead Size 0      |      4      |      2        |            |
|   `5A`     | String Base64 Only Lead Size 1      |      4      |      2        |            |
|   `6A`     | String Base64 Only Lead Size 2      |      4      |      2        |            |
|   `7AAA`   | String Big Base64 Only Lead Size 0 |      8      |      4        |            |
|   `8AAA`   | String Big Base64 Only Lead Size 1 |      8      |      4        |            |
|   `9AAA`   | String Big Base64 Only Lead Size 2 |      8      |      4        |            |
|   `4B`     | Bytes Lead Size 0                  |      4      |      2        |            |
|   `5B`     | Bytes Lead Size 1                  |      4      |      2        |            |
|   `6B`     | Bytes Lead Size 2                  |      4      |      2        |            |
|   `7AAB`   | Bytes Big Lead Size 0              |      8      |      4        |            |
|   `8AAB`   | Bytes Big Lead Size 1              |      8      |      4        |            |
|   `9AAB`   | Bytes Big Lead Size 2              |      8      |      4        |            |
|   `4C`     | X25519 sealed box cipher bytes of sniffable plaintext lead size 0 |      4      |      2        |            |
|   `5C`     | X25519 sealed box cipher bytes of sniffable plaintext lead size 1 |      4      |      2        |            |
|   `6C`     | X25519 sealed box cipher bytes of sniffable plaintext lead size 2 |      4      |      2        |            |
|   `7AAC`   | X25519 sealed box cipher bytes of sniffable plaintext big lead size 0 |      8      |      4        |            |
|   `8AAC`   | X25519 sealed box cipher bytes of sniffable plaintext big lead size 1 |      8      |      4        |            |
|   `9AAC`   | X25519 sealed box cipher bytes of sniffable plaintext big lead size 2 |      8      |      4        |            |
|   `4D`     | X25519 sealed box cipher bytes of QB64 plaintext lead size 0 |      4      |      2        |            |
|   `5D`     | X25519 sealed box cipher bytes of QB64 plaintext lead size 1 |      4      |      2        |            |
|   `6D`     | X25519 sealed box cipher bytes of QB64 plaintext lead size 2 |      4      |      2        |            |
|   `7AAD`   | X25519 sealed box cipher bytes of QB64 plaintext big lead size 0 |      8      |      4        |            |
|   `8AAD`   | X25519 sealed box cipher bytes of QB64 plaintext big lead size 1 |      8      |      4        |            |
|   `9AAD`   | X25519 sealed box cipher bytes of QB64 plaintext big lead size 2 |      8      |      4        |            |
|   `4E`     | X25519 sealed box cipher bytes of QB2 plaintext lead size 0 |      4      |      2        |            |
|   `5E`     | X25519 sealed box cipher bytes of QB2 plaintext lead size 1 |      4      |      2        |            |
|   `6E`     | X25519 sealed box cipher bytes of QB2 plaintext lead size 2 |      4      |      2        |            |
|   `7AAE`   | X25519 sealed box cipher bytes of QB2 plaintext big lead size 0 |      8      |      4        |            |
|   `8AAE`   | X25519 sealed box cipher bytes of QB2 plaintext big lead size 1 |      8      |      4        |            |
|   `9AAE`   | X25519 sealed box cipher bytes of QB2 plaintext big lead size 2 |      8      |      4        |            |

::: note Count Length of KERI protocol genus/version codes in --AAACAA
\*Similar to the table above, these represent *specific* instantiations of the protocol genus codes so their count lengths are 0.

\--AAACAA may recieve a --AAABAA code on the stream but would have to parse with a table from the original reference implementation not included in this specification.
:::


#### Indexed code table for genus/version `--AAACAA` (KERI/ACDC protocol stack version 2.00)

|   Code    | Description                            | Code Length | Index Length | Ondex Length | Total Length |
|:---------:|:---------------------------------------|:-----------:|:------------:|:------------:|:------------:|
|           | Indexed Two Character Codes        |             |              |              |              |
|    `A#`   | Ed25519 indexed signature both same    |      2      |       1      |       0      |      88      |
|    `B#`   | Ed25519 indexed signature current only |      2      |       1      |       0      |      88      |
|    `C#`   | ECDSA secp256k1 indexed sig both same |      2      |       1      |       0      |      88      |
|    `D#`   | ECDSA secp256k1 indexed sig current only |      2      |       1      |       0      |      88      |
|           | Indexed Four Character Codes       |             |              |              |              |
|   `0A##`  | Ed448 indexed signature  dual          |      4      |       1      |       1      |      156     |
|   `0B##`  | Ed448 indexed signature current only   |      4      |       1      |       1      |      156     |
|           | Indexed Six Character Codes        |             |              |              |              |
| `2A####`  | Ed25519 indexed sig big dual           |      6      |       2      |       2      |      92      |
| `2B####`  | Ed25519 indexed sig big current only   |      6      |       2      |       2      |      92      |
| `2C####`  | ECDSA secp256k1 indexed sig big dual   |      6      |       2      |       2      |      92      |
| `2D####`  | ECDSA secp256k1 idx sig big current only  |      6      |       2      |       2      |      92      |
|           | Indexed Eight Character Codes      |             |              |              |              |
|`3A######` | Ed448 indexed signature big dual      |      8      |       3      |       3      |      160     |
|`3B######` | Ed448 indexed signature big current only |      8      |       3      |       3      |      160     |

Legend:

| Short name | Description |
|:----------:|:-----------:|
| `pre` | Prefix |
| `sn` | Sequence number |
| `dig` | Digest |
| `sig` | Signature |
| `fn` | First seen number |
| `idx` | Index |
| `dt` | DateTime |

#### Examples

The tables above include complex groups that maybe composed of other groups. For example, consider the counter attachment group with code `-F##` where `##` is replaced by the two-character Base64 count of the number of complex groups. This is known as the TransIndexedSigGroups counter.  Within the complex group are one or more attached
groups where each group consists of a triple pre+snu+dig followed by a ControllerIdxSigs group that in turn, consists of a Count Code `-A##` followed by one or more indexed signature Primitives.

The following example details how a complex nested group may appear.

The example has only one group that includes nested groups. The example is annotated with comments, spaces, and line feeds for clarity.

```text
-FAB     # Trans Indexed Sig Groups Count Code 1 following group
E_T2_p83_gRSuAYvGhqV3S0JzYEF2dIa-OCPLbIhBO7Y    # trans prefix of signer for sigs
-EAB0AAAAAAAAAAAAAAAAAAAAAAB    # sequence number of est event of signer's public keys for sigs
EwmQtlcszNoEIDfqD-Zih3N6o5B3humRKvBBln2juTEM    # digest of est event of signer's public keys for sigs
-AAD     # Controller Indexed Sigs Count Code 3 following sigs
AA5267UlFg1jHee4Dauht77SzGl8WUC_0oimYG5If3SdIOSzWM8Qs9SFajAilQcozXJVnbkY5stG_K4NbKdNB4AQ # sig 0
ABBgeqntZW3Gu4HL0h3odYz6LaZ_SMfmITL-Btoq_7OZFe3L16jmOe49Ur108wH7mnBaq2E_0U0N0c5vgrJtDpAQ # sig 1
ACTD7NDX93ZGTkZBBuSeSGsAQ7u0hngpNTZTK_Um7rUZGnLRNJvo5oOnnC1J2iBQHuxoq8PyjdT3BHS2LiPrs2Cg # sig 2
```

### Version String field

Non-CESR serializations, namely, JSON, CBOR, and MGPK when interleaved in a CESR Stream shall have a Version String as their first field with field label, `v` (lower case "v"). The Version String field value enables the Stream parser to use a regular expression parser to determine the type and length of the interleaved serialization. See the section on cold start stream processing section above for more detail on how a stream parser detects when to perform a regular expression search for a version string in a JSON, CBOR, or MGPK serialization interleaved in a CESR stream.

##### Version 2.XX string field format

The Version String, `v` field shall be the first field in any top-level field map of any interleaved JSON, CBOR, or MGPK serialization. It provides a regular expression target for determining a serialized field map's serialization format and size (character count) of its enclosing field map. A Stream parser may use the Version String to extract and deserialize (deterministically) any serialized Stream field maps. Each field map in a Stream may use a different serialization type from the JSON, CBOR, or MGPK set.

The format of the Version String is `PPPPVVVKKKKBBBB.`. It is 16 characters in length and is divided into five parts: 
* Protocol: `PPPP` four character version string (for example, `KERI` or `ACDC`)
* Version: `VVV` three character major minor version (described below)
* Serialization kind: `KKKK` four character string of the types (`JSON`, `CBOR`, `MGPK`, `CESR`)
* Serialization length: `BBBB` integer encoded in Base64
* version 2.XX terminator character `.`

The first four characters, `PPPP` indicate the protocol. Each genus of a given CESR code table set may support multiple protocols.  

The next three characters, `VVV`, provide in Base64 notation the major and minor version numbers of the Version of the protocol specification. The first `V` character provides the major version number, and the final two `VV` characters provide the minor version number. For example, `CAA` indicates major version 2 and minor version 00 or in dotted-decimal notation, i.e., `2.00`. Likewise, `CAQ` indicates major version 2 and minor version decimal 16 or in dotted-decimal notation `1.16`. The Version part supports up to 64 major versions with 4096 minor versions per major version. 

::: warning non-canonical base64
This is a non-canonical encoding using Base64 indicies. Most [[spec: RFC4648]]-compliant libraries will drop bits that aren't on a byte boundary if you just call decode on these characters naively.

For example, in python (with padding character for demonstration), using a semantic version 2.00 that would map to "CAA" in our scheme as above.
```python
>>> base64.urlsafe_b64decode("CAA=")
b'\x08\x00'
```

Which is two bytes.  However, there are three base64 characters in this version scheme which encode 18 bits.  base64 encoding works on 6-bit groupings so: `C -> 0b000010, A -> 0b000000, A -> 0b000000` which is two bytes + two bits when concatenated together.  In the python example above we get back `b'\x08\x00'` -> `'0b00001000 0b00000000'` which is two bytes (16 bits) in hexidecimal notation.  The canonical decoding by the library is stripping the last two bits per the RFC.  Implementers should thus use a library capable of getting the index of the b64 characters according to the scheme (for this version string only) and not those written to give canonical decodings.

See https://datatracker.ietf.org/doc/html/rfc4648#section-3.5
:::

The next four characters, `KKKK` indicate the serialization kind in uppercase. The four supported serialization kinds are `JSON`, `CBOR`, `MGPK`, and `CESR` for the JSON, CBOR, MessagePack, and CESR serialization standards, respectively [[spec: RFC4627]] [[spec: RFC4627]] [[spec: RFC8949]] [[ref: RFC8949]] [[3]] [[ref: CESR]]. The last one, CESR is special. A CESR native serialization of a field map may use either the    `-G##` or  `-0G#####` count codes to indicate both that it is a field map and its size. Moreover, because count codes have unique start bits (see the section on Performant resynchronization) there is no need to embed a regular expression parsable version string field in a CESR native field map. Instead, a native CESR message's field map includes a protocol version field that indicates the protocol and version but not the size and serialization type. These are provided already by the count code. As a result, once deserialized into an in-memory data object representation of that field map, there is no normative indication that the in-memory object was deserialized from a  CESR native field map (i.e. no version string field with serialization kind).   This serialization kind indication would otherwise have to be provided externally.  Instead, the in-memory object representation of the field map may inject a placeholder version string, `v` field, whose value is a version string but with the serialization kind set to `CESR`. This way, when re-serializing, there is a normative indicator to reserialize as a CESR native field map, not JSON, CBOR, or MGPK.  This reserialization does not include an embedded version string field. It only appears in the in-memory object representation, not the serialization.

The next four characters, `BBBB`, provide in Base64 notation the total length of the serialization, inclusive of the Version String and any prefixed characters or bytes. This length is the total number of characters in the serialization of the field map. The maximum length of a given field map serialization is thereby constrained to be 64<sup>4</sup> = 2<sup>24</sup> = 16,777,216 characters in length. This is deemed generous enough for the vast majority of anticipated applications. For serializations that may exceed this size, a secure hash chain of Messages may be employed where the value of a field in one Message is the cryptographic digest, SAID of the following Message. The total size of the chain of Messages may, therefore, be some multiple of 2<sup>24</sup>.

The final character `.` is the Version String terminator. This enables later Versions of a protocol to change the total Version String size and thereby enable versioned changes to the composition of the fields in the Version String while preserving deterministic regular expression extractability of the Version String. 

Although a given field map serialization kind may have characters or bytes such as field map delimiters or Framing Codes that appear before, i.e., prefix the Version String field in a serialization, the set of possible prefixes for each of the supported serialization kinds is sufficiently constrained by the allowed serialization protocols to guarantee that a regular expression can determine unambiguously the start of any ordered field map serialization that includes the Version String as the first field value. Given the length of the serialization provided by the Version String, a parser may then determine the end of the serialization to extract the full field map serialization from the Stream without first deserializing it. This enables performant Stream parsing and off-loading of Streams that include any or all of the supported serialization types.

##### Legacy Version 1.XX string field format

Compliant Version 2.XX implementations shall support the old Version 1.XX Version String format to properly verify field maps created with 1.XX format events.

The format of the Version String for version 1.XX is `PPPPvvKKKKllllll_`. It is 17 characters in length and is divided into five parts: 
* Protocol: `PPPP` four character version string (for example, `KERI` or `ACDC`)
* Version: `vv` twocharacter major minor version (described below)
* Serialization kind: `KKKK` four character string of the types (`JSON`, `CBOR`, `MGPK`, `CESR`)
* Serialization length: `llllll` integer encoded in lowercase hexidecimal (Base 16) format
* legacy version terminator character `_`

The first four characters, `PPPP` indicate the protocol.  

The next two characters, `vv` provide the major and minor version numbers of the Version of the protocol specification in lowercase hexadecimal notation. The first `v` provides the major version number, and the second `v` provides the minor version number. For example, `01` indicates major version 0 and minor version 1 or in dotted-decimal notation `0.1`. Likewise, `1c` indicates major version 1 and minor version decimal 12 or in dotted-decimal notation `1.12`.

 The next four characters, `KKKK` indicate the serialization kind in uppercase. The four supported serialization kinds are `JSON`, `CBOR`, `MGPK`, and `CESR` for the JSON, CBOR, MessagePack, and CESR serialization standards, respectively [[spec: RFC4627]] [[spec: RFC4627]] [[spec: RFC8949]] [[ref: RFC8949]] [[3]] [[ref: CESR]]. 

The next six characters provide in lowercase hexadecimal notation the total length of the serialization, inclusive of the Version String and any prefixed characters or bytes. This length is the total number of characters in the serialization of the field map. The maximum length of a given field map serialization is thereby constrained to be 16<sup>6</sup> = 2<sup>24</sup> = 16,777,216 characters in length. For example, when the length of serialization is 384 decimal characters/bytes, the length part of the Version String has the value `000180`. The final character `_` is the Version String terminator. This enables later Versions of the protocol to change the total Version String size and thereby enable versioned changes to the composition of the fields in the Version String while preserving deterministic regular expression extractability of the Version String. 


### Self-addressing identifier (SAID)

A SAID (Self-Addressing Identifier) is a special type of content-addressable identifier based on encoded cryptographic digest that is self-referential. The SAID derivation protocol defined herein enables verification that a given SAID is uniquely cryptographically bound to a serialization that includes the SAID as a field in that serialization. Embedding a SAID as a field in the associated serialization indicates a preferred content-addressable identifier for that serialization that facilitates greater interoperability, reduced ambiguity, and enhanced security when reasoning about the serialization. Moreover, given sufficient cryptographic strength, a cryptographic commitment such as a signature, digest, or another SAID, to a given SAID is essentially equivalent to a commitment to its associated serialization. Any change to the serialization invalidates its SAID thereby ensuring secure immutability evident reasoning with SAIDs about serializations or equivalently their SAID. Thus SAIDs better facilitate immutably referenced data serializations for applications such as Verifiable Credentials or Ricardian Contracts.

SAIDs are encoded with CESR [CESR] which includes a pre-pended derivation code that encodes the cryptographic suite or algorithm used to generate the digest. A CESR Primitive's primary expression (alone or in combination) is textual using Base64 URL-safe characters. CESR Primitives may be round-tripped (alone or in combination) to a compact binary representation without loss. The CESR derivation code enables cryptographic digest algorithm agility in systems that use SAIDs as content addresses. Each serialization may use a different cryptographic digest algorithm as indicated by its derivation code. This provides interoperable future proofing. CESR was developed for the KERI protocol.

The primary advantage of a content-addressable identifier is that it is cryptographically bound to the content (expressed as a serialization), thus providing a secure root-of-trust for reasoning about that content. Any sufficiently strong cryptographic commitment to a content-addressable identifier is functionally equivalent to a cryptographic commitment to the content itself.

A  SAID is a special class of content-addressable identifier that is also self-referential. This requires a special derivation protocol that generates the SAID and embeds it in the serialized content.  The reason for a special derivation protocol is that a naive cryptographic content-addressable identifier must not be self-referential, i.e., the identifier must not appear within the content that it is identifying. This is because the naive cryptographic derivation process of a content-addressable identifier is a cryptographic digest of the serialized content. Changing one bit of the serialization content will result in a different digest. Therefore, self-referential content-addressable identifiers require a special derivation protocol.

To elaborate, this approach of deriving self-referential identifiers from the contents they identify, is called `self-addressing`. It allows any validator to verify or re-derive the `self-referential, self-addressing identifier` given the contents it identifies. To clarify, a SAID is different from a standard content address or content-addressable identifier in that a standard content-addressable identifier may not be included inside the contents it addresses. Moreover, a standard content-addressable identifier is computed on the finished immutable contents, and therefore is not self-referential. In addition, a SAID includes a pre-pended derivation code that specifies the cryptographic algorithm used to generate the digest.

An authenticatable data serialization is defined to be a serialization that is digitally signed with a non-repudiable asymmetric key-pair based signing scheme. A Verifier, given the public key, may verify the signature on the serialization and thereby securely attribute the serialization to the signer. Many use cases of authenticatable data serializations or statements include a self-referential identifier embedded in the authenticatable serialization. These serializations may also embed references to other self-referential identifiers to other serializations. The purpose of a self-referential identifier is to enable reasoning in software or otherwise about that serialization.  Typically, these self-referential identifiers are not cryptographically bound to their encompassing serializations such as would be the case for a content-addressable identifier of that serialization. This poses a security problem because there now may be more than one identifier for the same content. The first is self-referential, included in the serialization, but not cryptographically bound to its encompassing serialization and the second is cryptographically bound but not self-referential, not included in the serialization.

When reasoning about a given content serialization, the existence of a non-cryptographically bound but self-referential identifier is a security vulnerability. Certainly, this identifier cannot be used by itself to securely reason about the content because it is not bound to the content. Anyone can place such an identifier inside some other serialization and claim that the other serialization is the correct serialization for that self-referential identifier.  Unfortunately, a standard content-addressable identifier for a serialization which is bound to the serialization cannot be included in the serialization itself, i.e,. can be neither self-referential nor self-contained; it must be tracked independently. In contrast, a `self-addressing identifier` is included in the serialization to which it is cryptographically bound making it self-referential and self-contained. Reasoning about SAIDs is secure because a SAID will verify if and only if its encompassing serialization has not been mutated, which makes the content immutable. SAIDs used as references to serializations in other serializations enable tamper-evident reasoning about the referenced serializations. This enables a more compact representation of an authenticatable data serialization that includes other serializations by reference to their SAIDs instead of by embedded containment.

#### Generation and Verification Protocols

The SAID verification protocol is as follows:

- Make a copy of the embedded `CESR` [CESR] encoded SAID string included in the serialization.
- replace the SAID field value in the serialization with a dummy string of the same length. The dummy character is `#`, that is, ASCII 35 decimal (23 hex).
- Compute the digest of the serialization that includes the dummy value for the SAID field. Use the digest algorithm specified by the CESR [CESR] derivation code of the copied SAID.
- Encode the computed digest with CESR [CESR] to create the final derived and encoded SAID of the same total length as the dummy string and the copied embedded SAID.
- Compare the copied SAID with the recomputed SAID. If they are identical then the verification is successful; otherwise, unsuccessful.


##### Example Computation
The CESR [CESR] encoding of a Blake3-256 (32 byte) binary digest has 44 Base-64 URL-safe characters. The first character is `E` which represents Blake3-256. Therefore, a serialization of a fixed field data structure with a SAID generated by a Blake3-256 digest must reserve a field of length 44 characters. Suppose the initial value of the fixed field serialization is the following string:


```
field0______field1______________________________________field2______
```

In the string, `field1` is of length 44 characters. The first step to generating the SAID for this serialization is to replace the contents of `field1` with a dummy string of `#` characters of length 44 as follows:

```
field0______############################################field2______
```
The Blake3-256 digest is then computed on the above string and encoded in CESR format. This gives the following SAID:

```
E8wYuBjhslETYaLZcxMkWrhVbMcA8RS1pKYl7nJ77ntA
```

The dummy string is then replaced with the SAID above to produce the final serialization with embedded SAID as follows:

```
field0______E8wYuBjhslETYaLZcxMkWrhVbMcA8RS1pKYl7nJ77ntA______
```

To verify the embedded SAID with respect to its encompassing serialization above, just reverse the generation steps.

##### Serialization Generation

###### Order-Preserving Data Structures
The crucial consideration in SAID generation is reproducibility. This requires the ordering and sizing of fields in the serialization to be fixed. Data structures in most computer languages have fixed fields. The example above is such an example.

A very useful type of serialization especially in some languages like Python or JavaScript is of self-describing data structures that are mappings of (key, value) or (label, value) pairs. These are often also called dictionaries or hash tables. The essential feature needed for reproducible serialization of such mappings is that mapping preserve the ordering of its fields on any round trip to/from a serialization. In other words, the mapping is ordered with respect to serialization. Another way to describe a predefined order preserving serialization is canonicalization or canonical ordering. This is often referred to as the mapping canonicalization problem.

The natural canonical ordering for such mappings is insertion order or sometimes called field creation order. Natural order allows the fields to appear in a preset order independent of the lexicographic ordering of the labels. This enables functional or logical ordering of the fields. Logical ordering also allows the absence or presence of a field to have meaning. Fields may have a priority given by their relative order of appearance. Fields may be grouped in logical sequence for better usability and labels may use words that best reflect their function independent of their relative lexicographic ordering. The most popular serialization format for mappings is JSON. Other popular serializations for mappings are CBOR and MessagePack.

In contrast, from a functional perspective, lexicographic ordering appears un-natural. In lexicographic ordering the fields are sorted by label prior to serialization.  The problem with lexicographic ordering is that the relative order of appearance of the fields is determined by the labels themselves not some logical or functional purpose of the fields themselves. This often results in oddly-labeled fields that are so named merely to ensure that the lexicographic ordering matches a given logical ordering.

Originally mappings in most if not all computer languages were not insertion order preserving. The reason is that most mappings used hash tables under the hood. Early hash tables were highly efficient but by nature did not include any mechanism for preserving field creation or field insertion order for serialization. Fortunately, this is no longer true in general. Most if not all computer languages that support dictionaries or mappings as first-class data structures now support variations that are insertion order preserving.

For example, since Version 3.6 the default `dict` object in Python is insertion order preserving. Before that, Python 3.1 introduced the `OrderedDict` class which is insertion order preserving, and before that, custom classes existed in the wild for order preserving variants of a Python `dict`. Since Version 1.9 the Ruby version of a `dict`, the `Hash` class, is insertion order preserving. Javascript is a relative latecomer but since ECMAScript `ES6` the insertion ordering of JavaScript objects was preserved in `Reflect.ownPropertyKeys()`. Using custom `replacer` and `reviver` functions in `.stringify` and `.parse` allows one to serialize and de-serialize JavaScript objects in insertion order. Moreover, since ES11 the native `.stringify` uses insertion order all text string labeled fields in Javascript objects. It is an uncommon use case to have non-text string labels in a mapping serialization. A list is usually a better structure in those cases. Nonetheless, since ES6 the new Javascript `Map` object preserves insertion order for all fields for all label types. Custom `replacer` and `reviver` functions for `.stringify` and `.parse` allows one to serialize and de-serialize Map objects to/from JSON in natural order preserving fashion. Consequently, there is no need for any canonical serialization but natural insertion order preserving because one can always use lexicographic ordering to create the insertion order.

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

As before, the SAID will be a 44-character CESR encoded Blake3-256 digest. The serialization will be *JSON*. The`said` field value in the `dict` is to be populated with the resulting SAID. First the value of the `said` field is replaced with a 44-character dummy string as follows:

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

The Blake3-256 digest is then computed on that serialization above and encoded in CESR to provide the SAID as follows:

```
EnKa0ALimLL8eQdZGzglJG_SxvncxkmvwFDhIyLFchUk
```

The value of the `said` field is now replaced with the computed and encoded SAID to produce the final serialization with embedded SAID as follows:

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

The generation steps may be reversed to verify the embedded SAID. The SAID generation and verification protocol for mappings assumes that the fields in a mapping serialization such as JSON are ordered in Stable, round-trippable, reproducible order, i.e., canonical. The natural canonical ordering is called field insertion order.

#### Example Schema Immutability using JSON Schema with SAIDs

SAIDs make [JSON Schema](https://json-schema.org/draft/2020-12/json-schema-core.html) fully self-contained with self-referential, unambiguously cryptographically bound, and verifiable content-addressable identifiers. The SAID derivation protocol defined above is applied to generate the `$id` field.

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
    }
```

Usages of SAIDs within authentic data containers as demonstrated here are referred to as self-addressing data (SAD).

##### Discussion
As long as any verifier recognizes the derivation code of a SAID, the SAID is a cryptographically secure commitment to the contents in which it is embedded; it is a cryptographically verifiable, self-referential, content-addressable identifier. Because a SAID is both self-referential and cryptographically bound to the contents it identifies, anyone can validate this binding if they follow the _derivation protocol_ outlined above.


To elaborate, this approach of deriving self-referential identifiers from the contents they identify, is called `self-addressing`. It allows any validator to verify or re-derive the self-referential, self-addressing identifier given the contents it identifies. To clarify, a SAID is different from a standard content address or content-addressable identifier in that a standard content-addressable identifier may not be included inside the contents it addresses. Moreover, a standard content-addressable identifier is computed on the finished immutable contents, and therefore is not self-referential.

### Self-addressing Data (SAD) Path Signatures

SAD Path signatures are an extension to CESR that provide transposable cryptographic signature attachments on self-addressing data (SAD) [[ref: SAID]]. Any SAD, such as an Authentic Chained Data Container (ACDC) Verifiable Credential [[ref: ACDC]] for example, may be signed with a SAD Path Signature and streamed along with any other CESR content.  In addition, a signed SAD can be embedded inside another SAD and the SAD Path signature attachment can be transposed across envelope boundaries and streamed without losing any cryptographic integrity.

CESR is a dual text-binary encoding format that has the unique property of text-binary concatenation composability. The CESR specification not only provides the definition of the streaming format but also the attachment codes needed for differentiating the types of cryptographic material (such as signatures) used as attachments on all event types for the KERI [[1]]. While all KERI event Messages are SADs, there is a broad class of SADs that are not KERI events but that require signature attachments. ACDC Verifiable Credentials fit into this class of SADs. With more complex data structures represented as SADs, such as Verifiable Credentials, there is a need to provide signature attachments on nested subsets of SADs. Similar to indices in indexed controller signatures in KERI that specify the location of the public key that they represent, nested SAD signatures need a path mechanism to specify the exact location of the nested content that they are signing. SAD Path Signatures provide this mechanism with the CESR SAD Path Language and new CESR attachment codes are detailed in this specification.

#### Streamable SADs
A primary goal of SAD Path Signatures is to allow any signed SAD to be streamed inline with any other CESR content.  In support of that goal, SAD Path Signatures leverage CESR attachments to define a signature scheme that can be attached to any SAD content serialized as JSON [[spec: RFC4627]], MessagePack [[3]] or CBOR [[spec: RFC8949]].  Using this capability, SADs signed with SAD Path Signatures can be streamed inline in either the Text (T) or Binary (B) domain alongside any other KERI event Message over, for example TCP or UDP.  In addition, signed SADs can be transported via HTTP as a CESR HTTP Request.

#### Nested Partial Signatures
SAD Path Signatures can be used to sign as many portions of a SAD as needed, including the entire SAD. The signed subsets are either SADs themselves or the SAID of a SAD that will be provided out of band.  A new CESR Count Code is included with this specification to allow for multiple signatures on nested portions of a SAD to be grouped together under one attachment.  By including a SAD Path in the new CESR attachment for grouping signatures, the entire group of signatures can be transposed across envelope boundaries by changing only the root path of the group attachment code.

#### Transposable Signature Attachments

There are several events in KERI that can contain context specific embedded SADs. Exchange events (`exn`) for peer-to-peer communication and Replay events (`rpy`) for responding to data requests as well as Expose events (`exp`) for providing anchored data are all examples of KERI events that contain embedded SADs as part of their payload. If the SAD payload for one of these event types is signed with a CESR attachment, the resulting structure is not embeddable in one of the serializations of map or dictionary like data models. (JSON, CBOR, MessagePack) supported by CESR. To solve this problem, SAD Path Signatures are transposable across envelope boundaries in that a single SAD signature or an entire signature group on any given SAD can be transposed to attach to the end of an enveloping SAD without losing its meaning. This unique feature is provided by the SAD Path language used in either a SAD signature or the root path designation in the outermost attachment code of any SAD signature group.  These paths can be updated to point to the embedded location of the signed SAD inside the envelope. Protocols for verifiable credential issuance and proof presentation can be defined using this capability to embed the same verifiable credential SAD at and location in an enveloping `exn` Message as appropriate for the protocol without having to define a unique signature scheme for each protocol.

### SAD Path Language

SAD Path Signatures defines a SAD Path Language to be used in signature attachments for specifying the location of the SAD content within the signed SAD that a signature attachment is verifying. This path language has a more limited scope than alternatives like JSONPtr [[spec: RFC6901]] or JSONPath [[ref: JSONPath]] and is therefore simpler and more compact when encoding in CESR signature attachments. SADs in CESR and therefore SAD Path Signatures require static field ordering of all maps. The SAD path language takes advantage of this feature to allow for a Base64 compatible syntax into SADs even when a SAD uses non-Base64 compatible characters for field labels.

#### Description and Usage

The SAD path language contains a single reserved character, the `-` (dash) character. Similar to the `/` (forward slack) character in URLs, the `-` in the SAD Path Language is the path separator between components of the path. The `-` was selected because it is a one of the valid Base64 characters.

The simplest path in the SAD Path Language is a single `-` character representing the root path which specifies the top level of the SAD content.

Root Path

```
 -
```

After the root path, path components follow, delimited by the `-` character. Path components may be integer indices into field labels or arrays or may be full field labels. No wildcards are supported by the SAD Path Language.

An example SAD Path using only labels that resolve to map contexts follows:

```
-a-personal
```

In addition, integers can be specified and their meaning is dependent on the context of the SAD.

```
-1-12-personal-0
```

The rules for a SAD Path Language processor are simple. If a path consists of only a single `-`, it represents the root of the SAD and therefore the entire SAD content. Following any `-` character is a path component that points to a field if the current context is a map in the SAD or is an index of an element if the current context is an array. It is an error for any sub-path to resolve to a value this is not a map or an array.  Any trailing `-` character in a SAD Path can be ignored.

The root context (after the initial `-`) is always a map. Therefore, the first path component represents a field of that map. The SAD is traversed following the path components as field labels or indexes in arrays until the end of the path is reached. The value at the end of the path is then returned as the resolution of the SAD Path. If the current context is a map and the path component is an integer, the path component represents an index into fields of the map. This feature takes advantage of the static field ordering of SADs and is used against any SAD that contains field labels that use non-Base64 compatible characters or the `-` character. Any combination of integer and field label path components can be used when the current context is a map. All path components MUST be an integer when the current context is an array.

#### CESR Encoding for SAD Path Language
SAD Paths are variable raw size Primitives that require CESR variable size codes.  The `A` small variable size code for SAD Paths will be used which has 3 code entries being added to the Master Code Table, `4A##`, `5A##` and `6A##` for SAD Paths with 0 lead bytes, 1 lead byte and 2 lead bytes respectively.  This small variable size code is reserved for text values that only contain valid Base64 characters.  These codes are detailed in Table 2 below.  The selector not only encodes the table but also implicitly encodes the number of lead bytes. The variable size is measured in quadlets of 4 characters each in the T domain and equivalently in triplets of 3 bytes each in the B domain. Thus, computing the number of characters when parsing or off-loading in the T domain means multiplying the variable size by 4. Computing the number of bytes when parsing or off-loading in the B domain means multiplying the variable size by 3. The two Base64 size characters provide value lengths in quadlets/triplets from 0 to 4095 (64**2 -1). This corresponds to path lengths of up to 16,380 characters (4095 * 4) or 12,285 bytes (4095 * 3).

#### SAD Path Examples

This section provides some more examples for SAD Path expressions. The examples are based on Authentic Chained Data Containers (ACDCs) representing Verifiable Credentials.

```json
{
  "v": "ACDC10JSON00011c_",
  "d": "EBdXt3gIXOf2BBWNHdSXCJnFJL5OuQPyM5K0neuniccM",
  "i": "EmkPreYpZfFk66jpf3uFv7vklXKhzBrAqjsKAn2EDIPM",
  "s": "E46jrVPTzlSkUPqGGeIZ8a8FWS7a6s4reAXRZOkogZ2A",
  "a": {
    "d": "EgveY4-9XgOcLxUderzwLIr9Bf7V_NHwY1lkFrn9y2PY",
    "i": "EQzFVaMasUf4cZZBKA0pUbRc9T8yUXRFLyM1JDASYqAA",
    "dt": "2021-06-09T17:35:54.169967+00:00",
    "ri": "EymRy7xMwsxUelUauaXtMxTfPAMPAI6FkekwlOjkggt",
    "LEI": "254900OPPU84GM83MG36",
    "personal": {
      "legalName": "John Doe",
      "home-city": "Durham"
    }
  },
  "p": [
    {
      "qualifiedIssuerCredential": {
        "d": "EIl3MORH3dCdoFOLe71iheqcywJcnjtJtQIYPvAu6DZA",
        "i": "Et2DOOu4ivLsjpv89vgv6auPntSLx4CvOhGUxMhxPS24"
      }
    },
    {
      "certifiedLender": {
        "d": "EglG9JLG6UhkLrrv012NPuLEc1F3ne5vPH_sHGP_QPN0",
        "i": "E8YrUcVIqrMtDJHMHDde7LHsrBOpvN38PLKe_JCDzVrA"
      }
    }
  ]
}
```

Figure 1. Example ACDC Credential SAD

The examples in Table 1 represent all the features of the SAD Path language when referring to the SAD in Figure 1. along with the CESR text encoding.

|   SAD Path   | Result                            | CESR T domain Encoding |
|:-------------|:----------------------------------|:------|
|  -           | The root of the SAD               | 6AABAAA- |
|  -a-personal | The personal map of the a field   | 4AADA-a-personal |
|  -4-5        | The personal map of the a field   | 4AAB-4-5 |
|  -4-5-legalName        | "John Doe"   | 5AAEAA-4-5-legalName |
|  -a-personal-1        | "Durham"   |  6AAEAAA-a-personal-1 |
|  -p-1        | The second element in the p array | 4AAB-p-1 |
|  -a-LEI     | "254900OPPU84GM83MG36" | 5AACAA-a-LEI |
| -p-0-0-d     | "EIl3MORH...6DZA" | 4AAC-p-0-0-d |
| -p-0-certifiedLender-i | "E8YrUcVI...zVrA" | 5AAGAA-p-0-certifiedLender-i |

#### Alternative Pathing / Query Languages
The SAD Path language was chosen over alternatives such as JSONPtr and JSONPath in order to create a more compact representation of a pathing language in the text domain.  Many of the features of the alternatives are not needed for SAD Path Signatures.  The only token in the language (`-`) is Base64 compatible.  The use of field indices in SADs (which require statically ordered fields) allows for Base64 compatible pathing even when the field labels of the target SAD are not Base64 compatible.  The language accomplishes the goal of uniquely locating any path in a SAD using minimally sufficient means in a manner that allows it to be embedded in a CESR attachment as Base64.  Alternative syntaxes would need to be Base64 encoded to be used in a CESR attachment in the text domain thus incurring the additional bandwidth cost of such an encoding.

### CESR Attachments

This specification adds 2 Counter Four Character Codes to the Master Code Table and uses 1 Small Variable Raw Size Code Type and 1 Large Variable Raw Size Code Type from the Master Code Table (each of which have 3 code entries).

#### Counter Four Character Codes
The SAD Path Signature Count Code is represented by the four-character code `-J##`.  The first two characters reserve this code for attaching the couplet (SAD Path, Signature Group).  The second two characters represent the count in hexadecimal of the SAD path signatures are in this attachment.  The path is attached in the T domain using the codes described in the next section.  The signature group is from either a transferable identifier or a non-transferable identifier and therefore attached using the CESR codes `-F##` or `-C##`, respectively, as described above.

#### Variable Size Codes
The code `A` is reserved as a Small Variable Raw Size Code and `AAA` as a Large Variable Raw Size Code for Base64 URL safe strings.  SAD Paths are Base64 URL safe strings and so leverage these codes when encoded in the CESR T domain.  To account for the variable nature of path strings, the variable size types reserve 3 codes each with prefix indicators of lead byte size used for adjusting the T domain encoding to multiples of 4 characters and the B domain to multiples of 3 bytes.  For the Small codes the prefix indicators are `4`, `5` and `6` representing 0, 1 and 2 lead bytes respectively and for Large codes the prefix indicators are `7`, `8`, and `9` representing 0, 1 and 2 lead bytes respectively.  The resulting 6 code entries are displayed in the table that follows.

The additions to the Master Code Table of CESR is shown below:

|   Code   | Description                                                                         | Code Length | Count or Index Length | Total Length |
|:--------:|:----------------------------------|:------------:|:-------------:|:------------:|
|          |                        Counter Four Character Codes                         |             |              |              |
|   -J##   | Count of attached qualified Base64 SAD path sig groups path+sig group (trans or non-trans)                       |      2      |       2      |       4      |
|   -K##   | Count of attached qualified Base64 SAD Path groups                    |      2      |       2      |       4      |
|          |                        Small Variable Raw Size Code                         |             |              |              |
|   4A##   | String Base64 Only with 0 Lead Bytes                                                  |      2      |       2      |       4      |
|   5A##   | String Base64 Only with 1 Lead Byte                                                   |      2      |       2      |       4      |
|   6A##   | String Base64 Only with 2 Lead Bytes                                                  |      2      |       2      |       4      |
|          |                        Large Variable Raw Size Code                         |             |              |              |
|   7AAA####   | String Base64 Only with 0 Lead Bytes                                                  |      4      |       4      |       8      |
|   8AAA####   | String Base64 Only with 1 Lead Byte                                                   |      4      |       4      |       8      |
|   9AAA####   | String Base64 Only with 2 Lead Bytes                                                  |      4      |       4      |       8      |

#### SAD Path Signature Attachments
CESR defines several Count Codes for attaching signatures to serialized CESR event Messages.  For KERI event Messages, the signatures in the attachments apply to the entire serialized content of the KERI event Message.  As all KERI event Messages are SADs, the same rules for signing a KERI event Message applies to signing SADs for SAD Path Signatures.  A brief review of CESR signatures for transferable and non-transferable identifiers follows.  In addition, signatures on nested content must be specified.

##### Signing SAD Content

Signatures on SAD content require signing the serialized encoding format of the data ensuring that the signature applies to the data over the wire.  The serialization for any SAD is identified in the Version String which can be found in the `v` field of any KERI event Message or ACDC credential.   An example Version String follows:

```json
 {
     "v": "KERICAAJSONAAQB."
 }
```

where KERI is the identifier of KERI events followed by the hexadecimal major and minor version code and then the serialized encoding format of the event, JSON in this case.  KERI and ACDC support JSON, MessagePack and CBOR currently.  Field ordering is important when apply cryptographic signatures and all serialized encoding formats must support static field ordering.  Serializing a SAD starts with reading the Version String from the SAD field (`v` for KERI and ACDC events Message) to determine the serialized encoding format of the Message.  The serialized encoding format is used to generate the SAID at creation and cannot be changed.  The event map is serialized using a library that ensures the static field order preserved across serialization and deserialization and the private keys are used to generate the qualified cryptographic material that represents the signatures over the SAD content.

The same serialized encoding format must be used when nesting a SAD in another SAD.  For example, an ACDC credential that was issued using JSON can be embedded and presented only in a KERI `exn` presentation event Message that uses JSON as its serialized encoding format.  That same credential cannot be transmitted using CBOR or MessagePack.  Controllers can rely on this restriction when verifying signatures of embedded SADs.  When processing the signature attachments and resolving the data at a given SAD path, the serialization of the outer most SAD can be used at any depth of the traversal.  New Version String processing does not need to occur at nested paths.  However, if credential signature verification is pipelined and processed in parallel to the event Message such that the event Message is not available, the Version String of the nested SAD will still be valid and can be used if needed.

Each attached signature is accompanied by a SAD Path that indicates the content that is signed.  The path must resolve within the enveloping SAD to either a nested SAD (map) or a SAID (string) of an externally provided SAD.  This of course, includes a root path that resolves to the enveloping SAD itself.

##### Signatures with Non-Transferable Identifiers
Non-transferable identifiers only ever have one public key.  In addition, the identifier prefix is identical to the qualified cryptographic material of the public key and therefore no Key Event Log ( KEL) is required to validate the signature of a non-transferable identifier [[1]].  The attachment code for witness receipt couplets, used for SAD Path Signatures, takes this into account.  The four-character Count Code `-C##` is used for non-transferable identifiers and contains the signing identifier prefix and the signature.  Since the verification key can be extracted from the identifier prefix and the identifier cannot be rotated, all that is required to validate the signature is the identifier prefix, the data signed and the signature.

##### Signatures with Transferable Identifiers
Transferable identifiers require full KEL resolution and verification to determine the correct public key used to sign some content [[1]].  In addition, the attachment code used for transferable identifiers, `-F##` must specify the location in the KEL at which point the signature was generated.  To accomplish this, this Count Code includes the identifier prefix, the sequence number of the event in the KEL, the digest of the event in the KEL and the indexed signatures (transferable identifiers support multiple public/private keys and require index signatures).  Using all the values, the signature(s) can be verified by retrieving the KEL of the identifier prefix and determine the key state at the sequence number along with validating the digest of the event against the actual event.  Then using the key(s) at the determined key state, validate the signature(s).

#### Additional Count Codes
This specification adds two Counter Four Character Codes to the CESR Master Code Table for attaching and grouping transposable signatures on SAD and nested SAD content.  The first code (`-J##`) is reserved for attaching a SAD path and the associated signatures on the content at the resolution of the SAD Path (either a SAD or its associated SAID).  The second reserved code (`-K##`) is for grouping all SAD Path signature groups under a root path for a given SAD.  The root path in the second grouping code provides signature attachment transposability for embedding SAD content in other Messages.

##### SAD Path Signature Group
The SAD Path Signature Group provides a four-character Count Code, `-J##`, for attaching an encoded Variable Length SAD Path along with either a transferable index signature group or non-transferable identifier receipt couplets.  The SAD Path identifies the content that this attachment is signing.  The path must resolve to either a nested SAD (map) or a SAID (string) of an externally provided SAD within the context of the SAD and root path against which this attachment is applied.  Using the following ACDC SAD embedded in a KERI `exn` Message:

```json
{
  "v": "KERICAAJSONAAQB.",
  "t": "exn",
  "dt": "2020-08-22T17:50:12.988921+00:00",
  "r": "/credential/offer",
  "a": {
    "credential": { // SIGNATURE TARGET OF TRANSPOSED SAD PATH GROUP
      "v": "ACDC10JSON00011c_",
      "d": "EBdXt3gIXOf2BBWNHdSXCJnFJL5OuQPyM5K0neuniccM",
      "i": "EmkPreYpZfFk66jpf3uFv7vklXKhzBrAqjsKAn2EDIPM",
      "s": "E46jrVPTzlSkUPqGGeIZ8a8FWS7a6s4reAXRZOkogZ2A",
      "a": {
        "d": "EgveY4-9XgOcLxUderzwLIr9Bf7V_NHwY1lkFrn9y2PY",
        "i": "EQzFVaMasUf4cZZBKA0pUbRc9T8yUXRFLyM1JDASYqAA",
        "dt": "2021-06-09T17:35:54.169967+00:00",
        "ri": "EymRy7xMwsxUelUauaXtMxTfPAMPAI6FkekwlOjkggt",
        "LEI": "254900OPPU84GM83MG36",
        "personal": {
          "legalName": "John Doe",
          "home": "Durham"
        }
      }
    }
  }
}
```


The following signature applies to the nested credential SAD signed by a transferable identifier using the transferable index signature group. The example is annotated with spaces and line feeds for clarity and an accompanied table is provided with comments.

```
-JAB
6AAEAAA-a-credential
-FAB
E_T2_p83_gRSuAYvGhqV3S0JzYEF2dIa-OCPLbIhBO7Y
-EAB0AAAAAAAAAAAAAAAAAAAAAAB
EwmQtlcszNoEIDfqD-Zih3N6o5B3humRKvBBln2juTEM
-AAD
AA5267UlFg1jHee4Dauht77SzGl8WUC_0oimYG5If3SdIOSzWM8Qs9SFajAilQcozXJVnbkY5stG_K4NbKdNB4AQ
ABBgeqntZW3Gu4HL0h3odYz6LaZ_SMfmITL-Btoq_7OZFe3L16jmOe49Ur108wH7mnBaq2E_0U0N0c5vgrJtDpAQ
ACTD7NDX93ZGTkZBBuSeSGsAQ7u0hngpNTZTK_Um7rUZGnLRNJvo5oOnnC1J2iBQHuxoq8PyjdT3BHS2LiPrs2Cg
```

| code | description |
| --- | ----------- |
|-JAB| SAD path signature group Count Code 1 following the group |
|6AAEAAA-a-credential| encoded SAD path designation|
|-FAB| Trans Indexed Sig Groups Count Code 1 following group|
|E_T2_p83_gRSuAYvGhqV3S0JzYEF2dIa-OCPLbIhBO7Y|trans prefix of signer for sigs|
|-EAB0AAAAAAAAAAAAAAAAAAAAAAB|sequence number of est event of signer's public keys for sigs|
|EwmQtlcszNoEIDfqD-Zih3N6o5B3humRKvBBln2juTEM| digest of est event of signer's public keys for sigs|
|-AAD|Controller Indexed Sigs Count Code 3 following sigs |
|AA5267...4AQ| sig 0 |
|ABBgeq...pAQ| sig 1 |
|ACTD7N...2Cg| sig 2 |


The next example demonstrates the use of a non-transferable identifier to sign SAD content.  In this example, the entire nested SAD located at the `a` field is signed by the non-transferable identifier:

```
-JAB
5AABAA-a
-CAB
BmMfUwIOywRkyc5GyQXfgDA4UOAMvjvnXcaK9G939ArM
0BT7b5PzUBmts-lblgOBzdThIQjKCbq8gMinhymgr4_dD0JyfN6CjZhsOqqUYFmRhABQ-vPywggLATxBDnqQ3aBg
```


| code | description |
| --- | ----------- |
| -JAB | SAD path signature group Count Code 1 following the group |
| 5AABAA-a | encoded SAD path designation |
| -CAB  | NonTrans witness receipt couplet |
| BmMfUwIOywRkyc5GyQXfgDA4UOAMvjvnXcaK9G939ArM | non-trans prefix of signer of sig |
| 0BT7b5... aBg | sig |

##### SAD Path Groups
The SAD Path Group provides a four-character Count Code, `-K##`, for attaching encoded Variable Length root SAD Path along with 1 or more SAD Path Signature Groups.  The root SAD Path identifies the root context against which the paths in all included SAD Path Signature Groups are resolved.  When parsing a SAD Path Group, if the root path is the single `-` character, all SAD paths are treated as absolute paths.  Otherwise, the root path is prepended to the SAD paths in each of the SAD Path Signature Groups.  Given the following snippet of a SAD Path Group:

```
-KAB6AABAAA--JAB5AABAA-a...
```

The root path is the single `-` character meaning that all subsequent SAD Paths are absolute and therefore the first path is resolved as the `a` field of the root map of the SAD as seen in the following example:

```json
{
  "v": "ACDCCAAJSONAAQB.",
  "d": "EBdXt3gIXOf2BBWNHdSXCJnFJL5OuQPyM5K0neuniccM",
  "i": "EmkPreYpZfFk66jpf3uFv7vklXKhzBrAqjsKAn2EDIPM",
  "s": "E46jrVPTzlSkUPqGGeIZ8a8FWS7a6s4reAXRZOkogZ2A",
  "a": {  // SIGNATURE TARGET OF SAD PATH GROUP
    "d": "EgveY4-9XgOcLxUderzwLIr9Bf7V_NHwY1lkFrn9y2PY",
    "i": "EQzFVaMasUf4cZZBKA0pUbRc9T8yUXRFLyM1JDASYqAA",
    "dt": "2021-06-09T17:35:54.169967+00:00",
    "ri": "EymRy7xMwsxUelUauaXtMxTfPAMPAI6FkekwlOjkggt",
    "LEI": "254900OPPU84GM83MG36",
    "personal": {
      "legalName": "John Doe",
      "city": "Durham"
    }
  }
}
```

###### Transposable Signature Attachments
To support nesting of signed SAD content in other SAD content, the root path of SAD Path Groups or the path of a SAD Path Signature Group provides transposability of CESR SAD signatures such that a single SAD Path Signature Group or an entire SAD Path Group attachment can be transposed across envelope boundaries by updating the single path or root path to indicate the new location.  Extending the example above, the SAD content is now embedded in a KERI `exn` event Message as follows:


```json
{
  "v": "KERICAAJSONAAQB.",
  "t": "exn",
  "dt": "2020-08-22T17:50:12.988921+00:00",
  "r": "/credential/offer",
  "a": {
    "v": "ACDC10JSON00011c_",
    "d": "EBdXt3gIXOf2BBWNHdSXCJnFJL5OuQPyM5K0neuniccM",
    "i": "EmkPreYpZfFk66jpf3uFv7vklXKhzBrAqjsKAn2EDIPM",
    "s": "E46jrVPTzlSkUPqGGeIZ8a8FWS7a6s4reAXRZOkogZ2A",
    "a": { // SIGNATURE TARGET OF TRANSPOSED SAD PATH GROUP
      "d": "EgveY4-9XgOcLxUderzwLIr9Bf7V_NHwY1lkFrn9y2PY",
      "i": "EQzFVaMasUf4cZZBKA0pUbRc9T8yUXRFLyM1JDASYqAA",
      "dt": "2021-06-09T17:35:54.169967+00:00",
      "ri": "EymRy7xMwsxUelUauaXtMxTfPAMPAI6FkekwlOjkggt",
      "LEI": "254900OPPU84GM83MG36",
      "personal": {
        "legalName": "John Doe",
        "city": "Durham"
      }
    }
  }
}
```

The same signature gets transposed to the outer `exn` SAD by updating the root path of the `-K##` attachment:

```
-KAB5AABAA-a-JAB5AABAA-a...
```

Now the SAD Path of the first signed SAD content resolves to the `a` field of the `a` field of the streamed `exn` Message

#### Small Variable Raw Size SAD Path Code
The small variable raw side code reserved for SAD Path encoding is `A` which results in the addition of 3 entries (`4A##`, `5A##` and `6A##`) in the Master Code Table for each lead byte configuration.  These codes and their use are discussed in detail in [CESR Encoding for SAD Path Language]().

::: issue
fix this citation
:::

### Nested Partial Signatures
Additional signatures on nested content can be included in a SAD Path Group and are applied to the serialized data at the resolution of a SAD path in a SAD.  Signatures can be applied to the SAID or an entire nested SAD.   When verifying a SAD Path Signature, the content at the resolution of the SAD path is the data that was signed.  The choice to sign a SAID or the full SAD effects how the data may be used in presentations and the rules for verifying the signature.

#### Signing Nested SADs
When signing nested SAD content, the serialization used at the time of signing is the only serialization that can be used when presenting the signed data.  When transposing the signatures and nesting the signed data, the enveloping SAD must use the same serialization that was used to create the signatures.  This is to ensure that all signatures apply to the data over the wire and not a transformation of that data.  The serialization can be determined from the version field (`v`) of the nested SAD or any parent of the nested SAD as they are guaranteed to be identical.  Consider the following ACDC Credential SAD:

```json
{
  "v": "ACDCCAAJSONAAQB.",
  "d": "EBdXt3gIXOf2BBWNHdSXCJnFJL5OuQPyM5K0neuniccM",
  "i": "EmkPreYpZfFk66jpf3uFv7vklXKhzBrAqjsKAn2EDIPM",
  "s": "E46jrVPTzlSkUPqGGeIZ8a8FWS7a6s4reAXRZOkogZ2A",
  "a": {   // SIGNATURE TARGET OF SAD PATH GROUP
    "d": "EgveY4-9XgOcLxUderzwLIr9Bf7V_NHwY1lkFrn9y2PY",
    "i": "EQzFVaMasUf4cZZBKA0pUbRc9T8yUXRFLyM1JDASYqAA",
    "dt": "2021-06-09T17:35:54.169967+00:00",
    "ri": "EymRy7xMwsxUelUauaXtMxTfPAMPAI6FkekwlOjkggt",
    "LEI": "254900OPPU84GM83MG36",
    "personal": {
      "d": "E2X8OLaLnM0XRQEYgM5UV3bZmWg3UUn7CP4SoKkvsl-s",
        "first": "John",
        "last": "Doe"
    }
  }
}
```

To sign the SAD located at the path `-a`, JSON serialization would be used because the SAD at that path does not have a version field so the version field of its parent is used.  The serialization rules (spacing, field ordering, etc.) for a SAD would be used for the SAD and the serialization encoding format and the signature would be applied to the bytes of the JSON for that map.  Any presentation of the signed data must always include the fully nested SAD.  The only valid nesting of this credential would be as follows:

```json
{
  "v": "KERICAAJSONAAQB.",
  "t": "exn",
  "dt": "2020-08-22T17:50:12.988921+00:00"
  "r": "/credential/apply"
  "a": {
    "v": "ACDC10JSON00011c_",
    "d": "EBdXt3gIXOf2BBWNHdSXCJnFJL5OuQPyM5K0neuniccM",
    "i": "EmkPreYpZfFk66jpf3uFv7vklXKhzBrAqjsKAn2EDIPM",
    "s": "E46jrVPTzlSkUPqGGeIZ8a8FWS7a6s4reAXRZOkogZ2A",
    "a": {   // FULL SAD MUST BE PRESENT
      "d": "EgveY4-9XgOcLxUderzwLIr9Bf7V_NHwY1lkFrn9y2PY",
      "i": "EQzFVaMasUf4cZZBKA0pUbRc9T8yUXRFLyM1JDASYqAA",
      "dt": "2021-06-09T17:35:54.169967+00:00",
      "ri": "EymRy7xMwsxUelUauaXtMxTfPAMPAI6FkekwlOjkggt",
      "LEI": "254900OPPU84GM83MG36",
      "legalName": {
        "d": "E2X8OLaLnM0XRQEYgM5UV3bZmWg3UUn7CP4SoKkvsl-s",
        "first": "John",
        "last": "Doe"
      }
    }
  }
}
```

#### Signing SAIDs
Applying signatures to a SAD with SAIDs in place of fully expanded nested SAD content enables compact credentials for Domains with bandwidth restrictions such as IoT.  Consider the following fully expanded credential:

```json
{
    "v": "ACDCCAAJSONAAQB.",
    "d": "EBdXt3gIXOf2BBWNHdSXCJnFJL5OuQPyM5K0neuniccM",
    "i": "EmkPreYpZfFk66jpf3uFv7vklXKhzBrAqjsKAn2EDIPM",
    "s": "E46jrVPTzlSkUPqGGeIZ8a8FWS7a6s4reAXRZOkogZ2A",
    "a": {
      "d": "EgveY4-9XgOcLxUderzwLIr9Bf7V_NHwY1lkFrn9y2PY",
      "i": "EQzFVaMasUf4cZZBKA0pUbRc9T8yUXRFLyM1JDASYqAA",
      "dt": "2021-06-09T17:35:54.169967+00:00",
      "ri": "EymRy7xMwsxUelUauaXtMxTfPAMPAI6FkekwlOjkggt",
      "LEI": "254900OPPU84GM83MG36",
      "legalName": {
        "d": "E2X8OLaLnM0XRQEYgM5UV3bZmWg3UUn7CP4SoKkvsl-s",
        "n": "sKHtYSiCdlibuLDS2PTJg1AZXtPhaySZ9O3DoKrRXWY",
        "first": "John",
        "middle": "William",
        "last": "Doe"
      },
      "address": {
        "d": "E-0luqYSg6cPcMFmhiAz8VBQObZLmTQPrgsr7Z1j6CA4",
        "n": "XiSoVDNvqV8ldofPyTVqQ-EtVPlkIIQTln9Ai0yI05M",
        "street": "123 Main St",
        "city": "Salt Lake City",
        "state": "Utah",
        "zipcode": "84157"
      },
      "phone": {
        "d": "E6lty8H2sA_1acq8zg89_kqF194DbF1cDpwA7UPtwjPQ",
        "n": "_XKNVntbcIjp12DmsAGhv-R7JRwuzjD6KCHC7Fw3zvU",
        "mobile": "555-121-3434",
        "home": "555-121-3435",
        "work": "555-121-3436",
        "fax": "555-121-3437"
      }
    }
  }
}
```

The three nested blocks of the `a` block `legalName`, `address` and `phone` are SADs with a SAID in the `d` field and are candidates for SAID replacement in an issued credential.  A compact credential can be created and signed by replacing those three nested blocks with the SAID of each nested SAD.  The schema for this Verifiable Credential would need to specify conditional subschema for the field labels at each nesting location that requires the full schema of the nested SAD or a string for the SAID.  The commitment to a SAID in place of a SAD contains nearly the same cryptographic integrity as a commitment to the SAD itself since the SAID is the qualified cryptographic material of a digest of the SAD.  The same credential could be converted to a compact credential containing the SAIDs of each nested block and signed as follows:

```json
{
   "v": "ACDCCAAJSONAAQB.",
   "d": "EBdXt3gIXOf2BBWNHdSXCJnFJL5OuQPyM5K0neuniccM",
   "i": "EmkPreYpZfFk66jpf3uFv7vklXKhzBrAqjsKAn2EDIPM",
   "s": "E46jrVPTzlSkUPqGGeIZ8a8FWS7a6s4reAXRZOkogZ2A",
   "a": {
     "d": "EgveY4-9XgOcLxUderzwLIr9Bf7V_NHwY1lkFrn9y2PY",
     "i": "EQzFVaMasUf4cZZBKA0pUbRc9T8yUXRFLyM1JDASYqAA",
     "dt": "2021-06-09T17:35:54.169967+00:00",
     "ri": "EymRy7xMwsxUelUauaXtMxTfPAMPAI6FkekwlOjkggt",
     "LEI": "254900OPPU84GM83MG36",
     "legalName": "E2X8OLaLnM0XRQEYgM5UV3bZmWg3UUn7CP4SoKkvsl-s",
     "address": "E-0luqYSg6cPcMFmhiAz8VBQObZLmTQPrgsr7Z1j6CA4",
     "phone": "E6lty8H2sA_1acq8zg89_kqF194DbF1cDpwA7UPtwjPQ"
   }
}
```

It is important to note that if this Version of the credential is the one issued to the holder and the signature over the entire credential is on the serialized data of this Version of the credential it is the only Version that can be presented.  The full SAD data of the three nested blocks would be delivered out of band from the signed credential.  The top-level schema would describe the blocks with conditional subschema for each section.  The credential signature becomes a cryptographic commitment to the contents of the overall credential as well as the content of each of the blocks and will still validate the presented credential with significantly less bandwidth.

With this approach, credential presentation request and exchange protocols can be created that modify the schema with the conditional subschema, removing the conditions that allow for SAIDs in place of the required (or presented) nested blocks.  The modified schema can be used in such a protocol to indicate the required sections to be delivered out of bounds or as a commitment to provide the nested blocks after the credential presentation has occurred.

### Post-Quantum Security
Post-quantum or quantum-safe cryptography deals with techniques that maintain their cryptographic strength despite attacks from quantum computers. Because it is currently assumed that practical quantum computers do not yet exist, post-quantum techniques are forward-looking to some future time when they do exist. A one-way function that is post-quantum secure will not be any less secure (resistant to inversion) in the event that practical quantum computers suddenly or unexpectedly become available. One class of post-quantum secure one-way functions are some cryptographic strength hashes. The analysis of D.J. Bernstein with regards the collision resistance of cryptographic one-way hashing functions concludes that quantum computation provides no advantage over non-quantum techniques. Consequently, one way to provide some degree of post-quantum security is to hide cryptographic material behind digests of that material created by such hashing functions. This directly applies to the public keys declared in the pre-rotations. Instead of a pre-rotation making a cryptographic pre-commitment to a public key, it makes a pre-commitment to a digest of that public key. The digest may be verified once the public key is disclosed (unhidden) in a later rotation operation. Because the digest is the output of a one-way hash function, the digest is uniquely strongly bound to the public key. When the unexposed public keys of a pre-rotation are hidden in a digest, the associated private keys are protected from a post-quantum brute force inversion attack on those public keys.

To elaborate, a post-quantum attack that may practically invert the one-way public key generation (ECC scalar multiplication) function using quantum computation must first invert the digest of the public key using non-quantum computation. Pre-quantum cryptographic strength is, therefore, not weakened post-quantum. A surprise quantum capability may no longer be a vulnerability. Strong one-way hash functions, such as 256-bit (32-byte) Blake2, Blake3, and SHA3, with 128-bits of pre-quantum strength, maintain that strength post-quantum. Furthermore, hiding the pre-rotation public keys does not impose any additional storage burden on the controller because the controller must always be able to reproduce or recover the associated private keys to sign the associated rotation operation. Hidden public keys may be compactly expressed as Base64 encoded qualified public keys digests (hidden) where the digest function is indicated in the derivation code.

[//]: # (\newpage)

[//]: # (\makebibliography)