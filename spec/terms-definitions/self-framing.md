[[def: Self-Framing]]

~ a textual or binary encoding that begins with type, size, and value so that a parser knows how many characters (when textual) or bytes (when binary) to extract from the stream for a given element without parsing the rest of the characters or bytes in the element is Self-Framing. A self-framing Primitive may be extracted without needing any additional delimiting characters. Thus, a stream of concatenated Primitives may be extracted without the need to encapsulate each Primitive inside a set of delimiters or an envelope.
