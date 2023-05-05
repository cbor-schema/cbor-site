---
sidebar_position: 1
---

# CBOR 标准

本文使用 GPT-4 翻译，做了一些微调和排版优化，原文 RFC 8949：[Concise Binary Object Representation (CBOR)](https://datatracker.ietf.org/doc/html/rfc8949).

## 1. 引言（Introduction）

<details>
  <summary>Expand original text</summary>

There are hundreds of standardized formats for binary representation of structured data (also known as binary serialization formats). Of those, some are for specific domains of information, while others are generalized for arbitrary data. In the IETF, probably the best-known formats in the latter category are ASN.1's BER and DER [ASN.1][ASN.1].

The format defined here follows some specific design goals that are not well met by current formats. The underlying data model is an extended version of the JSON data model [RFC8259][RFC8259]. It is important to note that this is not a proposal that the grammar in RFC 8259 be extended in general, since doing so would cause a significant backwards incompatibility with already deployed JSON documents. Instead, this document simply defines its own data model that starts from JSON.

Appendix E lists some existing binary formats and discusses how well they do or do not fit the design objectives of the Concise Binary Object Representation (CBOR).

This document obsoletes [RFC7049][RFC7049], providing editorial improvements, new details, and errata fixes while keeping full compatibility with the interchange format of RFC 7049. It does not create a new version of the format.
</details>

有数百种标准化的格式用于表示结构化数据的二进制表示（也称为二进制序列化格式）。其中一些是针对特定领域的信息，而另一些则是针对任意数据进行泛化的。在 IETF 中，后一类中最著名的格式可能是 ASN.1 的 BER 和 DER [ASN.1][ASN.1]。

这里定义的格式遵循一些特定的设计目标，这些目标并没有被当前的格式很好地满足。底层数据模型是 JSON 数据模型的扩展版本 [RFC8259][RFC8259]。需要注意的是，这并不是建议将 RFC 8259 中的语法进行一般性扩展，因为这样做会导致与已部署的 JSON 文档的重大向后不兼容。相反，本文档只是定义了一个从 JSON 开始的自有数据模型。

附录 E 列出了一些现有的二进制格式，并讨论了它们是否符合简明二进制对象表示法（CBOR）的设计目标。

本文档取代了 [RFC7049][RFC7049]，在保持与 RFC 7049 交换格式的完全兼容性的同时，提供了编辑改进、新的细节和错误修正。它并未创建格式的新版本。

### 1.1. 目标（Objectives）

<details>
  <summary>Expand original text</summary>

The objectives of CBOR, roughly in decreasing order of importance, are:

1. The representation must be able to unambiguously encode most common data formats used in Internet standards.

- It must represent a reasonable set of basic data types and structures using binary encoding. "Reasonable" here is largely influenced by the capabilities of JSON, with the major addition of binary byte strings. The structures supported are limited to arrays and trees; loops and lattice-style graphs are not supported.
- There is no requirement that all data formats be uniquely encoded; that is, it is acceptable that the number "7" might be encoded in multiple different ways.

2. The code for an encoder or decoder must be able to be compact in order to support systems with very limited memory, processor power, and instruction sets.

- An encoder and a decoder need to be implementable in a very small amount of code (for example, in class 1 constrained nodes as defined in [RFC7228][RFC7228]).
- The format should use contemporary machine representations of data (for example, not requiring binary-to-decimal conversion).

3. Data must be able to be decoded without a schema description.

- Similar to JSON, encoded data should be self-describing so that a generic decoder can be written.

4. The serialization must be reasonably compact, but data compactness is secondary to code compactness for the encoder and decoder.

- "Reasonable" here is bounded by JSON as an upper bound in size and by the implementation complexity, which limits the amount of effort that can go into achieving that compactness. Using either general compression schemes or extensive bit-fiddling violates the complexity goals.

5. The format must be applicable to both constrained nodes and high-volume applications.

- This means it must be reasonably frugal in CPU usage for both encoding and decoding. This is relevant both for constrained nodes and for potential usage in applications with a very high volume of data.

6. The format must support all JSON data types for conversion to and from JSON.

- It must support a reasonable level of conversion as long as the data represented is within the capabilities of JSON. It must be possible to define a unidirectional mapping towards JSON for all types of data.

7. The format must be extensible, and the extended data must be decodable by earlier decoders.

- The format is designed for decades of use.
- The format must support a form of extensibility that allows fallback so that a decoder that does not understand an extension can still decode the message.
- The format must be able to be extended in the future by later IETF standards.

</details>

CBOR 的目标，大致按重要性递减顺序，如下：

1. 表示必须能够无歧义地编码互联网标准中使用的最常见数据格式。

- 它必须使用二进制编码来表示一组合理的基本数据类型和结构。这里的“合理”（Reasonable）主要受 JSON 的功能影响，主要增加了二进制字节串。支持的结构仅限于数组和树；不支持循环和格状（lattice-style）图结构。
- 没有要求所有数据格式都具有唯一编码；也就是说，数字“7”用多种不同方式编码是可以接受的。

2. 编码器或解码器的代码必须能够紧凑，以支持具有非常有限的内存、处理器能力和指令集的系统。

- 编码器和解码器需要在非常少量的代码中实现（例如，在 [RFC7228][RFC7228] 中定义的1类（class 1）受限节点中）。
- 格式应使用现代机器数据表示（例如，不需要二进制到十进制转换）。

3. 数据必须能够在没有模式描述的情况下进行解码。

- 与 JSON 类似，编码数据应是自描述的，以便可以编写通用解码器。

4. 序列化必须相当紧凑，但数据紧凑性对于编码器和解码器的代码紧凑性是次要的。

- 这里的“合理”（Reasonable）受 JSON 在大小上的上限约束，以及实现复杂性，这限制了为实现紧凑性所能付出的努力。使用通用压缩方案或大量的位操作违反了复杂性目标。

5. 该格式必须适用于受限节点和大容量应用程序。

- 这意味着它在编码和解码方面必须对 CPU 使用相当节俭。这既适用于受限节点，也适用于具有非常大数据量的应用程序。

6. 格式必须支持所有 JSON 数据类型，以便在 JSON 之间进行转换。

- 只要所表示的数据在 JSON 的功能范围内，就必须支持合理水平的转换。必须能够为所有类型的数据定义朝向 JSON 的单向映射。

7. 格式必须具有可扩展性，扩展后的数据必须能被早期的解码器解码。

- 该格式设计用于几十年的使用。
- 格式必须支持一种允许回退的可扩展性形式，以便不理解扩展的解码器仍然可以解码消息。
- 格式必须能够在将来由后来的 IETF 标准进行扩展。

### 1.2. 术语（Terminology）

<details>
  <summary>Expand original text</summary>

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [RFC2119][RFC2119] [RFC8174][RFC8174] when, and only when, they appear in all capitals, as shown here.

The term "byte" is used in its now-customary sense as a synonym for "octet". All multi-byte values are encoded in network byte order (that is, most significant byte first, also known as "big-endian").

This specification makes use of the following terminology:

- **Data item**: A single piece of CBOR data. The structure of a data item may contain zero, one, or more nested data items. The term is used both for the data item in representation format and for the abstract idea that can be derived from that by a decoder; the former can be addressed specifically by using the term "encoded data item".
- **Decoder**: A process that decodes a well-formed encoded CBOR data item and makes it available to an application. Formally speaking, a decoder contains a parser to break up the input using the syntax rules of CBOR, as well as a semantic processor to prepare the data in a form suitable to the application.
- **Encoder**: A process that generates the (well-formed) representation format of a CBOR data item from application information.
- **Data Stream**: A sequence of zero or more data items, not further assembled into a larger containing data item (see [RFC8742][RFC8742] for one application). The independent data items that make up a data stream are sometimes also referred to as "top-level data items".
- **Well-formed**: A data item that follows the syntactic structure of CBOR. A well-formed data item uses the initial bytes and the byte strings and/or data items that are implied by their values as defined in CBOR and does not include following extraneous data. CBOR decoders by definition only return contents from well-formed data items.
- **Valid**: A data item that is well-formed and also follows the semantic restrictions that apply to CBOR data items (Section 5.3).
- **Expected**: Besides its normal English meaning, the term "expected" is used to describe requirements beyond CBOR validity that an application has on its input data. Well-formed (processable at all), valid (checked by a validity-checking generic decoder), and expected (checked by the application) form a hierarchy of layers of acceptability.
- **Stream decoder**: A process that decodes a data stream and makes each of the data items in the sequence available to an application as they are received.

Terms and concepts for floating-point values such as Infinity, NaN (not a number), negative zero, and subnormal are defined in [IEEE754].

Where bit arithmetic or data types are explained, this document uses the notation familiar from the programming language [C][C], except that ".." denotes a range that includes both ends given, and superscript notation denotes exponentiation. For example, 2 to the power of 64 is notated: 2^64. In the plain-text version of this specification, superscript notation is not available and therefore is rendered by a surrogate notation. That notation is not optimized for this RFC; it is unfortunately ambiguous with C's exclusive-or (which is only used in the appendices, which in turn do not use exponentiation) and requires circumspection from the reader of the plain-text version.

Examples and pseudocode assume that signed integers use two's complement representation and that right shifts of signed integers perform sign extension; these assumptions are also specified in Sections 6.8.1 (basic.fundamental) and 7.6.7 (expr.shift) of the 2020 version of C++ (currently available as a final draft, [Cplusplus20][Cplusplus20]).

Similar to the "0x" notation for hexadecimal numbers, numbers in binary notation are prefixed with "0b". Underscores can be added to a number solely for readability, so 0b00100001 (0x21) might be written 0b001_00001 to emphasize the desired interpretation of the bits in the byte; in this case, it is split into three bits and five bits. Encoded CBOR data items are sometimes given in the "0x" or "0b" notation; these values are first interpreted as numbers as in C and are then interpreted as byte strings in network byte order, including any leading zero bytes expressed in the notation.

Words may be italicized for emphasis; in the plain text form of this specification, this is indicated by surrounding words with underscore characters. Verbatim text (e.g., names from a programming language) may be set in monospace type; in plain text, this is approximated somewhat ambiguously by surrounding the text in double quotes (which also retain their usual meaning).
</details>

本文档中的关键词 "MUST"（必须）, "MUST NOT"（必须不）, "REQUIRED"（要求）, "SHALL"（应当）, "SHALL NOT"（不应当）, "SHOULD"（应该）, "SHOULD NOT"（不应该）, "RECOMMENDED"（建议）, "NOT RECOMMENDED"（不建议）, "MAY"（可以）和 "OPTIONAL"（可选）应按照 BCP 14 [RFC2119][RFC2119] [RFC8174][RFC8174] 中的描述进行解释，只有当它们以大写字母出现时，如本文所示。

术语 `byte`（字节）在此以现有的习惯意义用作 `octet`（八位组）的同义词。所有多字节值均以网络字节序编码（即，最高有效字节优先，也称为 `big-endian`（大端序））。

本规范使用以下术语：

- **数据项（Data item）**：单个 CBOR 数据。数据项的结构可能包含零个、一个或多个嵌套数据项。术语既用于表示格式的数据项，也用于由解码器从中得出的抽象概念；前者可以通过使用术语“编码数据项”进行具体描述。
- **解码器（Decoder）**：解码格式良好的编码 CBOR 数据项并使其可供应用程序使用的过程。从形式上讲，解码器包含一个解析器，用于根据 CBOR 的语法规则拆分输入，以及一个语义处理器，用于以适合应用程序的形式准备数据。
- **编码器（Encoder）**：从应用程序信息生成 CBOR 数据项的（格式良好的）表示格式的过程。
- **数据流（Data Stream）**：零个或多个数据项的序列，没有进一步组装成更大的包含数据项（有关一个应用程序，请参见 [RFC8742][RFC8742]）。组成数据流的独立数据项有时也称为“顶级数据项”（top-level data items）。
- **格式良好（Well-formed）**：遵循 CBOR 语法结构的数据项。格式良好的数据项使用根据 CBOR 定义的初始字节以及由它们的值所隐含的字节串和/或数据项，并且不包括后续的多余数据。CBOR 解码器按定义仅从格式良好的数据项返回内容。
- **有效（Valid）**：格式良好且遵循适用于 CBOR 数据项的语义限制（第5.3节）的数据项。
- **预期（Expected）**：除了正常的英语意义外，术语 `expected` 用于描述应用程序对其输入数据的 CBOR 有效性之外的要求。格式良好（可处理），有效（由有效性检查通用解码器检查）和预期（由应用程序检查）形成了可接受性层次结构。
- **流解码器（Stream decoder）**：解码数据流并使序列中的每个数据项在接收时提供给应用程序的过程。

关于浮点数值的术语和概念，例如 Infinity（无穷大），NaN（非数字），negative zero（负零）和 subnormal（次正规数），在 [IEEE754][IEEE754] 中有定义。

在解释位运算或数据类型时，本文档使用来自编程语言 [C][C] 的熟悉表示法，只是 `..` 表示包含给定两端的范围，上标表示法表示指数运算。例如，2 的 64 次方表示为：2^64。在本规范的纯文本版本中，上标表示法不可用，因此用替代表示法呈现。该表示法不是针对此 RFC 优化的；它不幸地与 C 的异或（仅在附录中使用，附录反过来又不使用指数运算）产生歧义，并要求纯文本版本的读者谨慎阅读。

示例和伪代码假设有符号整数使用二进制补码表示，有符号整数的右移执行符号扩展；这些假设也在 2020 年 C++ 的第 6.8.1 节（basic.fundamental）和第 7.6.7 节（expr.shift）中指定（目前已作为最终草案提供，[Cplusplus20][Cplusplus20]）。

与十六进制数字的 `0x` 表示法类似，二进制数字表示法以 `0b` 为前缀。为了便于阅读，可以仅向数字中添加下划线，例如将 0b00100001（0x21）写为 0b001_00001，以强调字节中位的期望解释；在这种情况下，它被拆分为三位和五位。编码的 CBOR 数据项有时以 `0x` 或 `0b` 表示法给出；这些值首先按照 C 中的数字解释，然后解释为网络字节序的字节串，包括表示法中表示的任何前导零字节。

为了强调，单词可能会被斜体；在本规范的纯文本形式中，这用下划线字符表示。逐字文本（例如，来自编程语言的名称）可能设置为等宽字体；在纯文本中，这通过将文本放在双引号中来近似表示（双引号也保留其通常的含义）。

## 2. CBOR 数据模型（CBOR Data Models）

<details>
  <summary>Expand original text</summary>

CBOR is explicit about its generic data model, which defines the set of all data items that can be represented in CBOR. Its basic generic data model is extensible by the registration of "simple values" and tags. Applications can then create a subset of the resulting extended generic data model to build their specific data models.

Within environments that can represent the data items in the generic data model, generic CBOR encoders and decoders can be implemented (which usually involves defining additional implementation data types for those data items that do not already have a natural representation in the environment). The ability to provide generic encoders and decoders is an explicit design goal of CBOR; however, many applications will provide their own application-specific encoders and/or decoders.

In the basic (unextended) generic data model defined in Section 3, a data item is one of the following:

- an integer in the range -2^64..2^64-1 inclusive
- a simple value, identified by a number between 0 and 255, but distinct from that number itself
- a floating-point value, distinct from an integer, out of the set representable by IEEE 754 binary64 (including non-finites) [IEEE754][IEEE754]
- a sequence of zero or more bytes ("byte string")
- a sequence of zero or more Unicode code points ("text string")
- a sequence of zero or more data items ("array")
- a mapping (mathematical function) from zero or more data items ("keys") each to a data item ("values"), ("map")
- a tagged data item ("tag"), comprising a tag number (an integer in the range 0..2^64-1) and the tag content (a data item)

Note that integer and floating-point values are distinct in this model, even if they have the same numeric value.

Also note that serialization variants are not visible at the generic data model level. This deliberate absence of visibility includes the number of bytes of the encoded floating-point value. It also includes the choice of encoding for an "argument" (see Section 3) such as the encoding for an integer, the encoding for the length of a text or byte string, the encoding for the number of elements in an array or pairs in a map, or the encoding for a tag number.
</details>

CBOR 明确指出其通用数据模型，该模型定义了可在 CBOR 中表示的所有数据项的集合。通过注册“简单值”（simple values）和标签，可以扩展其基本通用数据模型。然后，应用程序可以从生成的扩展通用数据模型中创建一个子集以构建其特定的数据模型。

在可以表示通用数据模型中的数据项的环境中，可以实现通用 CBOR 编码器和解码器（通常涉及为那些在环境中尚无自然表示的数据项定义额外的实现数据类型）。提供通用编码器和解码器的能力是 CBOR 的一个明确设计目标；然而，许多应用程序将提供自己的特定于应用的编码器和/或解码器。

在第3节中定义的基本（未扩展）通用数据模型中，数据项（data item）是以下之一：

- 包含 -2^64..2^64-1 范围内的整数
- 由 0 到 255 之间的数字标识的简单值，但与该数字本身不同
- 一个浮点值，不同于整数，从 IEEE 754 binary64 可表示的集合中选取（包括非有限值）[IEEE754][IEEE754]
- 零个或多个字节的序列（“字节串”（"byte string"））
- 零个或多个 Unicode 码位的序列（“文本串”（"text string"））
- 零个或多个数据项的序列（“数组”（"array"））
- 从零个或多个数据项（“键”（"keys"））到数据项（“值”（"values"））的映射（数学函数）（“映射”（"map"））
- 一个带标签的数据项（“标签”（"tag"）），包括一个标签号（0..2^64-1 范围内的整数）和标签内容（一个数据项）

请注意，在此模型中，整数和浮点数值是不同的，即使它们具有相同的数值。

还要注意，在通用数据模型级别，序列化变体不可见。这种故意的可见性缺失包括编码浮点值的字节数。这还包括“参数”（argument）的编码选择（参见第 3 节），例如整数的编码、文本或字节串长度的编码、数组中元素数量或映射中对的数量的编码，或标签号的编码。

### 2.1. 扩展通用数据模型（Extended Generic Data Models）

<details>
  <summary>Expand original text</summary>

This basic generic data model has been extended in this document by the registration of a number of simple values and tag numbers, such as:

- false, true, null, and undefined (simple values identified by 20..23, Section 3.3)
- integer and floating-point values with a larger range and precision than the above (tag numbers 2 to 5, Section 3.4)
- application data types such as a point in time or date/time string defined in RFC 3339 (tag numbers 1 and 0, Section 3.4)

Additional elements of the extended generic data model can be (and have been) defined via the IANA registries created for CBOR. Even if such an extension is unknown to a generic encoder or decoder, data items using that extension can be passed to or from the application by representing them at the application interface within the basic generic data model, i.e., as generic simple values or generic tags.

In other words, the basic generic data model is stable as defined in this document, while the extended generic data model expands by the registration of new simple values or tag numbers, but never shrinks.

While there is a strong expectation that generic encoders and decoders can represent false, true, and null (undefined is intentionally omitted) in the form appropriate for their programming environment, the implementation of the data model extensions created by tags is truly optional and a matter of implementation quality.
</details>

通过注册一些简单值和标签号，本文档已经扩展了这个基本通用数据模型，例如：

- `false`、`true`、`null` 和 `undefined`（由 20..23 标识的简单值，第 3.3 节）
- 比上述更大范围和精度的整数和浮点数值（标签号 2 到 5，第 3.4 节）
- 应用程序数据类型，例如 RFC 3339 中定义的时间点或日期/时间字符串（标签号 1 和 0，第 3.4 节）

可以（且已经）通过为 CBOR 创建的 IANA 注册表定义扩展通用数据模型的其他元素。即使这样的扩展对通用编码器或解码器未知，也可以将使用该扩展的数据项传递给应用程序或从应用程序传递，即在基本通用数据模型中的应用程序接口表示它们，如通用简单值或通用标签。

换句话说，本文档定义的基本通用数据模型是稳定的，而通过注册新的简单值或标签号，扩展通用数据模型不断扩展，但从不收缩。

尽管有强烈的期望，通用编码器和解码器可以将 false、true 和 null（故意省略了 undefined）表示为适合其编程环境的形式，但通过标签创建的数据模型扩展的实现确实是可选的，是实现质量的问题。

### 2.2. 特定数据模型（Specific Data Models）

<details>
  <summary>Expand original text</summary>

The specific data model for a CBOR-based protocol usually takes a subset of the extended generic data model and assigns application semantics to the data items within this subset and its components. When documenting such specific data models and specifying the types of data items, it is preferable to identify the types by their generic data model names ("negative integer", "array") instead of referring to aspects of their CBOR representation ("major type 1", "major type 4").

Specific data models can also specify value equivalency (including values of different types) for the purposes of map keys and encoder freedom. For example, in the generic data model, a valid map MAY have both 0 and 0.0 as keys, and an encoder MUST NOT encode 0.0 as an integer (major type 0, Section 3.1). However, if a specific data model declares that floating-point and integer representations of integral values are equivalent, using both map keys 0 and 0.0 in a single map would be considered duplicates, even while encoded as different major types, and so invalid; and an encoder could encode integral-valued floats as integers or vice versa, perhaps to save encoded bytes.
</details>

基于 CBOR 的协议的特定数据模型通常从扩展通用数据模型中获取一个子集，并为此子集及其组件内的数据项分配应用语义。在记录这些特定数据模型并指定数据项类型时，最好通过它们的通用数据模型名称（如 "negative integer"、"array"）来标识类型，而不是引用它们的 CBOR 表示的方面（如 "major type 1"、"major type 4"）。

特定数据模型还可以为映射键和编码器自由指定值等效性（包括不同类型的值）。例如，在通用数据模型中，一个有效的映射可能（MAY）同时具有 `0` 和 `0.0` 作为键，编码器不能（MUST NOT）将 `0.0` 编码为整数（"major type 0"，第 3.1 节）。然而，如果一个特定的数据模型声明浮点数和整数表示的整数值是等价的，那么在单个映射中同时使用映射键 `0` 和 `0.0` 将被认为是重复的，即使它们被编码为不同的主类型，因此是无效的；而且编码器可以将整数值浮点数编码为整数，反之亦然，或许是为了节省编码字节。

## 3. CBOR 编码规范（Specification of the CBOR Encoding）

<details>
  <summary>Expand original text</summary>

A CBOR data item (Section 2) is encoded to or decoded from a byte string carrying a well-formed encoded data item as described in this section. The encoding is summarized in Table 7 in Appendix B, indexed by the initial byte. An encoder MUST produce only well-formed encoded data items. A decoder MUST NOT return a decoded data item when it encounters input that is not a well-formed encoded CBOR data item (this does not detract from the usefulness of diagnostic and recovery tools that might make available some information from a damaged encoded CBOR data item).

The initial byte of each encoded data item contains both information about the major type (the high-order 3 bits, described in Section 3.1) and additional information (the low-order 5 bits). With a few exceptions, the additional information's value describes how to load an unsigned integer "argument":

- **Less than 24**: The argument's value is the value of the additional information.
- **24, 25, 26, or 27**: The argument's value is held in the following 1, 2, 4, or 8 bytes, respectively, in network byte order. For major type 7 and additional information value 25, 26, 27, these bytes are not used as an integer argument, but as a floating-point value (see Section 3.3).
- **28, 29, 30**: These values are reserved for future additions to the CBOR format. In the present version of CBOR, the encoded item is not well-formed.
- **31**: No argument value is derived. If the major type is 0, 1, or 6, the encoded item is not well-formed. For major types 2 to 5, the item's length is indefinite, and for major type 7, the byte does not constitute a data item at all but terminates an indefinite-length item; all are described in Section 3.2.

The initial byte and any additional bytes consumed to construct the argument are collectively referred to as the head of the data item.

The meaning of this argument depends on the major type. For example, in major type 0, the argument is the value of the data item itself (and in major type 1, the value of the data item is computed from the argument); in major type 2 and 3, it gives the length of the string data in bytes that follow; and in major types 4 and 5, it is used to determine the number of data items enclosed.

If the encoded sequence of bytes ends before the end of a data item, that item is not well-formed. If the encoded sequence of bytes still has bytes remaining after the outermost encoded item is decoded, that encoding is not a single well-formed CBOR item. Depending on the application, the decoder may either treat the encoding as not well-formed or just identify the start of the remaining bytes to the application.

A CBOR decoder implementation can be based on a jump table with all 256 defined values for the initial byte ([Table 7][Table 7]). A decoder in a constrained implementation can instead use the structure of the initial byte and following bytes for more compact code (see Appendix C for a rough impression of how this could look).
</details>

CBOR 数据项（第 2 节）按照本节所述，编码为或解码自携带格式良好的编码数据项的字节串。编码在附录 B 的表7中总结，以初始字节为索引。编码器必须（MUST）只生成格式良好的编码数据项。解码器在遇到不是格式良好的编码 CBOR 数据项的输入时，必须不（MUST NOT）返回解码数据项（这并不影响诊断和恢复工具的有用性，这些工具可能从损坏的编码 CBOR 数据项中提供一些信息）。

每个编码数据项的初始字节包含关于主类型（major type，高阶 3 位，第 3.1 节中描述）和附加信息（additional information，低阶 5 位）的信息。除少数例外，附加信息的值描述了如何加载非负整数“参数”（argument）：

- **小于 24**：参数的值是附加信息的值。
- **24，25，26 或 27**：参数的值分别在接下来的 1，2，4 或 8 个字节中，以网络字节序存储。对于主类型 7 和附加信息值 25，26，27，这些字节不作为整数参数使用，而是作为浮点数值（见第 3.3 节）。
- **28，29，30**：这些值保留用于未来添加到 CBOR 格式。在 CBOR 的当前版本中，这些编码项是格式不正确的。
- **31**：不生成参数值。如果主类型为 0，1 或 6，则编码项格式不正确。对于主类型 2 到 5，该项的长度是不定的，对于主类型 7，该字节根本不构成数据项，而是终止不定长度项；所有这些都在第 3.2 节中描述。

初始字节和任何消耗在构造参数上的额外字节统称为数据项的头部。

该参数的含义取决于主类型。例如，在主类型 0 中，参数就是数据项本身的值（在主类型 1 中，数据项的值是根据参数计算的）；在主类型 2 和 3 中，它给出了后面的字符串数据的字节长度；在主类型 4 和 5 中，它用于确定包含的数据项的数量。

如果编码的字节序列在数据项结束前就结束了，那么该项的格式不正确。如果在最外层编码项解码后，编码的字节序列仍然有剩余字节，那么该编码不是单个格式良好的 CBOR 项。根据应用程序的需求，解码器可以将编码视为格式不正确，或者只是将剩余字节的开始标识给应用程序。

CBOR 解码器实现可以基于具有所有 256 个初始字节的已定义值（[表 7][Table 7]）的跳转表。受限制实现中的解码器可以使用初始字节和后续字节的结构来实现更紧凑的代码（参见附录 C 以获取这可能看起来的粗略印象）。

### 3.1. 主要类型（Major Types）

<details>
  <summary>Expand original text</summary>

The following lists the major types and the additional information and other bytes associated with the type.

- **Major type 0**: An unsigned integer in the range 0..2^64-1 inclusive. The value of the encoded item is the argument itself. For example, the integer 10 is denoted as the one byte 0b000_01010 (major type 0, additional information 10). The integer 500 would be 0b000_11001 (major type 0, additional information 25) followed by the two bytes 0x01f4, which is 500 in decimal.
- **Major type 1**: A negative integer in the range -2^64..-1 inclusive. The value of the item is -1 minus the argument. For example, the integer -500 would be 0b001_11001 (major type 1, additional information 25) followed by the two bytes 0x01f3, which is 499 in decimal.
- **Major type 2**: A byte string. The number of bytes in the string is equal to the argument. For example, a byte string whose length is 5 would have an initial byte of 0b010_00101 (major type 2, additional information 5 for the length), followed by 5 bytes of binary content. A byte string whose length is 500 would have 3 initial bytes of 0b010_11001 (major type 2, additional information 25 to indicate a two-byte length) followed by the two bytes 0x01f4 for a length of 500, followed by 500 bytes of binary content.
- **Major type 3**: A text string (Section 2) encoded as UTF-8 [RFC3629][RFC3629]. The number of bytes in the string is equal to the argument. A string containing an invalid UTF-8 sequence is well-formed but invalid (Section 1.2). This type is provided for systems that need to interpret or display human-readable text, and allows the differentiation between unstructured bytes and text that has a specified repertoire (that of Unicode) and encoding (UTF-8). In contrast to formats such as JSON, the Unicode characters in this type are never escaped. Thus, a newline character (U+000A) is always represented in a string as the byte 0x0a, and never as the bytes 0x5c6e (the characters "\" and "n") nor as 0x5c7530303061 (the characters "\", "u", "0", "0", "0", and "a").
- **Major type 4**: An array of data items. In other formats, arrays are also called lists, sequences, or tuples (a "CBOR sequence" is something slightly different, though [RFC8742][RFC8742]). The argument is the number of data items in the array. Items in an array do not need to all be of the same type. For example, an array that contains 10 items of any type would have an initial byte of 0b100_01010 (major type 4, additional information 10 for the length) followed by the 10 remaining items.
- **Major type 5**: A map of pairs of data items. Maps are also called tables, dictionaries, hashes, or objects (in JSON). A map is comprised of pairs of data items, each pair consisting of a key that is immediately followed by a value. The argument is the number of pairs of data items in the map. For example, a map that contains 9 pairs would have an initial byte of 0b101_01001 (major type 5, additional information 9 for the number of pairs) followed by the 18 remaining items. The first item is the first key, the second item is the first value, the third item is the second key, and so on. Because items in a map come in pairs, their total number is always even: a map that contains an odd number of items (no value data present after the last key data item) is not well-formed. A map that has duplicate keys may be well-formed, but it is not valid, and thus it causes indeterminate decoding; see also Section 5.6.
- **Major type 6**: A tagged data item ("tag") whose tag number, an integer in the range 0..2^64-1 inclusive, is the argument and whose enclosed data item (tag content) is the single encoded data item that follows the head. See Section 3.4.
- **Major type 7**: Floating-point numbers and simple values, as well as the "break" stop code. See Section 3.3.

These eight major types lead to a simple table showing which of the 256 possible values for the initial byte of a data item are used ([Table 7][Table 7]).

In major types 6 and 7, many of the possible values are reserved for future specification. See Section 9 for more information on these values.

Table 1 summarizes the major types defined by CBOR, ignoring Section 3.2 for now. The number N in this table stands for the argument.

| Major Type | Meaning               | Content                         |
| ---------- | --------------------- | ------------------------------- |
| 0          | unsigned integer N    | -                               |
| 1          | negative integer -1-N | -                               |
| 2          | byte string           | N bytes                         |
| 3          | text string           | N bytes (UTF-8 text)            |
| 4          | array                 | N data items (elements)         |
| 5          | map                   | 2N data items (key/value pairs) |
| 6          | tag of number N       | 1 data item                     |
| 7          | simple/float          | -                               |
Table 1: Overview over the Definite-Length Use of CBOR Major Types (N = Argument)

</details>

以下列表介绍了各个主要类型及与类型相关的附加信息和其他字节。

- **主类型 0**：范围在 0..2^64-1（包括）之间的非负整数（unsigned integer）。编码项的值就是参数本身。例如，整数 10 表示为一个字节 0b000_01010（主类型 0，附加信息 10）。整数 500 为 0b000_11001（主类型 0，附加信息 25），后跟两个字节 0x01f4，即十进制中的 500。
- **主类型 1**：范围在 -2^64..-1（包括）之间的负整数（negative integer）。项的值是 -1 减去参数。例如，整数 -500 将为 0b001_11001（主类型 1，附加信息 25），后跟两个字节 0x01f3，即十进制中的 499。
- **主类型 2**：字节串（byte string）。字符串中的字节数等于参数。例如，长度为 5 的字节串的初始字节为 0b010_00101（主类型 2，附加信息 5 表示长度），后跟 5 个二进制内容字节。长度为 500 的字节串的前 3 个字节为 0b010_11001（主类型 2，附加信息 25 表示两字节长度），后跟两个字节 0x01f4 作为长度 500，然后跟着 500 个二进制内容字节。
- **主类型 3**：以 UTF-8 [RFC3629][RFC3629] 编码的文本串（text string，第 2 节）。字符串中的字节数等于参数。包含无效 UTF-8 序列的字符串是格式良好（well-formed）但无效（invalid）的（第 1.2 节）。此类型适用于需要解释或显示人类可读文本的系统，并允许区分无结构字节和具有指定字符集（Unicode）和编码（UTF-8）的文本。与 JSON 等格式不同，此类型中的 Unicode 字符永远不会被转义。因此，换行符（U+000A）在字符串中总是表示为字节 0x0a，而不是字节 0x5c6e（字符 "\" 和 "n"）或 0x5c7530303061（字符 "\", "u", "0", "0", "0", 和 "a"）。
- **主类型 4**：由数据项组成的数组（array）。在其他格式中，数组也被称为列表（lists）、序列（sequences）或元组（tuples）（尽管 "CBOR sequence" 是稍微不同的东西 [RFC8742][RFC8742]）。参数是数组中的数据项数量。数组中的项不需要全部是相同类型的。例如，包含任意类型的 10 个项的数组的初始字节为 0b100_01010（主类型 4，附加信息 10 表示长度），后跟 10 个剩余项。
- **主类型 5**：一组数据项对的映射（map）。映射也称为表（tables）、字典（dictionaries）、哈希（hashes）或对象（objects，在 JSON 中）。映射由数据项对组成，每对包括一个紧跟其后的键和值。参数是映射中数据项对的数量。例如，包含 9 对的映射的初始字节为 0b101_01001（主类型 5，附加信息 9 表示对数），后跟 18 个剩余项。第一个项是第一个键，第二个项是第一个值，第三个项是第二个键，依此类推。因为映射中的项成对出现，所以它们的总数总是偶数：包含奇数个项的映射（在最后一个键数据项之后没有值数据项）格式不正确。具有重复键的映射可能格式良好，但无效，因此导致不确定解码；另请参见第 5.6 节。
- **主类型 6**：一个带有标签的数据项（tag），其标签号是范围在 0..2^64-1（包括）之间的整数，参数为该整数，其包含的数据项（标签内容）是紧跟在头部后面的单个编码数据项。请参阅第 3.4 节。
- **主类型 7**：简单值（simple values）和浮点数（floating-point numbers），以及 `break` 停止码。请参阅第 3.3 节。

这八个主要类型形成了一个简单的表格，显示了数据项的初始字节的 256 个可能值中的哪些值被使用（[表 7][Table 7]）。

在主类型 6 和 7 中，许多可能的值都是为未来规范保留的。有关这些值的更多信息，请参阅第 9 节。

表格 1 总结了 CBOR 定义的主要类型，暂时忽略第 3.2 节。表格中的数字 N 表示参数。

| Major Type | Meaning                      | Content                         |
| ---------- | ---------------------------- | ------------------------------- |
| 0          | 非负整数 unsigned integer N  | -                               |
| 1          | 负整数 negative integer -1-N | -                               |
| 2          | 字节串 byte string           | N bytes                         |
| 3          | 文本串 text string           | N bytes (UTF-8 text)            |
| 4          | 数组 array                   | N data items (elements)         |
| 5          | 映射 map                     | 2N data items (key/value pairs) |
| 6          | 标签 tag of number N         | 1 data item                     |
| 7          | 简单值/浮点数 simple/float   | -                               |
Table 1: CBOR 主类型的固定长度用法概述（N = 参数）

### 3.2. 一些主类型的不定长度（Indefinite Lengths for Some Major Types）

<details>
  <summary>Expand original text</summary>

Four CBOR items (arrays, maps, byte strings, and text strings) can be encoded with an indefinite length using additional information value 31. This is useful if the encoding of the item needs to begin before the number of items inside the array or map, or the total length of the string, is known. (The ability to start sending a data item before all of it is known is often referred to as "streaming" within that data item.)

Indefinite-length arrays and maps are dealt with differently than indefinite-length strings (byte strings and text strings).
</details>

四种 CBOR 数据项（数组、映射、字节串和文本串）可以使用附加信息值 31 编码为不定长度（indefinite length）。这在需要在已知数组或映射内的项数量或字符串的总长度之前开始对项进行编码时非常有用。（在数据项内开始发送数据项之前，常常称为“流式”处理。）

不定长度数组和映射与不定长度字符串（字节串和文本串）的处理方式不同。

#### 3.2.1. `break` 停止码（The "break" Stop Code）

<details>
  <summary>Expand original text</summary>

The "break" stop code is encoded with major type 7 and additional information value 31 (0b111_11111). It is not itself a data item: it is just a syntactic feature to close an indefinite-length item.

If the "break" stop code appears where a data item is expected, other than directly inside an indefinite-length string, array, or map -- for example, directly inside a definite-length array or map -- the enclosing item is not well-formed.
</details>

`break` 停止码使用主类型 7 和附加信息值 31 编码（0b111_11111）。它本身不是数据项：它只是一个语法特性，用于关闭不定长度项。

如果 `break` 停止码出现在预期为数据项的位置，除了直接在不定长度字符串、数组或映射内部之外 - 例如，直接在定长数组或映射内部 - 则封装项的格式不正确。

#### 3.2.2. 不定长度数组和映射（Indefinite-Length Arrays and Maps）

<details>
  <summary>Expand original text</summary>

Indefinite-length arrays and maps are represented using their major type with the additional information value of 31, followed by an arbitrary-length sequence of zero or more items for an array or key/value pairs for a map, followed by the "break" stop code (Section 3.2.1). In other words, indefinite-length arrays and maps look identical to other arrays and maps except for beginning with the additional information value of 31 and ending with the "break" stop code.

If the "break" stop code appears after a key in a map, in place of that key's value, the map is not well-formed.

There is no restriction against nesting indefinite-length array or map items. A "break" only terminates a single item, so nested indefinite-length items need exactly as many "break" stop codes as there are type bytes starting an indefinite-length item.

For example, assume an encoder wants to represent the abstract array [1, [2, 3], [4, 5]]. The definite-length encoding would be:

```txt
0x8301820203820405
83        -- Array of length 3
   01     -- 1
   82     -- Array of length 2
      02  -- 2
      03  -- 3
   82     -- Array of length 2
      04  -- 4
      05  -- 5
```

Indefinite-length encoding could be applied independently to each of the three arrays encoded in this data item, as required, leading to representations such as:

```txt
0x9f018202039f0405ffff
9F        -- Start indefinite-length array
   01     -- 1
   82     -- Array of length 2
      02  -- 2
      03  -- 3
   9F     -- Start indefinite-length array
      04  -- 4
      05  -- 5
      FF  -- "break" (inner array)
   FF     -- "break" (outer array)
0x9f01820203820405ff
9F        -- Start indefinite-length array
   01     -- 1
   82     -- Array of length 2
      02  -- 2
      03  -- 3
   82     -- Array of length 2
      04  -- 4
      05  -- 5
   FF     -- "break"
0x83018202039f0405ff
83        -- Array of length 3
   01     -- 1
   82     -- Array of length 2
      02  -- 2
      03  -- 3
   9F     -- Start indefinite-length array
      04  -- 4
      05  -- 5
      FF  -- "break"
0x83019f0203ff820405
83        -- Array of length 3
   01     -- 1
   9F     -- Start indefinite-length array
      02  -- 2
      03  -- 3
      FF  -- "break"
   82     -- Array of length 2
      04  -- 4
      05  -- 5
```

An example of an indefinite-length map (that happens to have two key/value pairs) might be:

```txt
0xbf6346756ef563416d7421ff
BF           -- Start indefinite-length map
   63        -- First key, UTF-8 string length 3
      46756e --   "Fun"
   F5        -- First value, true
   63        -- Second key, UTF-8 string length 3
      416d74 --   "Amt"
   21        -- Second value, -2
   FF        -- "break"
```

</details>

不定长度数组和映射使用其主类型和附加信息值 31 表示，后跟零个或多个数据项的任意长度序列（用于数组）或键/值对（用于映射），然后是 `break` 停止码（第 3.2.1 节）。换句话说，不定长度数组和映射看起来与其他数组和映射完全相同，只是以附加信息值 31 开头并以 `break` 停止码结尾。

如果 `break` 停止码出现在映射中的键之后，取代该键的值，则映射的格式不正确。

嵌套不定长度数组或映射项没有限制。`break` 仅终止单个数据项，因此嵌套的不定长度数据项需要与开始不定长度数据项的类型字节一样多的 `break` 停止码。

例如，假设编码器想要表示抽象数组 [1, [2, 3], [4, 5]]。定长编码将是：

```txt
0x8301820203820405
83        -- Array of length 3
   01     -- 1
   82     -- Array of length 2
      02  -- 2
      03  -- 3
   82     -- Array of length 2
      04  -- 4
      05  -- 5
```

根据需要，可以将不定长度编码独立应用于此数据项中编码的三个数组，从而导致诸如以下的表示形式：

```txt
0x9f018202039f0405ffff
9F        -- Start indefinite-length array
   01     -- 1
   82     -- Array of length 2
      02  -- 2
      03  -- 3
   9F     -- Start indefinite-length array
      04  -- 4
      05  -- 5
      FF  -- "break" (inner array)
   FF     -- "break" (outer array)
0x9f01820203820405ff
9F        -- Start indefinite-length array
   01     -- 1
   82     -- Array of length 2
      02  -- 2
      03  -- 3
   82     -- Array of length 2
      04  -- 4
      05  -- 5
   FF     -- "break"
0x83018202039f0405ff
83        -- Array of length 3
   01     -- 1
   82     -- Array of length 2
      02  -- 2
      03  -- 3
   9F     -- Start indefinite-length array
      04  -- 4
      05  -- 5
      FF  -- "break"
0x83019f0203ff820405
83        -- Array of length 3
   01     -- 1
   9F     -- Start indefinite-length array
      02  -- 2
      03  -- 3
      FF  -- "break"
   82     -- Array of length 2
      04  -- 4
      05  -- 5
```

一个具有不确定长度的映射的例子（恰好有两个键/值对）可能是：

```txt
0xbf6346756ef563416d7421ff
BF           -- Start indefinite-length map
   63        -- First key, UTF-8 string length 3
      46756e --   "Fun"
   F5        -- First value, true
   63        -- Second key, UTF-8 string length 3
      416d74 --   "Amt"
   21        -- Second value, -2
   FF        -- "break"
```

#### 3.2.3. 不定长度字节串和文本串（Indefinite-Length Byte Strings and Text Strings）

<details>
  <summary>Expand original text</summary>

Indefinite-length strings are represented by a byte containing the major type for byte string or text string with an additional information value of 31, followed by a series of zero or more strings of the specified type ("chunks") that have definite lengths, and finished by the "break" stop code (Section 3.2.1). The data item represented by the indefinite-length string is the concatenation of the chunks. If no chunks are present, the data item is an empty string of the specified type. Zero-length chunks, while not particularly useful, are permitted.

If any item between the indefinite-length string indicator (0b010_11111 or 0b011_11111) and the "break" stop code is not a definite-length string item of the same major type, the string is not well-formed.

The design does not allow nesting indefinite-length strings as chunks into indefinite-length strings. If it were allowed, it would require decoder implementations to keep a stack, or at least a count, of nesting levels. It is unnecessary on the encoder side because the inner indefinite-length string would consist of chunks, and these could instead be put directly into the outer indefinite-length string.

If any definite-length text string inside an indefinite-length text string is invalid, the indefinite-length text string is invalid. Note that this implies that the UTF-8 bytes of a single Unicode code point (scalar value) cannot be spread between chunks: a new chunk of a text string can only be started at a code point boundary.

For example, assume an encoded data item consisting of the bytes:

```txt
0b010_11111 0b010_00100 0xaabbccdd 0b010_00011 0xeeff99 0b111_11111
5F              -- Start indefinite-length byte string
   44           -- Byte string of length 4
      aabbccdd  -- Bytes content
   43           -- Byte string of length 3
      eeff99    -- Bytes content
   FF           -- "break"
```

After decoding, this results in a single byte string with seven bytes: `0xaabbccddeeff99`.
</details>

不定长度字符串由一个字节表示，该字节包含字节串或文本串的主类型和附加信息值 31，后跟指定类型的零个或多个具有定长的字符串“块”（chunks），最后是 `break` 停止码（第 3.2.1 节）。由不定长度字符串表示的数据项是这些块的连接。如果没有块，则数据项为空的指定类型字符串。虽然零长度块不是特别有用，但是允许使用。

如果不定长度字符串指示符（0b010_11111 或 0b011_11111）和 `break` 停止码之间的任何数据项不是相同主类型的定长字符串项，则字符串的格式不正确。

设计不允许将不定长度字符串作为块嵌套到不定长度字符串中。如果允许这样做，将需要解码器实现保持堆栈，或者至少是嵌套级别的计数。在编码器方面是不必要的，因为内部不定长度字符串将由块组成，这些块可以直接放入外部不定长度字符串中。

如果不定长度文本串内的任何定长文本串无效，则不定长度文本串无效。请注意，这意味着单个 Unicode 码点（标量值）的 UTF-8 字节不能在块之间传播：文本串的新块只能在码点边界开始。

例如，假设一个编码数据项由以下字节组成：

```txt
0b010_11111 0b010_00100 0xaabbccdd 0b010_00011 0xeeff99 0b111_11111
5F              -- Start indefinite-length byte string
   44           -- Byte string of length 4
      aabbccdd  -- Bytes content
   43           -- Byte string of length 3
      eeff99    -- Bytes content
   FF           -- "break"
```

解码后，结果是一个简单的 7 字节的字节串: `0xaabbccddeeff99`.

#### 3.2.4. 主类型的不定长度使用总结（Summary of Indefinite-Length Use of Major Types）

<details>
  <summary>Expand original text</summary>

Table 2 summarizes the major types defined by CBOR as used for indefinite-length encoding (with additional information set to 31).

| Major Type | Meaning           | Enclosed up to "break" Stop Code |
| ---------- | ----------------- | -------------------------------- |
| 0          | (not well-formed) | -                                |
| 1          | (not well-formed) | -                                |
| 2          | byte string       | definite-length byte strings     |
| 3          | text string       | definite-length text strings     |
| 4          | array             | data items (elements)            |
| 5          | map               | data items (key/value pairs)     |
| 6          | (not well-formed) | -                                |
| 7          | "break" stop code | -                                |
Table 2: Overview of the Indefinite-Length Use of CBOR Major Types (Additional Information = 31)
</details>

表 2 总结了 CBOR 定义的用于不定长度编码的主类型（附加信息设置为 31）。

| Major Type | Meaning           | Enclosed up to "break" Stop Code |
| ---------- | ----------------- | -------------------------------- |
| 0          | (not well-formed) | -                                |
| 1          | (not well-formed) | -                                |
| 2          | byte string       | definite-length byte strings     |
| 3          | text string       | definite-length text strings     |
| 4          | array             | data items (elements)            |
| 5          | map               | data items (key/value pairs)     |
| 6          | (not well-formed) | -                                |
| 7          | "break" stop code | -                                |
表 2：CBOR 主类型的不定长度使用概述（附加信息 = 31）

### 3.3. 浮点数和无内容的值（Floating-Point Numbers and Values with No Content）

<details>
  <summary>Expand original text</summary>

Major type 7 is for two types of data: floating-point numbers and "simple values" that do not need any content. Each value of the 5-bit additional information in the initial byte has its own separate meaning, as defined in Table 3. Like the major types for integers, items of this major type do not carry content data; all the information is in the initial bytes (the head).

| 5-Bit Value | Semantics                                                     |
| ----------- | ------------------------------------------------------------- |
| 0..23       | Simple value (value 0..23)                                    |
| 24          | Simple value (value 32..255 in following byte)                |
| 25          | IEEE 754 Half-Precision Float (16 bits follow)                |
| 26          | IEEE 754 Single-Precision Float (32 bits follow)              |
| 27          | IEEE 754 Double-Precision Float (64 bits follow)              |
| 28-30       | Reserved, not well-formed in the present document             |
| 31          | "break" stop code for indefinite-length items (Section 3.2.1) |
Table 3: Values for Additional Information in Major Type 7

As with all other major types, the 5-bit value 24 signifies a single-byte extension: it is followed by an additional byte to represent the simple value. (To minimize confusion, only the values 32 to 255 are used.) This maintains the structure of the initial bytes: as for the other major types, the length of these always depends on the additional information in the first byte. Table 4 lists the numeric values assigned and available for simple values.

| Value   | Semantics    |
| ------- | ------------ |
| 0..19   | (unassigned) |
| 20      | false        |
| 21      | true         |
| 22      | null         |
| 23      | undefined    |
| 24..31  | (reserved)   |
| 32..255 | (unassigned) |
Table 4: Simple Values

An encoder MUST NOT issue two-byte sequences that start with 0xf8 (major type 7, additional information 24) and continue with a byte less than 0x20 (32 decimal). Such sequences are not well-formed. (This implies that an encoder cannot encode false, true, null, or undefined in two-byte sequences and that only the one-byte variants of these are well-formed; more generally speaking, each simple value only has a single representation variant).

The 5-bit values of 25, 26, and 27 are for 16-bit, 32-bit, and 64-bit IEEE 754 binary floating-point values [IEEE754][IEEE754]. These floating-point values are encoded in the additional bytes of the appropriate size. (See Appendix D for some information about 16-bit floating-point numbers.)
</details>

主类型 7 用于两种类型的数据：浮点数和不需要任何内容的 "简单值"。初始字节中 5 位附加信息的每个值都有自己单独的含义，如表 3 所定义。与整数的主类型一样，此主类型的数据项不携带内容数据；所有信息都在初始字节（头部）中。

| 5-Bit Value | Semantics                                                     |
| ----------- | ------------------------------------------------------------- |
| 0..23       | Simple value (value 0..23)                                    |
| 24          | Simple value (value 32..255 in following byte)                |
| 25          | IEEE 754 Half-Precision Float (16 bits follow)                |
| 26          | IEEE 754 Single-Precision Float (32 bits follow)              |
| 27          | IEEE 754 Double-Precision Float (64 bits follow)              |
| 28-30       | Reserved, not well-formed in the present document             |
| 31          | "break" stop code for indefinite-length items (Section 3.2.1) |
表 3：主类型 7 中附加信息的值

与所有其他主类型一样，5 位值 24 表示单字节扩展：后跟一个附加字节表示简单值。（为了最小化混淆，只使用 32 到 255 的值。）这保持了初始字节的结构：对于其他主类型，这些长度始终取决于第一个字节中的附加信息。表 4 列出了分配和可用于简单值的数值。

| Value   | Semantics    |
| ------- | ------------ |
| 0..19   | (unassigned) |
| 20      | false        |
| 21      | true         |
| 22      | null         |
| 23      | undefined    |
| 24..31  | (reserved)   |
| 32..255 | (unassigned) |
表 4：简单值

编码器在开始为 0xf8（主类型 7，附加信息 24）和继续为小于 0x20（32 十进制）的字节时，不得（MUST NOT）发出两字节序列。这样的序列格式不正确。（这意味着编码器不能在两字节序列中编码 false、true、null 或 undefined，且这些值的单字节变体格式正确；更一般地说，每个简单值只有一个表示变体）。

5 位值 25、26 和 27 分别用于 16 位、32 位和 64 位 IEEE 754 二进制浮点值 [IEEE754][IEEE754]。这些浮点值在适当大小的附加字节中进行编码。（关于 16 位浮点数的一些信息，请参见附录 D。）

### 3.4. 标签化数据项（Tagging of Items）

<details>
  <summary>Expand original text</summary>

In CBOR, a data item can be enclosed by a tag to give it some additional semantics, as uniquely identified by a tag number. The tag is major type 6, its argument (Section 3) indicates the tag number, and it contains a single enclosed data item, the tag content. (If a tag requires further structure to its content, this structure is provided by the enclosed data item.) We use the term tag for the entire data item consisting of both a tag number and the tag content: the tag content is the data item that is being tagged.

For example, assume that a byte string of length 12 is marked with a tag of number 2 to indicate it is an unsigned bignum (Section 3.4.3). The encoded data item would start with a byte 0b110_00010 (major type 6, additional information 2 for the tag number) followed by the encoded tag content: 0b010_01100 (major type 2, additional information 12 for the length) followed by the 12 bytes of the bignum.

In the extended generic data model, a tag number's definition describes the additional semantics conveyed with the tag number. These semantics may include equivalence of some tagged data items with other data items, including some that can be represented in the basic generic data model. For instance, 0xc24101, a bignum the tag content of which is the byte string with the single byte 0x01, is equivalent to an integer 1, which could also be encoded as 0x01, 0x1801, or 0x190001. The tag definition may specify a preferred serialization (Section 4.1) that is recommended for generic encoders; this may prefer basic generic data model representations over ones that employ a tag.

The tag definition usually defines which nested data items are valid for such tags. Tag definitions may restrict their content to a very specific syntactic structure, as the tags defined in this document do, or they may define their content more semantically. An example for the latter is how tags 40 and 1040 accept multiple ways to represent arrays [RFC8746][RFC8746].

As a matter of convention, many tags do not accept null or undefined values as tag content; instead, the expectation is that a null or undefined value can be used in place of the entire tag; Section 3.4.2 provides some further considerations for one specific tag about the handling of this convention in application protocols and in mapping to platform types.

Decoders do not need to understand tags of every tag number, and tags may be of little value in applications where the implementation creating a particular CBOR data item and the implementation decoding that stream know the semantic meaning of each item in the data flow. The primary purpose of tags in this specification is to define common data types such as dates. A secondary purpose is to provide conversion hints when it is foreseen that the CBOR data item needs to be translated into a different format, requiring hints about the content of items. Understanding the semantics of tags is optional for a decoder; it can simply present both the tag number and the tag content to the application, without interpreting the additional semantics of the tag.

A tag applies semantics to the data item it encloses. Tags can nest: if tag A encloses tag B, which encloses data item C, tag A applies to the result of applying tag B on data item C.

IANA maintains a registry of tag numbers as described in Section 9.2. Table 5 provides a list of tag numbers that were defined in [RFC7049][RFC7049] with definitions in the rest of this section. (Tag number 35 was also defined in [RFC7049][RFC7049]; a discussion of this tag number follows in Section 3.4.5.3.) Note that many other tag numbers have been defined since the publication of [RFC7049][RFC7049]; see the registry described at Section 9.2 for the complete list.

| Tag   | Data Item        | Semantics                                                      |
| ----- | ---------------- | -------------------------------------------------------------- |
| 0     | text string      | Standard date/time string; see Section 3.4.1                   |
| 1     | integer or float | Epoch-based date/time; see Section 3.4.2                       |
| 2     | byte string      | Unsigned bignum; see Section 3.4.3                             |
| 3     | byte string      | Negative bignum; see Section 3.4.3                             |
| 4     | array            | Decimal fraction; see Section 3.4.4                            |
| 5     | array            | Bigfloat; see Section 3.4.4                                    |
| 21    | (any)            | Expected conversion to base64url encoding; see Section 3.4.5.2 |
| 22    | (any)            | Expected conversion to base64 encoding; see Section 3.4.5.2    |
| 23    | (any)            | Expected conversion to base16 encoding; see Section 3.4.5.2    |
| 24    | byte string      | Encoded CBOR data item; see Section 3.4.5.1                    |
| 32    | text string      | URI; see Section 3.4.5.3                                       |
| 33    | text string      | base64url; see Section 3.4.5.3                                 |
| 34    | text string      | base64; see Section 3.4.5.3                                    |
| 36    | text string      | MIME message; see Section 3.4.5.3                              |
| 55799 | (any)            | Self-described CBOR; see Section 3.4.6                         |
Table 5: Tag Numbers Defined in RFC 7049

Conceptually, tags are interpreted in the generic data model, not at (de-)serialization time. A small number of tags (at this time, tag number 25 and tag number 29 [IANA.cbor-tags][IANA.cbor-tags]) have been registered with semantics that may require processing at (de-)serialization time: the decoder needs to be aware of, and the encoder needs to be in control of, the exact sequence in which data items are encoded into the CBOR data item. This means these tags cannot be implemented on top of an arbitrary generic CBOR encoder/decoder (which might not reflect the serialization order for entries in a map at the data model level and vice versa); their implementation therefore typically needs to be integrated into the generic encoder/decoder. The definition of new tags with this property is NOT RECOMMENDED.

IANA allocated tag numbers 65535, 4294967295, and 18446744073709551615 (binary all-ones in 16-bit, 32-bit, and 64-bit). These can be used as a convenience for implementers who want a single-integer data structure to indicate either the presence of a specific tag or absence of a tag. That allocation is described in Section 10 of [CBOR-TAGS][CBOR-TAGS]. These tags are not intended to occur in actual CBOR data items; implementations MAY flag such an occurrence as an error.

Protocols can extend the generic data model (Section 2) with data items representing points in time by using tag numbers 0 and 1, with arbitrarily sized integers by using tag numbers 2 and 3, and with floating-point values of arbitrary size and precision by using tag numbers 4 and 5.
</details>

在 CBOR 中，可以用标签将数据项封装起来，以赋予它由标签号唯一标识的一些附加语义。标签是主类型 6，其参数（第 3 节）表示标签号（tag number），并包含单个封装的数据项，即标签内容（tag content）。（如果标签需要为其内容提供进一步的结构，则此结构由封装的数据项提供。）我们使用术语标签表示由标签号和标签内容组成的整个数据项：标签内容是被标记的数据项。

例如，假设一个长度为 12 的字节串用标签号 2 标记，表示它是一个非负大整数（第 3.4.3 节）。编码的数据项将从字节 0b110_00010（主类型 6，附加信息 2 表示标签号）开始，后跟编码的标签内容：0b010_01100（主类型 2，附加信息 12 表示长度），然后是大数的 12 个字节。

在扩展的通用数据模型中，标签号的定义描述了与标签号一起传输的附加语义。这些语义可能包括某些标签数据项与其他数据项的等价性，包括可以在基本通用数据模型中表示的某些数据项。例如，0xc24101 是一个非负大整数，其标签内容是具有单个字节 0x01 的字节串，等价于整数 1，也可以编码为 0x01、0x1801 或 0x190001。标签定义可以指定建议用于通用编码器的首选序列化（第 4.1 节）；这可能更倾向于使用基本通用数据模型表示而不是使用标签。

标签定义通常定义了对于这些标签有效的嵌套数据项。标签定义可以将其内容限制为非常特定的语法结构，就像本文档中定义的标签那样，或者可以更语义地定义其内容。后者的一个例子是标签 40 和 1040 如何接受表示数组的多种方式 [RFC8746][RFC8746]。

按照惯例，许多标签不接受空值或未定义值作为标签内容；相反，期望在整个标签的位置可以使用空值或未定义值；第 3.4.2 节为一个特定标签提供了关于在应用程序协议和映射到平台类型中处理此约定的一些进一步考虑。

解码器不需要了解每个标签号的标签，而且在实现创建特定 CBOR 数据项和解码该流的实现知道数据流中每个数据项的语义含义的应用中，标签可能价值不大。本规范中标签的主要目的是定义诸如日期之类的通用数据类型。次要目的是在预见到 CBOR 数据项需要转换为不同格式时，提供关于数据项内容的提示。解码器理解标签的语义是可选的；它可以在不解释标签的附加语义的情况下，将标签号和标签内容呈现给应用程序。

标签为其封装的数据项应用语义。标签可以嵌套：如果标签 A 封装标签 B，标签 B 封装数据项 C，那么标签 A 将应用于将标签 B 应用于数据项 C 的结果上。

IANA 按照第 9.2 节的描述维护一个标签号注册表。表 5 提供了在 [RFC7049][RFC7049] 中定义的标签号列表，本节其余部分给出了定义。（标签号 35 也在 [RFC7049][RFC7049] 中定义；关于此标签号的讨论见第 3.4.5.3 节。）请注意，自 [RFC7049][RFC7049] 出版以来已经定义了许多其他标签号；有关完整列表，请参见第 9.2 节描述的注册表。

| Tag   | Data Item        | Semantics                                                      |
| ----- | ---------------- | -------------------------------------------------------------- |
| 0     | text string      | Standard date/time string; see Section 3.4.1                   |
| 1     | integer or float | Epoch-based date/time; see Section 3.4.2                       |
| 2     | byte string      | Unsigned bignum; see Section 3.4.3                             |
| 3     | byte string      | Negative bignum; see Section 3.4.3                             |
| 4     | array            | Decimal fraction; see Section 3.4.4                            |
| 5     | array            | Bigfloat; see Section 3.4.4                                    |
| 21    | (any)            | Expected conversion to base64url encoding; see Section 3.4.5.2 |
| 22    | (any)            | Expected conversion to base64 encoding; see Section 3.4.5.2    |
| 23    | (any)            | Expected conversion to base16 encoding; see Section 3.4.5.2    |
| 24    | byte string      | Encoded CBOR data item; see Section 3.4.5.1                    |
| 32    | text string      | URI; see Section 3.4.5.3                                       |
| 33    | text string      | base64url; see Section 3.4.5.3                                 |
| 34    | text string      | base64; see Section 3.4.5.3                                    |
| 36    | text string      | MIME message; see Section 3.4.5.3                              |
| 55799 | (any)            | Self-described CBOR; see Section 3.4.6                         |
表 5：RFC 7049 中定义的标签号

从概念上讲，标签在通用数据模型中解释，而不是在（反）序列化时。少量标签（目前为止，标签号 25 和标签号 29 [IANA.cbor-tags][IANA.cbor-tags]）已注册具有可能需要在（反）序列化时处理的语义：解码器需要了解，并且编码器需要控制数据项被编码到 CBOR 数据项的确切顺序。这意味着这些标签不能在任意通用 CBOR 编码器/解码器之上实现（在数据模型级别可能无法反映映射中条目的序列化顺序，反之亦然）；因此，它们的实现通常需要集成到通用编码器/解码器中。不建议（NOT RECOMMENDED）定义具有此属性的新标签。

IANA 为标签号分配了 65535、4294967295 和 18446744073709551615（在 16 位、32 位和 64 位中的二进制全 1）。这可以方便实现者，他们希望使用单个整数数据结构来表示特定标签的存在或标签的缺失。该分配在 [CBOR-TAGS][CBOR-TAGS] 的第 10 节中有描述。这些标签并非实际 CBOR 数据项中出现的标签；实现可以（MAY）将此类出现标记为错误。

协议可以使用标签号 0 和 1 扩展通用数据模型（第 2 节），表示时间点的数据项；通过使用标签号 2 和 3，表示任意大小的整数；通过使用标签号 4 和 5，表示任意大小和精度的浮点值。

#### 3.4.1. 标准日期/时间字符串（Standard Date/Time String）

<details>
  <summary>Expand original text</summary>

Tag number 0 contains a text string in the standard format described by the date-time production in [RFC3339][RFC3339], as refined by Section 3.3 of [RFC4287][RFC4287], representing the point in time described there. A nested item of another type or a text string that doesn't match the format described in [RFC4287][RFC4287] is invalid.
</details>

标签号 0 包含一个文本串，采用 [RFC3339][RFC3339] 中描述的 `date-time` 产生的标准格式，由 [RFC4287][RFC4287] 第 3.3 节进行细化，表示其中描述的时间点。嵌套的其他类型的项或不符合 [RFC4287][RFC4287] 中描述的格式的文本串是无效的。

#### 3.4.2. 基于纪元的日期/时间（Epoch-Based Date/Time）

<details>
  <summary>Expand original text</summary>

Tag number 1 contains a numerical value counting the number of seconds from 1970-01-01T00:00Z in UTC time to the represented point in civil time.

The tag content MUST be an unsigned or negative integer (major types 0 and 1) or a floating-point number (major type 7 with additional information 25, 26, or 27). Other contained types are invalid.

Nonnegative values (major type 0 and nonnegative floating-point numbers) stand for time values on or after 1970-01-01T00:00Z UTC and are interpreted according to POSIX [TIME_T]. (POSIX time is also known as "UNIX Epoch time".) Leap seconds are handled specially by POSIX time, and this results in a 1-second discontinuity several times per decade. Note that applications that require the expression of times beyond early 2106 cannot leave out support of 64-bit integers for the tag content.

Negative values (major type 1 and negative floating-point numbers) are interpreted as determined by the application requirements as there is no universal standard for UTC count-of-seconds time before 1970-01-01T00:00Z (this is particularly true for points in time that precede discontinuities in national calendars). The same applies to non-finite values.

To indicate fractional seconds, floating-point values can be used within tag number 1 instead of integer values. Note that this generally requires binary64 support, as binary16 and binary32 provide nonzero fractions of seconds only for a short period of time around early 1970. An application that requires tag number 1 support may restrict the tag content to be an integer (or a floating-point value) only.

Note that platform types for date/time may include null or undefined values, which may also be desirable at an application protocol level. While emitting tag number 1 values with non-finite tag content values (e.g., with NaN for undefined date/time values or with Infinity for an expiry date that is not set) may seem an obvious way to handle this, using untagged null or undefined avoids the use of non-finites and results in a shorter encoding. Application protocol designers are encouraged to consider these cases and include clear guidelines for handling them.
</details>

标签号 1 包含一个数字值，表示从 1970-01-01T00:00Z 的 UTC 时间到表示的公历时间点之间的秒数。

标签内容必须（MUST）是非负整数或负整数（主类型 0 和 1）或浮点数（主类型 7，附加信息为 25、26 或 27）。其他包含的类型是无效的。

非负值（主类型 0 和非负浮点数）表示 1970-01-01T00:00Z UTC 之后的时间值，并根据 POSIX [TIME_T] 进行解释。（POSIX 时间也被称为 "UNIX Epoch time"。）闰秒在 POSIX 时间中得到特殊处理，并且在每十年内多次出现 1 秒的不连续性。请注意，需要表示 2106 年初之后的时间的应用程序不能省略对标签内容的 64 位整数支持。

负值（主类型 1 和负浮点数）的解释由应用程序需求决定，因为在 1970-01-01T00:00Z 之前的 UTC 秒计时没有通用标准（对于在国家历法中出现的不连续时间点之前的时间点尤其如此）。对于非有限值也是如此。

要表示秒的小数部分，可以在标签号 1 内使用浮点值而不是整数值。请注意，这通常需要 binary64 支持，因为 binary16 和 binary32 仅在 1970 年初附近的短时间内提供非零秒的分数。需要标签号 1 支持的应用程序可能会限制标签内容仅为整数（或浮点值）。

请注意，平台类型的日期/时间可能包括空值或未定义的值，这在应用程序协议层面上可能也是需要的。虽然发出具有非有限标签内容值的标签号 1 值（例如，对于未定义的日期/时间值使用 NaN 或对于未设置的到期日期使用 Infinity）可能看起来是处理这个问题的明显方法，但使用未标记的空值或未定义值可以避免使用非有限值并产生更短的编码。应用协议设计者鼓励考虑这些情况并为处理它们提供明确的指南。

#### 3.4.3. 大整数（Bignums）

<details>
  <summary>Expand original text</summary>

Protocols using tag numbers 2 and 3 extend the generic data model (Section 2) with "bignums" representing arbitrarily sized integers. In the basic generic data model, bignum values are not equal to integers from the same model, but the extended generic data model created by this tag definition defines equivalence based on numeric value, and preferred serialization (Section 4.1) never makes use of bignums that also can be expressed as basic integers (see below).

Bignums are encoded as a byte string data item, which is interpreted as an unsigned integer n in network byte order. Contained items of other types are invalid. For tag number 2, the value of the bignum is n. For tag number 3, the value of the bignum is -1 - n. The preferred serialization of the byte string is to leave out any leading zeroes (note that this means the preferred serialization for n = 0 is the empty byte string, but see below). Decoders that understand these tags MUST be able to decode bignums that do have leading zeroes. The preferred serialization of an integer that can be represented using major type 0 or 1 is to encode it this way instead of as a bignum (which means that the empty string never occurs in a bignum when using preferred serialization). Note that this means the non-preferred choice of a bignum representation instead of a basic integer for encoding a number is not intended to have application semantics (just as the choice of a longer basic integer representation than needed, such as 0x1800 for 0x00, does not).

For example, the number 18446744073709551616 (2^64) is represented as 0b110_00010 (major type 6, tag number 2), followed by 0b010_01001 (major type 2, length 9), followed by 0x010000000000000000 (one byte 0x01 and eight bytes 0x00). In hexadecimal:

```txt
C2                        -- Tag 2
   49                     -- Byte string of length 9
      010000000000000000  -- Bytes content
```

</details>

使用标签号 2 和 3 的协议通过“大整数”扩展了通用数据模型（第 2 节），表示任意大小的整数。在基本通用数据模型中，大整数值与来自相同模型的整数不相等，但由该标签定义创建的扩展通用数据模型基于数值定义等价，并且首选序列化（第 4.1 节）从不使用也可以表示为基本整数的大整数（见下文）。

大整数被编码为字节串数据项，以网络字节顺序解释为非负整数 n。包含其他类型的项是无效的。对于标签号 2，大整数的值为 n。对于标签号 3，大整数的值为 -1 - n。字节串的首选序列化是省略任何前导零（注意这意味着 n = 0 的首选序列化是空字节串，但请参阅下文）。理解这些标签的解码器必须（MUST）能够解码具有前导零的大整数。可以用主类型 0 或 1 表示的整数的首选序列化是将其编码为这种方式而不是作为大整数（这意味着在使用首选序列化时，空字符串永远不会出现在大整数中）。请注意，这意味着使用大整数表示而不是基本整数表示来编码数字的非首选选择不是为了具有应用程序语义（正如选择比需要的更长的基本整数表示，例如 0x1800 表示 0x00，也不是）。

例如，数字 18446744073709551616（2^64）表示为 0b110_00010（主类型 6，标签号 2），后跟 0b010_01001（主类型 2，长度 9），再跟 0x010000000000000000（一个字节 0x01 和八个字节 0x00）。用十六进制表示为：

```txt
C2                        -- Tag 2
   49                     -- Byte string of length 9
      010000000000000000  -- Bytes content
```

#### 3.4.4. 十进制分数和大浮点数（Decimal Fractions and Bigfloats）

<details>
  <summary>Expand original text</summary>

Protocols using tag number 4 extend the generic data model with data items representing arbitrary-length decimal fractions of the form m*(10^e). Protocols using tag number 5 extend the generic data model with data items representing arbitrary-length binary fractions of the form m*(2^e). As with bignums, values of different types are not equal in the generic data model.

Decimal fractions combine an integer mantissa with a base-10 scaling factor. They are most useful if an application needs the exact representation of a decimal fraction such as 1.1 because there is no exact representation for many decimal fractions in binary floating-point representations.

"Bigfloats" combine an integer mantissa with a base-2 scaling factor. They are binary floating-point values that can exceed the range or the precision of the three IEEE 754 formats supported by CBOR (Section 3.3). Bigfloats may also be used by constrained applications that need some basic binary floating-point capability without the need for supporting IEEE 754.

A decimal fraction or a bigfloat is represented as a tagged array that contains exactly two integer numbers: an exponent e and a mantissa m. Decimal fractions (tag number 4) use base-10 exponents; the value of a decimal fraction data item is m*(10e). Bigfloats (tag number 5) use base-2 exponents; the value of a bigfloat data item is m*(2^e). The exponent e MUST be represented in an integer of major type 0 or 1, while the mantissa can also be a bignum (Section 3.4.3). Contained items with other structures are invalid.

An example of a decimal fraction is the representation of the number 273.15 as 0b110_00100 (major type 6 for tag, additional information 4 for the tag number), followed by 0b100_00010 (major type 4 for the array, additional information 2 for the length of the array), followed by 0b001_00001 (major type 1 for the first integer, additional information 1 for the value of -2), followed by 0b000_11001 (major type 0 for the second integer, additional information 25 for a two-byte value), followed by 0b0110101010110011 (27315 in two bytes). In hexadecimal:

```txt
C4             -- Tag 4
   82          -- Array of length 2
      21       -- -2
      19 6ab3  -- 27315
```

An example of a bigfloat is the representation of the number 1.5 as 0b110_00101 (major type 6 for tag, additional information 5 for the tag number), followed by 0b100_00010 (major type 4 for the array, additional information 2 for the length of the array), followed by 0b001_00000 (major type 1 for the first integer, additional information 0 for the value of -1), followed by 0b000_00011 (major type 0 for the second integer, additional information 3 for the value of 3). In hexadecimal:

```txt
C5             -- Tag 5
   82          -- Array of length 2
      20       -- -1
      03       -- 3
```

Decimal fractions and bigfloats provide no representation of Infinity, -Infinity, or NaN; if these are needed in place of a decimal fraction or bigfloat, the IEEE 754 half-precision representations from Section 3.3 can be used.
</details>

使用标签号 4 的协议通过数据项扩展通用数据模型，表示形式为 m*(10^e) 的任意长度十进制分数。使用标签号 5 的协议通过数据项扩展通用数据模型，表示形式为 m*(2^e) 的任意长度二进制分数。与大整数一样，在通用数据模型中，不同类型的值是不相等的。

十进制分数将整数尾数与基数为 10 的缩放因子结合起来。如果应用程序需要精确表示如 1.1 的十进制分数，十进制分数非常有用，因为在二进制浮点表示中，许多十进制分数没有精确表示。

“大浮点数”将整数尾数与基数为 2 的缩放因子结合起来。它们是二进制浮点值，可以超出 CBOR（第 3.3 节）支持的三种 IEEE 754 格式的范围或精度。在不需要支持 IEEE 754 的情况下，需要一些基本二进制浮点功能的受限应用程序也可以使用大浮点数。

十进制分数或大浮点数表示为包含两个整数：指数 e 和尾数 m 的标记数组。十进制分数（标签号 4）使用基数为 10 的指数；十进制分数数据项的值为 m*(10^e)。大浮点数（标签号 5）使用基数为 2 的指数；大浮点数数据项的值为 m*(2^e)。指数 e 必须用（MUST）主类型为 0 或 1 的整数表示，而尾数还可以是大整数（第 3.4.3 节）。包含其他结构的项是无效的。

一个十进制分数的例子是将数字 273.15 表示为 0b110_00100（主类型 6 用于标签，附加信息 4 用于标签号），接着是 0b100_00010（主类型 4 用于数组，附加信息 2 用于数组的长度），接着是 0b001_00001（主类型 1 用于第一个整数，附加信息 1 用于 -2 的值），接着是 0b000_11001（主类型 0 用于第二个整数，附加信息 25 用于两字节的值），接着是 0b0110101010110011（两字节的 27315）。用十六进制表示：

```txt
C4             -- Tag 4
   82          -- Array of length 2
      21       -- -2
      19 6ab3  -- 27315
```

一个大浮点数的例子是将数字 1.5 表示为 0b110_00101（主类型 6 用于标签，附加信息 5 用于标签号），接着是 0b100_00010（主类型 4 用于数组，附加信息 2 用于数组的长度），接着是 0b001_00000（主类型 1 用于第一个整数，附加信息 0 用于 -1 的值），接着是 0b000_00011（主类型 0 用于第二个整数，附加信息 3 用于 3 的值）。用十六进制表示：

```txt
C5             -- Tag 5
   82          -- Array of length 2
      20       -- -1
      03       -- 3
```

十进制分数和大浮点数没有表示无穷大、负无穷大或 NaN 的表示方法；如果需要用这些值代替十进制分数或大浮点数，可以使用第 3.3 节的 IEEE 754 半精度表示。

#### 3.4.5. 内容提示（Content Hints）

<details>
  <summary>Expand original text</summary>

The tags in this section are for content hints that might be used by generic CBOR processors. These content hints do not extend the generic data model.
</details>

本节中的标签用于通用 CBOR 处理器可能使用的内容提示。这些内容提示不扩展通用数据模型。

##### 3.4.5.1. 编码的 CBOR 数据项（Encoded CBOR Data Item）

<details>
  <summary>Expand original text</summary>

Sometimes it is beneficial to carry an embedded CBOR data item that is not meant to be decoded immediately at the time the enclosing data item is being decoded. Tag number 24 (CBOR data item) can be used to tag the embedded byte string as a single data item encoded in CBOR format. Contained items that aren't byte strings are invalid. A contained byte string is valid if it encodes a well-formed CBOR data item; validity checking of the decoded CBOR item is not required for tag validity (but could be offered by a generic decoder as a special option).
</details>

有时在解码外部数据项时不立即解码嵌入式 CBOR 数据项是有益的。标签号 24（CBOR 数据项）可用于将嵌入式字节串标记为以 CBOR 格式编码的单个数据项。不是字节串的包含项是无效的。包含的字节串在编码格式良好的 CBOR 数据项时是有效的；对解码的 CBOR 项进行有效性检查对于标签有效性并非必需（但通用解码器可以作为特殊选项提供）。

##### 3.4.5.2. 预期的 CBOR 到 JSON 转换器的后续编码（Expected Later Encoding for CBOR-to-JSON Converters）

<details>
  <summary>Expand original text</summary>

Tag numbers 21 to 23 indicate that a byte string might require a specific encoding when interoperating with a text-based representation. These tags are useful when an encoder knows that the byte string data it is writing is likely to be later converted to a particular JSON-based usage. That usage specifies that some strings are encoded as base64, base64url, and so on. The encoder uses byte strings instead of doing the encoding itself to reduce the message size, to reduce the code size of the encoder, or both. The encoder does not know whether or not the converter will be generic, and therefore wants to say what it believes is the proper way to convert binary strings to JSON.

The data item tagged can be a byte string or any other data item. In the latter case, the tag applies to all of the byte string data items contained in the data item, except for those contained in a nested data item tagged with an expected conversion.

These three tag numbers suggest conversions to three of the base data encodings defined in [RFC4648][RFC4648]. Tag number 21 suggests conversion to base64url encoding (Section 5 of [RFC4648][RFC4648]) where padding is not used (see Section 3.2 of [RFC4648][RFC4648]); that is, all trailing equals signs ("=") are removed from the encoded string. Tag number 22 suggests conversion to classical base64 encoding (Section 4 of [RFC4648][RFC4648]) with padding as defined in RFC 4648. For both base64url and base64, padding bits are set to zero (see Section 3.5 of [RFC4648][RFC4648]), and the conversion to alternate encoding is performed on the contents of the byte string (that is, without adding any line breaks, whitespace, or other additional characters). Tag number 23 suggests conversion to base16 (hex) encoding with uppercase alphabetics (see Section 8 of [RFC4648][RFC4648]). Note that, for all three tag numbers, the encoding of the empty byte string is the empty text string.
</details>

标签号 21 到 23 表示在与基于文本的表示进行互操作时，字节串可能需要特定的编码。在编码器知道它正在编写的字节串数据可能会在后续转换为特定基于 JSON 的用法时，这些标签很有用。该用法规定一些字符串以 base64、base64url 等进行编码。编码器使用字节串而不是自己进行编码，以减小消息大小、减小编码器的代码大小或两者兼而有之。编码器不知道转换器是否是通用的，因此想要说明将二进制字符串转换为 JSON 的正确方法。

被标记的数据项可以是字节串或任何其他数据项。在后一种情况下，标签适用于包含在数据项中的所有字节串数据项，除了那些包含在带有预期转换的嵌套数据项中的数据项。

这三个标签号分别建议转换为 [RFC4648][RFC4648] 中定义的三个基本数据编码。标签号 21 建议转换为不使用填充的 base64url 编码（[RFC4648][RFC4648] 第 5 节）（参见 [RFC4648][RFC4648] 第 3.2 节）；也就是说，从编码的字符串中删除所有尾随的等号（"="）。标签号 22 建议转换为具有 RFC 4648 中定义的填充的经典 base64 编码（[RFC4648][RFC4648] 第 4 节）。对于 base64url 和 base64，填充位设置为零（参见 [RFC4648][RFC4648] 第 3.5 节），并在字节串的内容上执行替代编码（即，不添加任何换行符、空格或其他附加字符）。标签号 23 建议转换为使用大写字母的 base16（十六进制）编码（参见 [RFC4648][RFC4648] 第 8 节）。请注意，对于所有三个标签号，空字节串的编码是空文本串。

##### 3.4.5.3. 编码文本（Encoded Text）

<details>
  <summary>Expand original text</summary>

Some text strings hold data that have formats widely used on the Internet, and sometimes those formats can be validated and presented to the application in appropriate form by the decoder. There are tags for some of these formats.

* Tag number 32 is for URIs, as defined in [RFC3986][RFC3986]. If the text string doesn't match the URI-reference production, the string is invalid.
* Tag numbers 33 and 34 are for base64url- and base64-encoded text strings, respectively, as defined in [RFC4648][RFC4648]. If any of the following apply:
  - the encoded text string contains non-alphabet characters or only 1 alphabet character in the last block of 4 (where alphabet is defined by Section 5 of [RFC4648][RFC4648] for tag number 33 and Section 4 of [RFC4648][RFC4648] for tag number 34), or
  - the padding bits in a 2- or 3-character block are not 0, or
  - the base64 encoding has the wrong number of padding characters, or
  - the base64url encoding has padding characters,

  the string is invalid.
* Tag number 36 is for MIME messages (including all headers), as defined in [RFC2045][RFC2045]. A text string that isn't a valid MIME message is invalid. (For this tag, validity checking may be particularly onerous for a generic decoder and might therefore not be offered. Note that many MIME messages are general binary data and therefore cannot be represented in a text string; [IANA.cbor-tags][IANA.cbor-tags] lists a registration for tag number 257 that is similar to tag number 36 but uses a byte string as its tag content.)

Note that tag numbers 33 and 34 differ from 21 and 22 in that the data is transported in base-encoded form for the former and in raw byte string form for the latter.

[RFC7049][RFC7049] also defined a tag number 35 for regular expressions that are in Perl Compatible Regular Expressions (PCRE/PCRE2) form [PCRE][PCRE] or in JavaScript regular expression syntax [ECMA262][ECMA262]. The state of the art in these regular expression specifications has since advanced and is continually advancing, so this specification does not attempt to update the references. Instead, this tag remains available (as registered in [RFC7049][RFC7049]) for applications that specify the particular regular expression variant they use out-of-band (possibly by limiting the usage to a defined common subset of both PCRE and ECMA262). As this specification clarifies tag validity beyond [RFC7049][RFC7049], we note that due to the open way the tag was defined in [RFC7049][RFC7049], any contained string value needs to be valid at the CBOR tag level (but then may not be "expected" at the application level).
</details>

某些文本串包含了在互联网上广泛使用的格式，有时解码器可以对这些格式进行验证，并以适当的形式呈现给应用程序。这里有一些这样的格式的标签。

* 标签号 32 用于 [RFC3986][RFC3986] 中定义的 URI。如果文本串与 URI-reference 生成的匹配不符，字符串则无效。
* 标签号 33 和 34 分别用于 [RFC4648][RFC4648] 中定义的 base64url 编码和 base64 编码文本串。如果满足以下任一条件：
  - 编码的文本串包含非字母表字符，或者最后一个 4 个字符块中仅包含 1 个字母表字符（其中字母表由 [RFC4648][RFC4648] 第 5 节定义用于标签号 33，[RFC4648][RFC4648] 第 4 节定义用于标签号 34），或
  - 2 或 3 个字符块中的填充位不为 0，或
  - base64 编码具有错误数量的填充字符，或
  - base64url 编码具有填充字符，

  字符串则无效。
* 标签号 36 用于 [RFC2045][RFC2045] 中定义的 MIME 消息（包括所有头部）。一个无效的 MIME 消息文本串是无效的。（对于这个标签，通用解码器的有效性检查可能特别繁琐，因此可能不提供。请注意，许多 MIME 消息是通用二进制数据，因此不能在文本串中表示；[IANA.cbor-tags][IANA.cbor-tags] 列出了一个类似于标签号 36 的标签号 257 注册，但使用字节串作为其标签内容。）

请注意，标签号 33 和 34 与 21 和 22 的不同之处在于，前者的数据以基本编码形式传输，后者以原始字节串形式传输。

[RFC7049][RFC7049] 还为 Perl 兼容正则表达式（PCRE/PCRE2）格式 [PCRE][PCRE] 或 JavaScript 正则表达式语法 [ECMA262][ECMA262] 中的正则表达式定义了标签号 35。这些正则表达式规范的技术水平自此已经提高，并且还在不断提高，因此本规范不试图更新这些引用。相反，这个标签仍然可用（如 [RFC7049][RFC7049] 中注册的那样），用于指定它们在外带应用程序中使用的特定正则表达式变体（可能通过将使用限制为 PCRE 和 ECMA262 的定义的公共子集）。由于本规范明确了 [RFC7049][RFC7049] 之外的标签有效性，我们注意到，由于 [RFC7049][RFC7049] 中标签定义的开放方式，任何包含的字符串值都需要在 CBOR 标签级别有效（但在应用程序级别上可能是非“预期”的）。

#### 3.4.6. 自描述 CBOR（Self-Described CBOR）

<details>
  <summary>Expand original text</summary>

In many applications, it will be clear from the context that CBOR is being employed for encoding a data item. For instance, a specific protocol might specify the use of CBOR, or a media type is indicated that specifies its use. However, there may be applications where such context information is not available, such as when CBOR data is stored in a file that does not have disambiguating metadata. Here, it may help to have some distinguishing characteristics for the data itself.

Tag number 55799 is defined for this purpose, specifically for use at the start of a stored encoded CBOR data item as specified by an application. It does not impart any special semantics on the data item that it encloses; that is, the semantics of the tag content enclosed in tag number 55799 is exactly identical to the semantics of the tag content itself.

The serialization of this tag's head is 0xd9d9f7, which does not appear to be in use as a distinguishing mark for any frequently used file types. In particular, 0xd9d9f7 is not a valid start of a Unicode text in any Unicode encoding if it is followed by a valid CBOR data item.

For instance, a decoder might be able to decode both CBOR and JSON. Such a decoder would need to mechanically distinguish the two formats. An easy way for an encoder to help the decoder would be to tag the entire CBOR item with tag number 55799, the serialization of which will never be found at the beginning of a JSON text.
</details>

在许多应用程序中，从上下文中可以清楚地看出 CBOR 正在用于编码数据项。例如，特定的协议可能会指定使用 CBOR，或者指定了使用 CBOR 的媒体类型。然而，在某些情况下，可能没有这样的上下文信息，比如当 CBOR 数据存储在没有消除歧义元数据的文件中。在这里，数据本身具有一些区分特征可能会有所帮助。

标签号 55799 为此目的而定义，特别是用于存储编码的 CBOR 数据项开始处，如应用程序所指定。它对所封装的数据项没有任何特殊语义；也就是说，标签号 55799 中封装的标签内容的语义与标签内容本身的语义完全相同。

该标签头的序列化是 0xd9d9f7，它似乎没有被用作任何常用文件类型的区分标记。特别是，如果后面跟着一个有效的 CBOR 数据项，0xd9d9f7 不是任何 Unicode 编码中有效的 Unicode 文本的起始。

例如，解码器可能能够同时解码 CBOR 和 JSON。这样的解码器需要在机械上区分这两种格式。一个简单的方法是，编码器可以通过使用标签号 55799 对整个 CBOR 项进行标记来帮助解码器，而这个序列化永远不会出现在 JSON 文本的开头。

## 4. 序列化注意事项（Serialization Considerations）
### 4.1. 首选序列化（Preferred Serialization）

<details>
  <summary>Expand original text</summary>

For some values at the data model level, CBOR provides multiple serializations. For many applications, it is desirable that an encoder always chooses a preferred serialization (preferred encoding); however, the present specification does not put the burden of enforcing this preference on either the encoder or decoder.

Some constrained decoders may be limited in their ability to decode non-preferred serializations: for example, if only integers below 1_000_000_000 (one billion) are expected in an application, the decoder may leave out the code that would be needed to decode 64-bit arguments in integers. An encoder that always uses preferred serialization ("preferred encoder") interoperates with this decoder for the numbers that can occur in this application. Generally speaking, a preferred encoder is more universally interoperable (and also less wasteful) than one that, say, always uses 64-bit integers.

Similarly, a constrained encoder may be limited in the variety of representation variants it supports such that it does not emit preferred serializations ("variant encoder"). For instance, a constrained encoder could be designed to always use the 32-bit variant for an integer that it encodes even if a short representation is available (assuming that there is no application need for integers that can only be represented with the 64-bit variant). A decoder that does not rely on receiving only preferred serializations ("variation-tolerant decoder") can therefore be said to be more universally interoperable (it might very well optimize for the case of receiving preferred serializations, though). Full implementations of CBOR decoders are by definition variation tolerant; the distinction is only relevant if a constrained implementation of a CBOR decoder meets a variant encoder.

The preferred serialization always uses the shortest form of representing the argument (Section 3); it also uses the shortest floating-point encoding that preserves the value being encoded.

The preferred serialization for a floating-point value is the shortest floating-point encoding that preserves its value, e.g., 0xf94580 for the number 5.5, and 0xfa45ad9c00 for the number 5555.5. For NaN values, a shorter encoding is preferred if zero-padding the shorter significand towards the right reconstitutes the original NaN value (for many applications, the single NaN encoding 0xf97e00 will suffice).

Definite-length encoding is preferred whenever the length is known at the time the serialization of the item starts.
</details>

对于数据模型级别的某些值，CBOR 提供了多种序列化方式。对于许多应用程序来说，编码器总是选择首选序列化（首选编码）是可取的；然而，本规范不要求编码器或解码器强制执行此首选项。

一些受限制的解码器在解码非首选序列化方面可能受到限制：例如，如果在应用程序中只期望出现低于 10 亿的整数，则解码器可能会省略解码整数中 64 位参数所需的代码。对于在此应用程序中可能出现的数字，始终使用首选序列化的编码器（“首选编码器”）与此解码器进行互操作。一般来说，首选编码器比总是使用 64 位整数的编码器更具通用互操作性（同时也更节省资源）。

类似地，受限制的编码器可能在其支持的表示变体种类上受到限制，以至于它不发出首选序列化（“变体编码器”）。例如，受限编码器可以设计为始终对其编码的整数使用 32 位变体，即使有短表示可用（假设没有应用程序需要整数，这些整数只能用 64 位变体表示）。一个不依赖于只接收首选序列化的解码器（“容忍变化的解码器”）可以说具有更普遍的互操作性（尽管它很可能会优化接收首选序列化的情况）。CBOR 解码器的完整实现在定义上是容忍变化的；只有在受限制的 CBOR 解码器实现遇到变体编码器时，这种区别才具有意义。

首选序列化始终使用表示参数的最短形式（第 3 节）；它还使用保留正在编码的值的最短浮点数编码。

浮点值的首选序列化是保留其值的最短浮点数编码，例如，数字 5.5 的 0xf94580，以及数字 5555.5 的 0xfa45ad9c00。对于 NaN 值，如果将较短有效数向右填充零可以重建原始 NaN 值，则首选较短编码（对于许多应用程序，单个 NaN 编码 0xf97e00 就足够了）。

当在项的序列化开始时已知长度时，首选明确长度编码。

### 4.2. 确定性编码的 CBOR（Deterministically Encoded CBOR）

<details>
  <summary>Expand original text</summary>

Some protocols may want encoders to only emit CBOR in a particular deterministic format; those protocols might also have the decoders check that their input is in that deterministic format. Those protocols are free to define what they mean by a "deterministic format" and what encoders and decoders are expected to do. This section defines a set of restrictions that can serve as the base of such a deterministic format.
</details>

一些协议可能希望编码器仅以特定确定性格式发出 CBOR；这些协议可能还希望解码器检查其输入是否具有该确定性格式。这些协议可以自由定义它们所说的“确定性格式”以及编码器和解码器预期要做什么。本节定义了一组限制，可以作为这种确定性格式的基础。

#### 4.2.1. 核心确定性编码要求（Core Deterministic Encoding Requirements）

<details>
  <summary>Expand original text</summary>

A CBOR encoding satisfies the "core deterministic encoding requirements" if it satisfies the following restrictions:

* Preferred serialization MUST be used. In particular, this means that arguments (see Section 3) for integers, lengths in major types 2 through 5, and tags MUST be as short as possible, for instance:
  - 0 to 23 and -1 to -24 MUST be expressed in the same byte as the major type;
  - 24 to 255 and -25 to -256 MUST be expressed only with an additional uint8_t;
  - 256 to 65535 and -257 to -65536 MUST be expressed only with an additional uint16_t;
  - 65536 to 4294967295 and -65537 to -4294967296 MUST be expressed only with an additional uint32_t.

  Floating-point values also MUST use the shortest form that preserves the value, e.g., 1.5 is encoded as 0xf93e00 (binary16) and 1000000.5 as 0xfa49742408 (binary32). (One implementation of this is to have all floats start as a 64-bit float, then do a test conversion to a 32-bit float; if the result is the same numeric value, use the shorter form and repeat the process with a test conversion to a 16-bit float. This also works to select 16-bit float for positive and negative Infinity as well.)
* Indefinite-length items MUST NOT appear. They can be encoded as definite-length items instead.
* The keys in every map MUST be sorted in the bytewise lexicographic order of their deterministic encodings. For example, the following keys are sorted correctly:
  ```
  1. 10, encoded as 0x0a.
  2. 100, encoded as 0x1864.
  3. -1, encoded as 0x20.
  4. "z", encoded as 0x617a.
  5. "aa", encoded as 0x626161.
  6. [100], encoded as 0x811864.
  7. [-1], encoded as 0x8120.
  8. false, encoded as 0xf4.
  ```

Implementation note: the self-delimiting nature of the CBOR encoding means that there are no two well-formed CBOR encoded data items where one is a prefix of the other. The bytewise lexicographic comparison of deterministic encodings of different map keys therefore always ends in a position where the byte differs between the keys, before the end of a key is reached.
</details>

如果 CBOR 编码满足以下限制，它就满足“核心确定性编码要求”：
* 必须（MUST）使用首选序列化。特别是，这意味着整数、主类型 2 到 5 的长度以及标签的参数（参见第 3 节）必须（MUST）尽可能短，例如：
  - 0 到 23 和 -1 到 -24 必须（MUST）在与主类型相同的字节中表示；
  - 24 到 255 和 -25 到 -256 必须（MUST）仅用额外的 uint8_t 表示；
  - 256 到 65535 和 -257 到 -65536 必须（MUST）仅用额外的 uint16_t 表示；
  - 65536 到 4294967295 和 -65537 到 -4294967296 必须（MUST）仅用额外的 uint32_t 表示。

  浮点值还必须（MUST）使用保留值的最短形式，例如，1.5 编码为 0xf93e00（binary16）和 1000000.5 编码为 0xfa49742408（binary32）。（实现这一点的一种方法是将所有浮点数作为 64 位浮点数开始，然后将其测试转换为 32 位浮点数；如果结果是相同的数值，则使用较短的形式并将过程重复为 16 位浮点数的测试转换。这对于选择正无穷大和负无穷大的 16 位浮点数也是有效的。）
* 不得（MUST NOT）出现不定长度项。它们可以编码为定长项。
* 每个映射中的键必须（MUST）按照其确定性编码的逐字节字典顺序（bytewise lexicographic order）排序。例如，以下键正确排序：
  ```
  1. 10，编码为 0x0a
  2. 100，编码为 0x1864
  3. -1，编码为 0x20
  4. "z"，编码为 0x617a
  5. "aa"，编码为 0x626161
  6. [100]，编码为 0x811864
  7. [-1]，编码为 0x8120
  8. false，编码为 0xf4
  ```

实现注意事项：CBOR 编码的自定界性质意味着没有两个格式正确的 CBOR 编码数据项，其中一个是另一个的前缀。因此，在不同映射键的确定性编码的逐字节字典顺序比较总是在键之间的字节不同的位置结束，然后到达键的末尾。

#### 4.2.2. 其他确定性编码注意事项（Additional Deterministic Encoding Considerations）

<details>
  <summary>Expand original text</summary>

CBOR tags present additional considerations for deterministic encoding. If a CBOR-based protocol were to provide the same semantics for the presence and absence of a specific tag (e.g., by allowing both tag 1 data items and raw numbers in a date/time position, treating the latter as if they were tagged), the deterministic format would not allow the presence of the tag, based on the "shortest form" principle. For example, a protocol might give encoders the choice of representing a URL as either a text string or, using Section 3.4.5.3, tag number 32 containing a text string. This protocol's deterministic encoding needs either to require that the tag is present or to require that it is absent, not allow either one.

In a protocol that does require tags in certain places to obtain specific semantics, the tag needs to appear in the deterministic format as well. Deterministic encoding considerations also apply to the content of tags.

If a protocol includes a field that can express integers with an absolute value of 264 or larger using tag numbers 2 or 3 (Section 3.4.3), the protocol's deterministic encoding needs to specify whether smaller integers are also expressed using these tags or using major types 0 and 1. Preferred serialization uses the latter choice, which is therefore recommended.

Protocols that include floating-point values, whether represented using basic floating-point values (Section 3.3) or using tags (or both), may need to define extra requirements on their deterministic encodings, such as:
* Although IEEE floating-point values can represent both positive and negative zero as distinct values, the application might not distinguish these and might decide to represent all zero values with a positive sign, disallowing negative zero. (The application may also want to restrict the precision of floating-point values in such a way that there is never a need to represent 64-bit -- or even 32-bit -- floating-point values.)
* If a protocol includes a field that can express floating-point values, with a specific data model that declares integer and floating-point values to be interchangeable, the protocol's deterministic encoding needs to specify whether, for example, the integer 1.0 is encoded as 0x01 (unsigned integer), 0xf93c00 (binary16), 0xfa3f800000 (binary32), or 0xfb3ff0000000000000 (binary64). Example rules for this are:
  1. Encode integral values that fit in 64 bits as values from major types 0 and 1, and other values as the preferred (smallest of 16-, 32-, or 64-bit) floating-point representation that accurately represents the value,
  2. Encode all values as the preferred floating-point representation that accurately represents the value, even for integral values, or
  3. Encode all values as 64-bit floating-point representations.

  Rule 1 straddles the boundaries between integers and floating-point values, and Rule 3 does not use preferred serialization, so Rule 2 may be a good choice in many cases.
* If NaN is an allowed value, and there is no intent to support NaN payloads or signaling NaNs, the protocol needs to pick a single representation, typically 0xf97e00. If that simple choice is not possible, specific attention will be needed for NaN handling.
* Subnormal numbers (nonzero numbers with the lowest possible exponent of a given IEEE 754 number format) may be flushed to zero outputs or be treated as zero inputs in some floating-point implementations. A protocol's deterministic encoding may want to specifically accommodate such implementations while creating an onus on other implementations by excluding subnormal numbers from interchange, interchanging zero instead.
* The same number can be represented by different decimal fractions, by different bigfloats, and by different forms under other tags that may be defined to express numeric values. Depending on the implementation, it may not always be practical to determine whether any of these forms (or forms in the basic generic data model) are equivalent. An application protocol that presents choices of this kind for the representation format of numbers needs to be explicit about how the formats for deterministic encoding are to be chosen.

</details>

CBOR 标签对于确定性编码提出了额外的考虑。如果一个基于 CBOR 的协议为特定标签的存在和不存在提供相同的语义（例如，允许标签 1 数据项和原始数字出现在日期/时间位置，将后者视为带标签的数据），基于“最短形式”原则，确定性格式将不允许标签出现。例如，协议可能允许编码器选择将 URL 表示为文本串或者使用第 3.4.5.3 节，标签号 32 包含文本串。这个协议的确定性编码需要要么要求标签存在，要么要求它不存在，而不是允许两者都存在。

在某个协议中确实需要在某些位置使用标签以获得特定语义时，确定性格式中也需要出现标签。确定性编码的注意事项也适用于标签的内容。

如果协议包含一个字段，可以使用标签号 2 或 3（第 3.4.3 节）表示绝对值为 2^64 或更大的整数，那么协议的确定性编码需要指定是否使用这些标签还是使用主类型 0 和 1 表示较小的整数。首选序列化使用后者选择，因此建议使用。

包含浮点值的协议（无论是使用基本浮点值（第 3.3 节）还是使用标签（或两者都使用）表示），可能需要为其确定性编码定义额外要求，例如：
* 尽管 IEEE 浮点值可以将正零和负零表示为不同的值，但应用程序可能无法区分它们，可能决定用正号表示所有零值，不允许负零。（应用程序还可能希望限制浮点值的精度，以便永远不需要表示 64 位（甚至 32 位）的浮点值。）
* 如果协议包含一个可以表示浮点值的字段，并且具有特定的数据模型，该数据模型声明整数和浮点值是可互换的，那么协议的确定性编码需要指定，例如，整数 1.0 是编码为 0x01（非负整数）、0xf93c00（binary16）、0xfa3f800000（binary32）还是 0xfb3ff0000000000000（binary64）。这方面的示例规则有：
  1. 将适用于 64 位的整数值编码为主类型 0 和 1 的值，将其他值编码为准确表示该值的首选（16、32 或 64 位中最小的）浮点表示；
  2. 将所有值编码为准确表示该值的首选浮点表示，即使对于整数值也是如此；
  3. 将所有值编码为 64 位浮点表示。

  规则 1 在整数和浮点值之间跨越边界，规则 3 不使用首选序列化，因此在许多情况下，规则 2 可能是一个不错的选择。
* 如果 NaN 是允许的值，并且无意支持 NaN 载荷或信号 NaN，协议需要选择一个单一表示，通常是 0xf97e00。如果无法做出简单的选择，需要特别注意 NaN 的处理。
* 在某些浮点实现中，次正规数（具有给定 IEEE 754 数字格式的最低可能指数的非零数）可能被刷新为零输出或被视为零输入。协议的确定性编码可能希望特别适应这样的实现，同时通过从交换中排除次正规数，交换零值来为其他实现创造负担。
* 相同的数字可以用不同的小数、不同的大浮点数表示，也可以用其他可能被定义为表示数值的标签下的不同形式表示。根据实现情况，确定这些形式（或基本通用数据模型中的形式）是否等价可能并不总是实际可行的。在数字表示格式方面提供选择的应用程序协议需要明确说明如何选择确定性编码的格式。

#### 4.2.3. 长度优先的映射键排序（Length-First Map Key Ordering）

<details>
  <summary>Expand original text</summary>

The core deterministic encoding requirements (Section 4.2.1) sort map keys in a different order from the one suggested by Section 3.9 of [RFC7049][RFC7049] (called "Canonical CBOR" there). Protocols that need to be compatible with the order specified in [RFC7049][RFC7049] can instead be specified in terms of this specification's "length-first core deterministic encoding requirements":

A CBOR encoding satisfies the "length-first core deterministic encoding requirements" if it satisfies the core deterministic encoding requirements except that the keys in every map MUST be sorted such that:

1. If two keys have different lengths, the shorter one sorts earlier;
2. If two keys have the same length, the one with the lower value in (bytewise) lexical order sorts earlier.

For example, under the length-first core deterministic encoding requirements, the following keys are sorted correctly:

1. 10, encoded as 0x0a.
2. -1, encoded as 0x20.
3. false, encoded as 0xf4.
4. 100, encoded as 0x1864.
5. "z", encoded as 0x617a.
6. [-1], encoded as 0x8120.
7. "aa", encoded as 0x626161.
8. [100], encoded as 0x811864.

Although [RFC7049][RFC7049] used the term "Canonical CBOR" for its form of requirements on deterministic encoding, this document avoids this term because "canonicalization" is often associated with specific uses of deterministic encoding only. The terms are essentially interchangeable, however, and the set of core requirements in this document could also be called "Canonical CBOR", while the length-first-ordered version of that could be called "Old Canonical CBOR".
</details>

核心确定性编码要求（第 4.2.1 节）将映射键按照与 [RFC7049][RFC7049] 第 3.9 节建议的顺序不同的顺序排序（在那里称为“规范化 CBOR”）。需要与 [RFC7049][RFC7049] 中指定的顺序兼容的协议可以用这个规范的“长度优先核心确定性编码要求”来替代指定：

一个 CBOR 编码满足“长度优先核心确定性编码要求”，如果它满足核心确定性编码要求，除了每个映射中的键必须（MUST）排序如下：

1. 如果两个键的长度不同，较短的键排在前面；
2. 如果两个键长度相同，按字节顺序排序值较小的键排在前面。

例如，在长度优先核心确定性编码要求下，以下键正确地排序：

1. 10，编码为 0x0a.
2. -1，编码为 0x20.
3. false，编码为 0xf4.
4. 100，编码为 0x1864.
5. "z"，编码为 0x617a.
6. [-1]，编码为 0x8120.
7. "aa"，编码为 0x626161.
8. [100]，编码为 0x811864.

尽管 [RFC7049][RFC7049] 为其确定性编码要求使用了“规范化 CBOR”这个术语，但本文档避免使用这个术语，因为“规范化”通常只与确定性编码的特定用途相关。然而，这些术语在本质上是可以互换的，本文档中的核心要求集合也可以称为“规范化 CBOR”，而这个版本的长度优先排序可以称为“旧规范化 CBOR”。

## 5. 创建基于 CBOR 的协议（Creating CBOR-Based Protocols）

<details>
  <summary>Expand original text</summary>

Data formats such as CBOR are often used in environments where there is no format negotiation. A specific design goal of CBOR is to not need any included or assumed schema: a decoder can take a CBOR item and decode it with no other knowledge.

Of course, in real-world implementations, the encoder and the decoder will have a shared view of what should be in a CBOR data item. For example, an agreed-to format might be "the item is an array whose first value is a UTF-8 string, second value is an integer, and subsequent values are zero or more floating-point numbers" or "the item is a map that has byte strings for keys and contains a pair whose key is 0xab01".

CBOR-based protocols MUST specify how their decoders handle invalid and other unexpected data. CBOR-based protocols MAY specify that they treat arbitrary valid data as unexpected. Encoders for CBOR-based protocols MUST produce only valid items, that is, the protocol cannot be designed to make use of invalid items. An encoder can be capable of encoding as many or as few types of values as is required by the protocol in which it is used; a decoder can be capable of understanding as many or as few types of values as is required by the protocols in which it is used. This lack of restrictions allows CBOR to be used in extremely constrained environments.

The rest of this section discusses some considerations in creating CBOR-based protocols. With few exceptions, it is advisory only and explicitly excludes any language from BCP 14 [RFC2119][RFC2119] [RFC8174][RFC8174] other than words that could be interpreted as "MAY" in the sense of BCP 14. The exceptions aim at facilitating interoperability of CBOR-based protocols while making use of a wide variety of both generic and application-specific encoders and decoders.
</details>

诸如 CBOR 这样的数据格式通常用于没有格式协商的环境。CBOR 的一个具体设计目标是不需要任何包含的或假定的模式：解码器可以接受一个 CBOR 项并在没有其他知识的情况下对其进行解码。

当然，在现实世界的实现中，编码器和解码器会对 CBOR 数据项中应该包含什么有共同的认识。例如，一个约定的格式可能是“数据项是一个数组，其第一个值是一个 UTF-8 字符串，第二个值是一个整数，随后的值是零个或多个浮点数”，或者“数据项是一个具有字节串键的映射，包含一个键为 0xab01 的键值对”。

基于 CBOR 的协议必须（MUST）指定其解码器如何处理无效和其他意外数据。基于 CBOR 的协议可以（MAY）指定将任意有效数据视为意外数据。CBOR 协议的编码器必须（MUST）只产生有效项，也就是说，协议不能设计为利用无效项。编码器可以根据所使用的协议的要求，编码尽可能多或尽可能少的类型值；解码器可以根据所使用的协议的要求，理解尽可能多或尽可能少的类型值。这种缺乏限制使得 CBOR 可以在极为受限的环境中使用。

本节其余部分讨论了创建基于 CBOR 的协议的一些注意事项。除了少数例外，本节主要是建议性质的，明确排除了 BCP 14 [RFC2119][RFC2119] [RFC8174][RFC8174] 中的任何语言，除了可以理解为 BCP 14 中“MAY”意义的词语。这些例外旨在促进基于 CBOR 的协议的互操作性，同时利用各种通用和特定于应用的编码器和解码器。

### 5.1. CBOR 在流式应用中的应用（CBOR in Streaming Applications）

<details>
  <summary>Expand original text</summary>

In a streaming application, a data stream may be composed of a sequence of CBOR data items concatenated back-to-back. In such an environment, the decoder immediately begins decoding a new data item if data is found after the end of a previous data item.

Not all of the bytes making up a data item may be immediately available to the decoder; some decoders will buffer additional data until a complete data item can be presented to the application. Other decoders can present partial information about a top-level data item to an application, such as the nested data items that could already be decoded, or even parts of a byte string that hasn't completely arrived yet. Such an application also MUST have a matching streaming security mechanism, where the desired protection is available for incremental data presented to the application.

Note that some applications and protocols will not want to use indefinite-length encoding. Using indefinite-length encoding allows an encoder to not need to marshal all the data for counting, but it requires a decoder to allocate increasing amounts of memory while waiting for the end of the item. This might be fine for some applications but not others.
</details>

在流式应用中，数据流可能由一系列首尾相接的 CBOR 数据项组成。在这样的环境中，如果在前一个数据项结束后发现有数据，解码器将立即开始解码新的数据项。

构成数据项的所有字节可能不会立即提供给解码器；一些解码器将缓冲额外的数据，直到可以向应用程序提供完整的数据项。其他解码器可以向应用程序呈现关于顶层数据项的部分信息，例如已经解码的嵌套数据项，甚至尚未完全到达的字节串的部分内容。这样的应用程序还必须（MUST）具有匹配的流式安全机制，以便为应用程序呈现的增量数据提供所需的保护。

请注意，某些应用程序和协议可能不希望使用不定长度编码。使用不定长度编码允许编码器无需整理所有数据以进行计数，但需要解码器在等待项结束时分配越来越多的内存。这对某些应用程序可能是可行的，但对其他应用程序可能不适用。

### 5.2. 通用编码器和解码器（Generic Encoders and Decoders）

<details>
  <summary>Expand original text</summary>

A generic CBOR decoder can decode all well-formed encoded CBOR data items and present the data items to an application. See Appendix C. (The diagnostic notation, Section 8, may be used to present well-formed CBOR values to humans.)

Generic CBOR encoders provide an application interface that allows the application to specify any well-formed value to be encoded as a CBOR data item, including simple values and tags unknown to the encoder.

Even though CBOR attempts to minimize these cases, not all well-formed CBOR data is valid: for example, the encoded text string 0x62c0ae does not contain valid UTF-8 (because [RFC3629][RFC3629] requires always using the shortest form) and so is not a valid CBOR item. Also, specific tags may make semantic constraints that may be violated, for instance, by a bignum tag enclosing another tag or by an instance of tag number 0 containing a byte string or containing a text string with contents that do not match the date-time production of [RFC3339][RFC3339]. There is no requirement that generic encoders and decoders make unnatural choices for their application interface to enable the processing of invalid data. Generic encoders and decoders are expected to forward simple values and tags even if their specific codepoints are not registered at the time the encoder/decoder is written (Section 5.4).
</details>

通用 CBOR 解码器可以解码所有格式良好的编码 CBOR 数据项，并将数据项呈现给应用程序。请参阅附录 C。（诊断符号表示法（diagnostic notation，第 8 节）可用于将格式良好的 CBOR 值呈现给人类。）

通用 CBOR 编码器提供了一个应用程序接口，允许应用程序指定任何格式良好的值作为 CBOR 数据项进行编码，包括编码器未知的简单值和标签。

尽管 CBOR 努力将这些情况降至最低，但并非所有格式良好的 CBOR 数据都是有效的：例如，编码文本串 0x62c0ae 不包含有效的 UTF-8（因为 [RFC3629][RFC3629] 要求始终使用最短格式），因此不是有效的 CBOR 项。此外，特定标签可能会对可能被违反的语义约束进行限制，例如，由大数标签封装另一个标签，或者由标签编号为 0 的实例包含一个字节串，或者包含一个文本串，其内容不符合 [RFC3339][RFC3339] 的日期时间产生。通用编码器和解码器无需为其应用程序接口做不自然的选择以处理无效数据。通用编码器和解码器预计会转发简单值和标签，即使在编码器/解码器编写时它们的特定代码点尚未注册（第 5.4 节）。

### 5.3. 数据项有效性（Validity of Items）

<details>
  <summary>Expand original text</summary>

A well-formed but invalid CBOR data item (Section 1.2) presents a problem with interpreting the data encoded in it in the CBOR data model. A CBOR-based protocol could be specified in several layers, in which the lower layers don't process the semantics of some of the CBOR data they forward. These layers can't notice any validity errors in data they don't process and MUST forward that data as-is. The first layer that does process the semantics of an invalid CBOR item MUST pick one of two choices:

1. Replace the problematic item with an error marker and continue with the next item, or
2. Issue an error and stop processing altogether.

A CBOR-based protocol MUST specify which of these options its decoders take for each kind of invalid item they might encounter.

Such problems might occur at the basic validity level of CBOR or in the context of tags (tag validity).
</details>

格式良好但无效的 CBOR 数据项（第 1.2 节）在 CBOR 数据模型中解释其中编码的数据时存在问题。基于 CBOR 的协议可以分为多个层次，其中较低层次不处理它们转发的某些 CBOR 数据的语义。这些层次无法注意到它们未处理的数据中的任何有效性错误，并且必须（MUST）原样转发该数据。处理无效 CBOR 项语义的第一层必须（MUST）选择以下两个选项之一：

1. 用错误标记替换有问题的数据项并继续下一个数据项，或者
2. 发出错误并停止所有处理。

基于 CBOR 的协议必须（MUST）为解码器在可能遇到的每种无效项上指定采用这些选项中的哪一个。

此类问题可能发生在 CBOR 的基本有效性级别或标签上下文中（标签有效性）。

#### 5.3.1. 基本有效性（Basic validity）

<details>
  <summary>Expand original text</summary>

Two kinds of validity errors can occur in the basic generic data model:

**Duplicate keys in a map**:
Generic decoders (Section 5.2) make data available to applications using the native CBOR data model. That data model includes maps (key-value mappings with unique keys), not multimaps (key-value mappings where multiple entries can have the same key). Thus, a generic decoder that gets a CBOR map item that has duplicate keys will decode to a map with only one instance of that key, or it might stop processing altogether. On the other hand, a "streaming decoder" may not even be able to notice. See Section 5.6 for more discussion of keys in maps.

**Invalid UTF-8 string**:
A decoder might or might not want to verify that the sequence of bytes in a UTF-8 string (major type 3) is actually valid UTF-8 and react appropriately.
</details>

在基本通用数据模型中可以发生两种有效性错误：

**映射中的重复键**：
通用解码器（第 5.2 节）使用本机 CBOR 数据模型向应用程序提供数据。该数据模型包括映射（具有唯一键的键值映射），而不是多映射（可以具有相同键的多个条目的键值映射）。因此，获得具有重复键的 CBOR 映射项的通用解码器将解码为仅具有该键的一个实例的映射，或者可能会完全停止处理。另一方面，“流式解码器”甚至可能无法注意到。有关映射中键的更多讨论，请参阅第 5.6 节。

**无效的 UTF-8 字符串**：
解码器可能希望也可能不希望验证 UTF-8 字符串（主类型 3）中字节序列实际上是否为有效的 UTF-8，并作出相应的反应。

#### 5.3.2. 标签有效性（Tag validity）

<details>
  <summary>Expand original text</summary>

Two additional kinds of validity errors are introduced by adding tags to the basic generic data model:

**Inadmissible type for tag content**:
Tag numbers (Section 3.4) specify what type of data item is supposed to be used as their tag content; for example, the tag numbers for unsigned or negative bignums are supposed to be put on byte strings. A decoder that decodes the tagged data item into a native representation (a native big integer in this example) is expected to check the type of the data item being tagged. Even decoders that don't have such native representations available in their environment may perform the check on those tags known to them and react appropriately.

**Inadmissible value for tag content**:
The type of data item may be admissible for a tag's content, but the specific value may not be; e.g., a value of "yesterday" is not acceptable for the content of tag 0, even though it properly is a text string. A decoder that normally ingests such tags into equivalent platform types might present this tag to the application in a similar way to how it would present a tag with an unknown tag number (Section 5.4).
</details>

通过将标签添加到基本通用数据模型，引入了另外两种有效性错误：

**对于标签内容不允许的类型**：
标签号（第 3.4 节）指定了应将哪种类型的数据项用作其标签内容；例如，应将无符号或负数大整数的标签号放在字节串上。解码器在将带标签的数据项解码为本地表示（本示例中的本地大整数）时，预期会检查被标签的数据项的类型。即使在环境中没有这样的本地表示的解码器，也可以对已知的那些标签执行检查并做出适当的反应。

**对于标签内容不允许的值**：
数据项的类型可能对标签的内容是可接受的，但特定值可能不是；例如，对于标签 0 的内容，"昨天" 的值是不可接受的，即使它确实是一个文本串。通常将此类标签转换为等效平台类型的解码器可能以类似于如何呈现具有未知标签号的标签的方式将此标签呈现给应用程序（第 5.4 节）。

### 5.4. 有效性和演变（Validity and Evolution）

<details>
  <summary>Expand original text</summary>

A decoder with validity checking will expend the effort to reliably detect data items with validity errors. For example, such a decoder needs to have an API that reports an error (and does not return data) for a CBOR data item that contains any of the validity errors listed in the previous subsection.

The set of tags defined in the "Concise Binary Object Representation (CBOR) Tags" registry (Section 9.2), as well as the set of simple values defined in the "Concise Binary Object Representation (CBOR) Simple Values" registry (Section 9.1), can grow at any time beyond the set understood by a generic decoder. A validity-checking decoder can do one of two things when it encounters such a case that it does not recognize:

* It can report an error (and not return data). Note that treating this case as an error can cause ossification and is thus not encouraged. This error is not a validity error, per se. This kind of error is more likely to be raised by a decoder that would be performing validity checking if this were a known case.
* It can emit the unknown item (type, value, and, for tags, the decoded tagged data item) to the application calling the decoder, and then give the application an indication that the decoder did not recognize that tag number or simple value.

The latter approach, which is also appropriate for decoders that do not support validity checking, provides forward compatibility with newly registered tags and simple values without the requirement to update the encoder at the same time as the calling application. (For this, the decoder's API needs the ability to mark unknown items so that the calling application can handle them in a manner appropriate for the program.)

Since some of the processing needed for validity checking may have an appreciable cost (in particular with duplicate detection for maps), support of validity checking is not a requirement placed on all CBOR decoders.

Some encoders will rely on their applications to provide input data in such a way that valid CBOR results from the encoder. A generic encoder may also want to provide a validity-checking mode where it reliably limits its output to valid CBOR, independent of whether or not its application is indeed providing API-conformant data.
</details>

具有有效性检查的解码器将付出努力可靠地检测具有有效性错误的数据项。例如，这样的解码器需要具有一个 API，该 API 对于包含前一子段中列出的任何有效性错误的 CBOR 数据项报告错误（并且不返回数据）。

在“简明二进制对象表示（CBOR）标签”注册表（第 9.2 节）中定义的标签集合，以及在“简明二进制对象表示（CBOR）简单值”注册表（第 9.1 节）中定义的简单值集合，可能随时超出通用解码器所理解的集合。在遇到这样一个它无法识别的情况时，具有有效性检查的解码器可以做以下两件事之一：

* 它可以报告错误（并且不返回数据）。请注意，将此情况视为错误可能会导致僵化，因此不建议这样做。严格来说，这个错误不是有效性错误。这种错误更有可能由在这是已知情况的情况下执行有效性检查的解码器引发。
* 它可以将未知数据项（类型、值和对于标签，解码的带标签数据项）发送给调用解码器的应用程序，然后向应用程序提示解码器没有识别到该标签号或简单值。

后一种方法，也适用于不支持有效性检查的解码器，为新注册的标签和简单值提供向前兼容性，无需在调用应用程序同时更新编码器。（为此，解码器的 API 需要标记未知数据项的能力，以便调用应用程序可以以适合程序的方式处理它们。）

由于有效性检查所需的某些处理可能具有可观的成本（尤其是对于映射的重复检测），因此有效性检查的支持并不是对所有 CBOR 解码器的要求。

一些编码器将依赖其应用程序以这样一种方式提供输入数据，以使编码器产生有效的 CBOR。通用编码器还可能希望提供一个有效性检查模式，其中它可靠地将其输出限制为有效的 CBOR，无论其应用程序是否确实提供符合 API 的数据。

### 5.5. 数字（Numbers）

<details>
  <summary>Expand original text</summary>

CBOR-based protocols should take into account that different language environments pose different restrictions on the range and precision of numbers that are representable. For example, the basic JavaScript number system treats all numbers as floating-point values, which may result in the silent loss of precision in decoding integers with more than 53 significant bits. Another example is that, since CBOR keeps the sign bit for its integer representation in the major type, it has one bit more for signed numbers of a certain length (e.g., -2^64..2^64-1 for 1+8-byte integers) than the typical platform signed integer representation of the same length (-2^63..2^63-1 for 8-byte int64_t). A protocol that uses numbers should define its expectations on the handling of nontrivial numbers in decoders and receiving applications.

A CBOR-based protocol that includes floating-point numbers can restrict which of the three formats (half-precision, single-precision, and double-precision) are to be supported. For an integer-only application, a protocol may want to completely exclude the use of floating-point values.

A CBOR-based protocol designed for compactness may want to exclude specific integer encodings that are longer than necessary for the application, such as to save the need to implement 64-bit integers. There is an expectation that encoders will use the most compact integer representation that can represent a given value. However, a compact application that does not require deterministic encoding should accept values that use a longer-than-needed encoding (such as encoding "0" as 0b000_11001 followed by two bytes of 0x00) as long as the application can decode an integer of the given size. Similar considerations apply to floating-point values; decoding both preferred serializations and longer-than-needed ones is recommended.

CBOR-based protocols for constrained applications that provide a choice between representing a specific number as an integer and as a decimal fraction or bigfloat (such as when the exponent is small and nonnegative) might express a quality-of-implementation expectation that the integer representation is used directly.
</details>

基于 CBOR 的协议应考虑到不同的语言环境对可表示数字的范围和精度施加了不同的限制。例如，基本的 JavaScript 数字系统将所有数字视为浮点值，这可能导致在解码具有超过 53 个有效位的整数时静默地失去精度。另一个例子是，由于 CBOR 为其整数表示保留了主要类型中的符号位，它对于某个长度的有符号数（例如，1+8 字节整数的 -2^64..2^64-1）比相同长度的典型平台有符号整数表示（8 字节 int64_t 的 -2^63..2^63-1）多一个位。使用数字的协议应该定义其对解码器和接收应用程序中处理非平凡数字的期望。

包含浮点数的基于 CBOR 的协议可以限制支持哪种格式（半精度、单精度和双精度）。对于仅整数应用程序，协议可能希望完全排除使用浮点值。

设计用于紧凑性的基于 CBOR 的协议可能希望排除对应用程序来说过长的特定整数编码，例如为了节省实现 64 位整数的需求。有一种期望，编码器将使用最紧凑的整数表示来表示给定的值。然而，一个紧凑的应用程序，如果不需要确定性编码，应该接受使用比所需更长的编码的值（例如，将“0”编码为 0b000_11001，后面跟两个字节的 0x00），只要应用程序能够解码给定大小的整数。类似的考虑适用于浮点值；建议解码首选序列化和比所需更长的序列化。

为受限应用程序提供在将特定数字表示为整数和小数或大浮点数（例如，当指数很小且为非负数时）之间进行选择的基于 CBOR 的协议可能表达了一种实现质量的期望，即直接使用整数表示。

### 5.6. 为映射指定键（Specifying Keys for Maps）

<details>
  <summary>Expand original text</summary>

The encoding and decoding applications need to agree on what types of keys are going to be used in maps. In applications that need to interwork with JSON-based applications, conversion is simplified by limiting keys to text strings only; otherwise, there has to be a specified mapping from the other CBOR types to text strings, and this often leads to implementation errors. In applications where keys are numeric in nature, and numeric ordering of keys is important to the application, directly using the numbers for the keys is useful.

If multiple types of keys are to be used, consideration should be given to how these types would be represented in the specific programming environments that are to be used. For example, in JavaScript Maps [ECMA262][ECMA262], a key of integer 1 cannot be distinguished from a key of floating-point 1.0. This means that, if integer keys are used, the protocol needs to avoid the use of floating-point keys the values of which happen to be integer numbers in the same map.

Decoders that deliver data items nested within a CBOR data item immediately on decoding them ("streaming decoders") often do not keep the state that is necessary to ascertain uniqueness of a key in a map. Similarly, an encoder that can start encoding data items before the enclosing data item is completely available ("streaming encoder") may want to reduce its overhead significantly by relying on its data source to maintain uniqueness.

A CBOR-based protocol MUST define what to do when a receiving application sees multiple identical keys in a map. The resulting rule in the protocol MUST respect the CBOR data model: it cannot prescribe a specific handling of the entries with the identical keys, except that it might have a rule that having identical keys in a map indicates a malformed map and that the decoder has to stop with an error. When processing maps that exhibit entries with duplicate keys, a generic decoder might do one of the following:

* Not accept maps with duplicate keys (that is, enforce validity for maps, see also Section 5.4). These generic decoders are universally useful. An application may still need to perform its own duplicate checking based on application rules (for instance, if the application equates integers and floating-point values in map key positions for specific maps).
* Pass all map entries to the application, including ones with duplicate keys. This requires that the application handle (check against) duplicate keys, even if the application rules are identical to the generic data model rules.
* Lose some entries with duplicate keys, e.g., deliver only the final (or first) entry out of the entries with the same key. With such a generic decoder, applications may get different results for a specific key on different runs, and with different generic decoders, which value is returned is based on generic decoder implementation and the actual order of keys in the map. In particular, applications cannot validate key uniqueness on their own as they do not necessarily see all entries; they may not be able to use such a generic decoder if they need to validate key uniqueness. These generic decoders can only be used in situations where the data source and transfer always provide valid maps; this is not possible if the data source and transfer can be attacked.

Generic decoders need to document which of these three approaches they implement.

The CBOR data model for maps does not allow ascribing semantics to the order of the key/value pairs in the map representation. Thus, a CBOR-based protocol MUST NOT specify that changing the key/value pair order in a map changes the semantics, except to specify that some orders are disallowed, for example, where they would not meet the requirements of a deterministic encoding (Section 4.2). (Any secondary effects of map ordering such as on timing, cache usage, and other potential side channels are not considered part of the semantics but may be enough reason on their own for a protocol to require a deterministic encoding format.)

Applications for constrained devices should consider using small integers as keys if they have maps with a small number of frequently used keys; for instance, a set of 24 or fewer keys can be encoded in a single byte as unsigned integers, up to 48 if negative integers are also used. Less frequently occurring keys can then use integers with longer encodings.
</details>

编码和解码应用程序需要就映射中要使用的键的类型达成一致。在需要与基于 JSON 的应用程序互操作的应用程序中，仅将键限制为文本串可简化转换；否则，必须有一个从其他 CBOR 类型到文本串的指定映射，这通常会导致实现错误。在键本质上是数字的应用程序中，并且键的数字顺序对应用程序很重要时，直接使用数字作为键是有用的。

如果要使用多种类型的键，应考虑在要使用的特定编程环境中如何表示这些类型。例如，在 JavaScript Maps [ECMA262][ECMA262] 中，整数 1 的键无法与浮点数 1.0 的键区分开。这意味着，如果使用整数键，协议需要避免在同一个映射中使用浮点键，而这些浮点键的值碰巧是整数。

在解码它们时立即传递嵌套在 CBOR 数据项内部的数据项的解码器（"流式解码器"）通常无法保持确定映射中键的唯一性所需的状态。类似地，一个可以在封装数据项完全可用之前开始编码数据项的编码器（"流式编码器"）可能希望通过依赖其数据源来维护唯一性，从而显著降低其开销。

一个基于 CBOR 的协议必须（MUST）定义在接收应用程序在映射中看到多个相同键时该如何处理。协议中的结果规则必须（MUST）遵守 CBOR 数据模型：除了可能有一个规则，即映射中具有相同键表示映射格式错误并且解码器必须停止并报错外，它不能规定对具有相同键的条目的特定处理。在处理具有重复键的条目的映射时，通用解码器可能会执行以下操作之一：

* 不接受具有重复键的映射（即，对映射执行有效性检查，参见第 5.4 节）。这些通用解码器具有普遍适用性。应用程序可能仍需要根据应用程序规则执行自己的重复检查（例如，如果应用程序将特定映射中的整数和浮点值视为相等的映射键位置）。
* 将所有映射条目传递给应用程序，包括具有重复键的条目。这要求应用程序处理（检查）重复键，即使应用程序规则与通用数据模型规则相同。
* 丢失一些具有重复键的条目，例如，只传递具有相同键的条目中的最后一个（或第一个）条目。对于这样的通用解码器，应用程序可能在不同的运行和不同的通用解码器上针对特定键获得不同的结果，哪个值被返回取决于通用解码器的实现和映射中键的实际顺序。特别是，应用程序无法验证键的唯一性，因为它们不一定能看到所有条目；如果它们需要验证键的唯一性，可能无法使用这种通用解码器。这些通用解码器只能在数据源和传输始终提供有效映射的情况下使用；如果数据源和传输可能受到攻击，这是不可能的。

通用解码器需要记录它们实现了这三种方法中的哪一种。

CBOR 数据模型对于映射不允许赋予映射表示中键/值对顺序的语义。因此，一个基于 CBOR 的协议不能（MUST NOT）规定更改映射中的键/值对顺序会改变语义，除非指定某些顺序是不允许的，例如，它们不符合确定性编码（第 4.2 节）的要求。（映射排序的任何次要影响，如对计时、缓存使用和其他潜在的侧信道的影响，都不视为语义的一部分，但可能是协议要求确定性编码格式的充分理由。）

面向受限设备的应用程序在具有少量频繁使用的键的映射中，应考虑使用较小的整数作为键；例如，一组 24 个或更少的键可以作为非负整数使用单个字节进行编码，如果还使用负整数，则最多可以达到 48 个。较少出现的键可以使用编码较长的整数。

#### 5.6.1. 键的等价性（Equivalence of Keys）

<details>
  <summary>Expand original text</summary>

The specific data model that applies to a CBOR data item is used to determine whether keys occurring in maps are duplicates or distinct.

At the generic data model level, numerically equivalent integer and floating-point values are distinct from each other, as they are from the various big numbers (Tags 2 to 5). Similarly, text strings are distinct from byte strings, even if composed of the same bytes. A tagged value is distinct from an untagged value or from a value tagged with a different tag number.

Within each of these groups, numeric values are distinct unless they are numerically equal (specifically, -0.0 is equal to 0.0); for the purpose of map key equivalence, NaN values are equivalent if they have the same significand after zero-extending both significands at the right to 64 bits.

Both byte strings and text strings are compared byte by byte, arrays are compared element by element, and are equal if they have the same number of bytes/elements and the same values at the same positions. Two maps are equal if they have the same set of pairs regardless of their order; pairs are equal if both the key and value are equal.

Tagged values are equal if both the tag number and the tag content are equal. (Note that a generic decoder that provides processing for a specific tag may not be able to distinguish some semantically equivalent values, e.g., if leading zeroes occur in the content of tag 2 or tag 3 (Section 3.4.3).) Simple values are equal if they simply have the same value. Nothing else is equal in the generic data model; a simple value 2 is not equivalent to an integer 2, and an array is never equivalent to a map.

As discussed in Section 2.2, specific data models can make values equivalent for the purpose of comparing map keys that are distinct in the generic data model. Note that this implies that a generic decoder may deliver a decoded map to an application that needs to be checked for duplicate map keys by that application (alternatively, the decoder may provide a programming interface to perform this service for the application). Specific data models are not able to distinguish values for map keys that are equal for this purpose at the generic data model level.
</details>

适用于 CBOR 数据项的特定数据模型用于确定映射中出现的键是重复的还是不同的。

在通用数据模型层面上，数值上等价的整数和浮点值彼此之间是独立的，就像它们与各种大数（标签 2 到 5）之间的关系一样。同样，文本串与字节串是不同的，即使它们由相同的字节组成。带标签的值不同于未标记的值或带有不同标签编号的值。

在这些组中，数值是不同的，除非它们在数值上相等（具体而言，-0.0 等于 0.0）；对于映射键等价的目的，如果两个 NaN 值的尾数在右边都扩展到 64 位后具有相同的尾数，则它们是等价的。

字节串和文本串按字节逐个进行比较，数组按元素逐个进行比较，并且如果它们具有相同数量的字节/元素以及相同位置的相同值，则它们相等。两个映射相等，如果它们具有相同的键值对集合，而不考虑它们的顺序；如果键和值都相等，则键值对相等。

如果标签编号和标签内容都相等，则带标签的值相等。（请注意，为特定标签提供处理的通用解码器可能无法区分某些语义上等价的值，例如，如果标签 2 或标签 3（第 3.4.3 节）的内容中出现前导零。）简单值相等，如果它们只是具有相同的值。在通用数据模型中，其他任何东西都不相等；简单值 2 不等于整数 2，数组永远不等于映射。

如第 2.2 节所讨论的，特定数据模型可以使通用数据模型中不同的映射键的值等价。请注意，这意味着通用解码器可能会将解码后的映射交付给应用程序，而该应用程序需要检查重复的映射键（或者，解码器可以为应用程序提供执行此服务的编程接口）。特定数据模型无法区分在通用数据模型层面上具有相同目的的映射键的值。

### 5.7. Undefined 值（Undefined Values）

<details>
  <summary>Expand original text</summary>

In some CBOR-based protocols, the simple value (Section 3.3) of undefined might be used by an encoder as a substitute for a data item with an encoding problem, in order to allow the rest of the enclosing data items to be encoded without harm.
</details>

在某些基于 CBOR 的协议中，简单值（第 3.3 节）中的 undefined 值（编码为 0xf7）可能被编码器用作具有编码问题的数据项的替代品，以便允许对包含的其他数据项进行无损害的编码。

## 6. 在 CBOR 和 JSON 之间转换数据（Converting Data between CBOR and JSON）

<details>
  <summary>Expand original text</summary>

This section gives non-normative advice about converting between CBOR and JSON. Implementations of converters MAY use whichever advice here they want.

It is worth noting that a JSON text is a sequence of characters, not an encoded sequence of bytes, while a CBOR data item consists of bytes, not characters.
</details>

本节提供关于在 CBOR 和 JSON 之间转换的非规范性建议。转换器的实现可以（MAY）根据需要使用此处的建议。

值得注意的是，JSON 文本是字符序列，而不是编码的字节序列，而 CBOR 数据项由字节组成，而不是字符。

### 6.1. 从 CBOR 转换为 JSON（Converting from CBOR to JSON）

<details>
  <summary>Expand original text</summary>

Most of the types in CBOR have direct analogs in JSON. However, some do not, and someone implementing a CBOR-to-JSON converter has to consider what to do in those cases. The following non-normative advice deals with these by converting them to a single substitute value, such as a JSON null.

* An integer (major type 0 or 1) becomes a JSON number.
* A byte string (major type 2) that is not embedded in a tag that specifies a proposed encoding is encoded in base64url without padding and becomes a JSON string.
* A UTF-8 string (major type 3) becomes a JSON string. Note that JSON requires escaping certain characters ([RFC8259][RFC8259], Section 7): quotation mark (U+0022), reverse solidus (U+005C), and the "C0 control characters" (U+0000 through U+001F). All other characters are copied unchanged into the JSON UTF-8 string.
* An array (major type 4) becomes a JSON array.
* A map (major type 5) becomes a JSON object. This is possible directly only if all keys are UTF-8 strings. A converter might also convert other keys into UTF-8 strings (such as by converting integers into strings containing their decimal representation); however, doing so introduces a danger of key collision. Note also that, if tags on UTF-8 strings are ignored as proposed below, this will cause a key collision if the tags are different but the strings are the same.
* False (major type 7, additional information 20) becomes a JSON false.
* True (major type 7, additional information 21) becomes a JSON true.
* Null (major type 7, additional information 22) becomes a JSON null.
* A floating-point value (major type 7, additional information 25 through 27) becomes a JSON number if it is finite (that is, it can be represented in a JSON number); if the value is non-finite (NaN, or positive or negative Infinity), it is represented by the substitute value.
* Any other simple value (major type 7, any additional information value not yet discussed) is represented by the substitute value.
* A bignum (major type 6, tag number 2 or 3) is represented by encoding its byte string in base64url without padding and becomes a JSON string. For tag number 3 (negative bignum), a "~" (ASCII tilde) is inserted before the base-encoded value. (The conversion to a binary blob instead of a number is to prevent a likely numeric overflow for the JSON decoder.)
* A byte string with an encoding hint (major type 6, tag number 21 through 23) is encoded as described by the hint and becomes a JSON string.
* For all other tags (major type 6, any other tag number), the tag content is represented as a JSON value; the tag number is ignored.
* Indefinite-length items are made definite before conversion.

A CBOR-to-JSON converter may want to keep to the JSON profile I-JSON [RFC7493][RFC7493], to maximize interoperability and increase confidence that the JSON output can be processed with predictable results. For example, this has implications on the range of integers that can be represented reliably, as well as on the top-level items that may be supported by older JSON implementations.
</details>

CBOR 中的大多数类型在 JSON 中有直接对应项。然而，有些类型没有对应项，实现 CBOR 到 JSON 转换器的人需要考虑在这些情况下该怎么做。以下非规范性建议通过将这些类型转换为单一替代值（substitute value，如 JSON null）来处理这些问题。

* 整数（主类型 0 或 1）转换为 JSON 数字。
* 字节串（主类型 2），如果没有嵌套在指定建议编码的标签中，则以无填充的 base64url 编码，转换为 JSON 字符串。
* UTF-8 字符串（主类型 3）转换为 JSON 字符串。注意，JSON 需要转义某些字符（[RFC8259][RFC8259]，第 7 节）：双引号（U+0022）、反斜线（U+005C）以及“C0 控制字符”（U+0000 到 U+001F）。所有其他字符都原样复制到 JSON UTF-8 字符串中。
* 数组（主类型 4）转换为 JSON 数组。
* 映射（主类型 5）转换为 JSON 对象。只有在所有键都是 UTF-8 字符串时才可以直接实现。转换器还可以将其他键转换为 UTF-8 字符串（例如，将整数转换为包含其十进制表示的字符串）；然而，这样做会引入键冲突的风险。另请注意，如果像下面建议的那样忽略 UTF-8 字符串上的标签，那么当标签不同但字符串相同时，将导致键冲突。
* False（主类型 7，附加信息 20）转换为 JSON false。
* True（主类型 7，附加信息 21）转换为 JSON true。
* Null（主类型 7，附加信息 22）转换为 JSON null。
* 浮点数值（主类型 7，附加信息 25 到 27）如果是有限的（即可以用 JSON 数字表示），则转换为 JSON 数字；如果值是无限的（NaN，或正负无穷大），则用替代值表示。
* 其他任何简单值（主类型 7，尚未讨论的任何附加信息值）用替代值表示。
* 大数（主类型 6，标签编号 2 或 3）通过对其字节串进行无填充的 base64url 编码，转换为 JSON 字符串。对于标签编号 3（负大数），在基数编码值前插入 "~"（ASCII 波浪号）。（将其转换为二进制数据块而不是数字，是为了防止 JSON 解码器可能出现的数值溢出。）
* 带有编码提示的字节串（主类型 6，标签编号 21 到 23）按照提示描述进行编码，转换为 JSON 字符串。
* 对于所有其他标签（主类型 6，任何其他标签编号），将标签内容表示为 JSON 值；忽略标签编号。
* 在转换之前，将不定长项转换为定长项。

CBOR-to-JSON 转换器可能希望遵循 JSON 配置文件 I-JSON [RFC7493][RFC7493]，以最大限度地提高互操作性并增加 JSON 输出能够以可预测的结果处理的信心。例如，这对可靠表示的整数范围以及旧版本 JSON 实现可能支持的顶层数据项有影响。

### 6.2. 从 JSON 转换为 CBOR（Converting from JSON to CBOR）

<details>
  <summary>Expand original text</summary>

All JSON values, once decoded, directly map into one or more CBOR values. As with any kind of CBOR generation, decisions have to be made with respect to number representation. In a suggested conversion:

JSON numbers without fractional parts (integer numbers) are represented as integers (major types 0 and 1, possibly major type 6, tag number 2 and 3), choosing the shortest form; integers longer than an implementation-defined threshold may instead be represented as floating-point values. The default range that is represented as integer is -2^53+1..2^53-1 (fully exploiting the range for exact integers in the binary64 representation often used for decoding JSON [RFC7493][RFC7493]). A CBOR-based protocol, or a generic converter implementation, may choose -2^32..2^32-1 or -2^64..2^64-1 (fully using the integer ranges available in CBOR with uint32_t or uint64_t, respectively) or even -2^31..2^31-1 or -2^63..2^63-1 (using popular ranges for two's complement signed integers). (If the JSON was generated from a JavaScript implementation, its precision is already limited to 53 bits maximum.)
Numbers with fractional parts are represented as floating-point values, performing the decimal-to-binary conversion based on the precision provided by IEEE 754 binary64. The mathematical value of the JSON number is converted to binary64 using the roundTiesToEven procedure in Section 4.3.1 of [IEEE754]. Then, when encoding in CBOR, the preferred serialization uses the shortest floating-point representation exactly representing this conversion result; for instance, 1.5 is represented in a 16-bit floating-point value (not all implementations will be capable of efficiently finding the minimum form, though). Instead of using the default binary64 precision, there may be an implementation-defined limit to the precision of the conversion that will affect the precision of the represented values. Decimal representation should only be used on the CBOR side if that is specified in a protocol.
CBOR has been designed to generally provide a more compact encoding than JSON. One implementation strategy that might come to mind is to perform a JSON-to-CBOR encoding in place in a single buffer. This strategy would need to carefully consider a number of pathological cases, such as that some strings represented with no or very few escapes and longer (or much longer) than 255 bytes may expand when encoded as UTF-8 strings in CBOR. Similarly, a few of the binary floating-point representations might cause expansion from some short decimal representations (1.1, 1e9) in JSON. This may be hard to get right, and any ensuing vulnerabilities may be exploited by an attacker.
</details>

所有 JSON 值在解码后直接映射到一个或多个 CBOR 值。与任何类型的 CBOR 生成一样，必须就数字表示作出决策。在建议的转换中：

没有小数部分的 JSON 数字（整数）表示为整数（主类型 0 和 1，可能为主类型 6，标签编号 2 和 3），选择最短形式；长于实现定义的阈值的整数可能用浮点值表示。默认表示为整数的范围是 -2^53+1..2^53-1（充分利用 binary64 用于解码 JSON 的精确整数范围 [RFC7493][RFC7493]）。基于 CBOR 的协议，或通用转换器实现，可以选择 -2^32..2^32-1 或 -2^64..2^64-1（分别使用 CBOR 中可用的 uint32_t 或 uint64_t 的整数范围）甚至 -2^31..2^31-1 或 -2^63..2^63-1（使用二进制补码有符号整数的流行范围）。（如果 JSON 是从 JavaScript 实现生成的，其精度已经限制在最多 53 位。）
带有小数部分的数字表示为浮点值，根据 IEEE 754 binary64 提供的精度执行十进制到二进制的转换。将 JSON 数字的数学值使用 [IEEE754][IEEE754] 第 4.3.1 节中的 roundTiesToEven 过程转换为 binary64。然后，在 CBOR 编码时，首选序列化使用精确表示此转换结果的最短浮点表示；例如，1.5 用 16 位浮点值表示（尽管并非所有实现都能有效地找到最小形式）。除了使用默认的 binary64 精度之外，可能存在影响表示值精度的实现定义的精度限制。仅当协议中指定时，才应在 CBOR 侧使用十进制表示。

CBOR 旨在通常比 JSON 提供更紧凑的编码。一个可能会想到的实现策略是在单个缓冲区中执行 JSON-to-CBOR 编码。这种策略需要仔细考虑许多特殊情况，例如，一些没有转义或很少转义且长度超过 255 字节的字符串在 CBOR 中编码为 UTF-8 字符串时可能会扩展。类似地，一些二进制浮点表示可能会导致 JSON 中一些短小数表示（1.1、1e9）的扩展。这可能很难做到，并且任何随后的漏洞可能会被攻击者利用。

## 7. CBOR 的未来演进（Future Evolution of CBOR）

<details>
  <summary>Expand original text</summary>

Successful protocols evolve over time. New ideas appear, implementation platforms improve, related protocols are developed and evolve, and new requirements from applications and protocols are added. Facilitating protocol evolution is therefore an important design consideration for any protocol development.

For protocols that will use CBOR, CBOR provides some useful mechanisms to facilitate their evolution. Best practices for this are well known, particularly from JSON format development of JSON-based protocols. Therefore, such best practices are outside the scope of this specification.

However, facilitating the evolution of CBOR itself is very well within its scope. CBOR is designed to both provide a stable basis for development of CBOR-based protocols and to be able to evolve. Since a successful protocol may live for decades, CBOR needs to be designed for decades of use and evolution. This section provides some guidance for the evolution of CBOR. It is necessarily more subjective than other parts of this document. It is also necessarily incomplete, lest it turn into a textbook on protocol development.
</details>

成功的协议会随着时间的推移而演进。新观念出现，实现平台得到改进，相关协议得到开发和演进，以及应用程序和协议中新增的需求。因此，促进协议演进是任何协议开发的重要设计考虑因素。

对于将使用 CBOR 的协议，CBOR 提供了一些有用的机制来促进它们的演进。这方面的最佳实践众所周知，尤其是来自基于 JSON 的协议的 JSON 格式开发。因此，这些最佳实践超出了本规范的范围。

然而，促进 CBOR 本身的演进确实在其范围之内。CBOR 旨在为开发基于 CBOR 的协议提供稳定的基础，并且能够演进。由于一个成功的协议可能会持续数十年，CBOR 需要为数十年的使用和演进而设计。本节为 CBOR 的演进提供了一些指导。这比本文档的其他部分更具主观性。它也必然是不完整的，以免它变成一个关于协议开发的教材。

### 7.1. 扩展点（Extension Points）

<details>
  <summary>Expand original text</summary>

In a protocol design, opportunities for evolution are often included in the form of extension points. For example, there may be a codepoint space that is not fully allocated from the outset, and the protocol is designed to tolerate and embrace implementations that start using more codepoints than initially allocated.

Sizing the codepoint space may be difficult because the range required may be hard to predict. Protocol designs should attempt to make the codepoint space large enough so that it can slowly be filled over the intended lifetime of the protocol.

CBOR has three major extension points:

**the "simple" space (values in major type 7):**
Of the 24 efficient (and 224 slightly less efficient) values, only a small number have been allocated. Implementations receiving an unknown simple data item may easily be able to process it as such, given that the structure of the value is indeed simple. The IANA registry in Section 9.1 is the appropriate way to address the extensibility of this codepoint space.

**the "tag" space (values in major type 6):**
The total codepoint space is abundant; only a tiny part of it has been allocated. However, not all of these codepoints are equally efficient: the first 24 only consume a single ("1+0") byte, and half of them have already been allocated. The next 232 values only consume two ("1+1") bytes, with nearly a quarter already allocated. These subspaces need some curation to last for a few more decades. Implementations receiving an unknown tag number can choose to process just the enclosed tag content or, preferably, to process the tag as an unknown tag number wrapping the tag content. The IANA registry in Section 9.2 is the appropriate way to address the extensibility of this codepoint space.

**the "additional information" space:**
An implementation receiving an unknown additional information value has no way to continue decoding, so allocating codepoints in this space is a major step beyond just exercising an extension point. There are also very few codepoints left. See also Section 7.2.
</details>

在协议设计中，演进的机会通常以扩展点的形式包含在内。例如，可能有一个从一开始就没有完全分配的代码点空间，协议被设计为能容忍并接纳开始使用比最初分配的更多代码点的实现。

确定代码点空间的大小可能很困难，因为所需的范围可能很难预测。协议设计应尝试使代码点空间足够大，以便在协议的预期使用寿命内可以慢慢填充。

CBOR 有三个主要扩展点：

**"simple" 空间（主类型 7 中的值）：**
在 24 个高效（和 224 个稍微低效）的值中，只有一小部分已被分配。实现接收到未知简单数据项时，可能很容易将其处理为简单数据项，因为该值的结构确实是简单的。第 9.1 节的 IANA 注册表是解决此代码点空间可扩展性的适当方式。

**"tag" 空间（主类型 6 中的值）：**
总的代码点空间非常丰富，但只有一小部分已被分配。然而，并非所有这些代码点都同样高效：前 24 个只占用一个（“1+0”）字节，其中一半已被分配。接下来的 232 个值只占用两个（“1+1”）字节，其中近四分之一已经分配。这些子空间需要一些整理以维持几十年。实现接收到未知标签号时可以选择仅处理封装的标签内容，或者最好处理为未知标签号包装标签内容。第 9.2 节的 IANA 注册表是解决此代码点空间可扩展性的适当方式。

**"additional information" 空间：**
一个实现接收到未知附加信息值时无法继续解码，因此在这个空间分配代码点是比仅仅使用扩展点更重要的一步。这里还剩下很少的代码点。另请参见第 7.2 节。

### 7.2. 策展附加信息空间（Curating the Additional Information Space）

<details>
  <summary>Expand original text</summary>

The human mind is sometimes drawn to filling in little perceived gaps to make something neat. We expect the remaining gaps in the codepoint space for the additional information values to be an attractor for new ideas, just because they are there.

The present specification does not manage the additional information codepoint space by an IANA registry. Instead, allocations out of this space can only be done by updating this specification.

For an additional information value of n >= 24, the size of the additional data typically is 2n-24 bytes. Therefore, additional information values 28 and 29 should be viewed as candidates for 128-bit and 256-bit quantities, in case a need arises to add them to the protocol. Additional information value 30 is then the only additional information value available for general allocation, and there should be a very good reason for allocating it before assigning it through an update of the present specification.
</details>

人类思维有时会被吸引到填补小的感知缺口以使事物变得整洁。我们预计附加信息值代码点空间中剩余的缺口将成为新想法的吸引物，仅仅因为它们存在。

本规范没有通过 IANA 注册表管理附加信息代码点空间。相反，从这个空间中分配只能通过更新本规范来完成。

对于附加信息值 n >= 24，附加数据的大小通常是 2n-24 字节。因此，附加信息值 28 和 29 应被视为 128 位和 256 位数量的候选项，以防需要将它们添加到协议中。附加信息值 30 是唯一可用于一般分配的附加信息值，在通过更新现有规范分配它之前，应该有一个非常好的理由。

## 8. 诊断符号表示法（Diagnostic Notation）

<details>
  <summary>Expand original text</summary>

CBOR is a binary interchange format. To facilitate documentation and debugging, and in particular to facilitate communication between entities cooperating in debugging, this section defines a simple human-readable diagnostic notation. All actual interchange always happens in the binary format.

Note that this truly is a diagnostic format; it is not meant to be parsed. Therefore, no formal definition (as in ABNF) is given in this document. (Implementers looking for a text-based format for representing CBOR data items in configuration files may also want to consider [YAML][YAML].)

The diagnostic notation is loosely based on JSON as it is defined in RFC 8259, extending it where needed.

The notation borrows the JSON syntax for numbers (integer and floating-point), True (>true<), False (>false<), Null (>null<), UTF-8 strings, arrays, and maps (maps are called objects in JSON; the diagnostic notation extends JSON here by allowing any data item in the key position). Undefined is written >undefined< as in JavaScript. The non-finite floating-point numbers Infinity, -Infinity, and NaN are written exactly as in this sentence (this is also a way they can be written in JavaScript, although JSON does not allow them). A tag is written as an integer number for the tag number, followed by the tag content in parentheses; for instance, a date in the format specified by RFC 3339 (ISO 8601) could be notated as:

```txt
0("2013-03-21T20:04:00Z")
```

or the equivalent relative time as the following:

```txt
1(1363896240)
```

Byte strings are notated in one of the base encodings, without padding, enclosed in single quotes, prefixed by >h< for base16, >b32< for base32, >h32< for base32hex, >b64< for base64 or base64url (the actual encodings do not overlap, so the string remains unambiguous). For example, the byte string 0x12345678 could be written h'12345678', b32'CI2FM6A', or b64'EjRWeA'.

Unassigned simple values are given as "simple()" with the appropriate integer in the parentheses. For example, "simple(42)" indicates major type 7, value 42.

A number of useful extensions to the diagnostic notation defined here are provided in Appendix G of [RFC8610][RFC8610], "Extended Diagnostic Notation" (EDN). Similarly, this notation could be extended in a separate document to provide documentation for NaN payloads, which are not covered in this document.
</details>

CBOR 是一种二进制交换格式。为了便于记录和调试，特别是为了便于在调试过程中相互合作的实体之间进行沟通，本节定义了一种简单的人类可读诊断表示法。所有实际交换都始终以二进制格式进行。

请注意，这确实是一种诊断格式；它并不意味着要进行解析。因此，本文档未给出正式定义（如 ABNF 中的定义）。（在配置文件中表示 CBOR 数据项的基于文本的格式的实现者可能还需要考虑 [YAML][YAML]。）

诊断表示法松散地基于 RFC 8259 中定义的 JSON，并在需要时对其进行扩展。

表示法借用了 JSON 语法中的数字（整数和浮点数）、True（`true`）、False（`false`）、Null（`null`）、UTF-8 字符串、数组和映射（在 JSON 中，映射被称为对象；诊断表示法在此通过允许在键位置使用任何数据项来扩展 JSON）。Undefined 如 JavaScript 中一样写作 `undefined`。非有限浮点数 `Infinity`、`-Infinity` 和 `NaN` 正如本句中一样表示（这也是它们可以在 JavaScript 中表示的方式，尽管 JSON 不允许它们）。标签以标签号的整数形式表示，后跟括号内的标签内容；例如，RFC 3339（ISO 8601）规定的日期格式可以表示为：

```txt
0("2013-03-21T20:04:00Z")
```

或等效的相对时间如下：

```txt
1(1363896240)
```

字节串用单引号括起，不带填充，前缀为基数编码，`h` 代表 base16，`b32` 代表 base32，`h32` 代表 base32hex，`b64` 代表 base64 或 base64url（实际编码没有重叠，因此字符串仍然是无歧义的）。例如，字节串 0x12345678 可以写成 `h'12345678'`、`b32'CI2FM6A'` 或 `b64'EjRWeA'`。

未分配的简单值表示为 `simple()`，括号内是适当的整数。例如，`simple(42)` 表示主类型 7，值 42。

[RFC8610][RFC8610] 附录 G 中提供了一些对此处定义的诊断表示法有用的扩展，名为“扩展诊断表示法”（EDN）。类似地，本表示法可以在另一个文档中扩展以提供 NaN 有效载荷的文档，本文档中未涉及这些有效载荷。

## 8.1. 编码指示符（Encoding Indicators）

<details>
  <summary>Expand original text</summary>

Sometimes it is useful to indicate in the diagnostic notation which of several alternative representations were actually used; for example, a data item written `1.5` by a diagnostic decoder might have been encoded as a half-, single-, or double-precision float.

The convention for encoding indicators is that anything starting with an underscore and all following characters that are alphanumeric or underscore is an encoding indicator, and can be ignored by anyone not interested in this information. For example, `_` or `_3`. Encoding indicators are always optional.

A single underscore can be written after the opening brace of a map or the opening bracket of an array to indicate that the data item was represented in indefinite-length format. For example, `[_ 1, 2]` contains an indicator that an indefinite-length representation was used to represent the data item `[1, 2]`.

An underscore followed by a decimal digit n indicates that the preceding item (or, for arrays and maps, the item starting with the preceding bracket or brace) was encoded with an additional information value of 24+n. For example, `1.5_1` is a half-precision floating-point number, while `1.5_3` is encoded as double precision. This encoding indicator is not shown in Appendix A. (Note that the encoding indicator "_" is thus an abbreviation of the full form "_7", which is not used.)

The detailed chunk structure of byte and text strings of indefinite length can be notated in the form `(_ h'0123', h'4567')` and `(_ "foo", "bar")`. However, for an indefinite-length string with no chunks inside, `(_ )` would be ambiguous as to whether a byte string (0x5fff) or a text string (0x7fff) is meant and is therefore not used. The basic forms `''_` and `""_` can be used instead and are reserved for the case of no chunks only -- not as short forms for the (permitted, but not really useful) encodings with only empty chunks, which need to be notated as `(_ '')`, `(_ "")`, etc., to preserve the chunk structure.
</details>

有时在诊断表示法中指示实际使用的几种可选表示法中的哪一种是有用的；例如，诊断解码器写的数据项 `1.5` 可能被编码为半精度、单精度或双精度浮点数。

编码指示符的约定是，以下划线开头的任何内容以及后续所有字母数字或下划线字符都是编码指示符，对于对这些信息不感兴趣的人可以忽略。例如，`_` 或 `_3`。编码指示符始终是可选的。

在映射的开始大括号或数组的开始括号后可以写一个下划线，以指示数据项在不定长度格式中表示。例如，`[_ 1, 2]` 包含一个指示符，表示数据项 `[1, 2]` 是使用不定长度表示法表示的。

下划线后跟一个十进制数字 n 表示前面的数据项（或对于数组和映射，以前面的括号或大括号开始的数据项）编码为附加信息值 24+n。例如，`1.5_1` 是半精度浮点数，而 `1.5_3` 是双精度编码。附录 A 中未显示此编码指示符。（注意，编码指示符 "_" 因此是全形式 "_7" 的缩写，而不使用全形式。）

无限长度字节和文本串的详细块结构可以表示为 `(_ h'0123', h'4567')` 和 `(_ "foo", "bar")`。然而，对于没有块的无限长度字符串，`(_ )` 在表示字节串（0x5fff）还是文本串（0x7fff）时将产生歧义，因此不使用。可以使用基本形式 `''_` 和 `""_`，并仅保留用于没有块的情况 -- 而不是用作仅具有空块的编码的简写形式（允许，但实际上没有用），这些编码需要表示为 `(_ '')`, `(_ "")` 等，以保留块结构。

## 9. IANA 注意事项（IANA Considerations）

<details>
  <summary>Expand original text</summary>

IANA has created two registries for new CBOR values. The registries are separate, that is, not under an umbrella registry, and follow the rules in [RFC8126][RFC8126]. IANA has also assigned a new media type, an associated CoAP Content-Format entry, and a structured syntax suffix.
</details>

IANA 为新的 CBOR 值创建了两个注册表。注册表是独立的，即没有在总注册表下，并遵循 [RFC8126][RFC8126] 中的规则。IANA 还分配了一个新的媒体类型、一个相关的 CoAP Content-Format 条目和一个结构化语法后缀。

### 9.1. CBOR 简单值注册表（CBOR Simple Values Registry）

<details>
  <summary>Expand original text</summary>

IANA has created the "Concise Binary Object Representation (CBOR) Simple Values" registry at [IANA.cbor-simple-values][IANA.cbor-simple-values]. The initial values are shown in Table 4.

New entries in the range 0 to 19 are assigned by Standards Action [RFC8126][RFC8126]. It is suggested that IANA allocate values starting with the number 16 in order to reserve the lower numbers for contiguous blocks (if any).

New entries in the range 32 to 255 are assigned by Specification Required.
</details>

IANA 在 [IANA.cbor-simple-values][IANA.cbor-simple-values] 处创建了“简明二进制对象表示（CBOR）简单值”注册表。初始值如表 4 所示。

范围 0 至 19 的新条目由标准操作 [RFC8126][RFC8126] 分配。建议 IANA 从 16 开始分配值，以便为连续的块（如果有的话）预留较低的数字。

范围 32 至 255 的新条目由规范要求分配。

### 9.2. CBOR 标签注册表（CBOR Tags Registry）

<details>
  <summary>Expand original text</summary>

IANA has created the "Concise Binary Object Representation (CBOR) Tags" registry at [IANA.cbor-tags][IANA.cbor-tags]. The tags that were defined in [RFC7049][RFC7049] are described in detail in Section 3.4, and other tags have already been defined since then.

New entries in the range 0 to 23 ("1+0") are assigned by Standards Action. New entries in the ranges 24 to 255 ("1+1") and 256 to 32767 (lower half of "1+2") are assigned by Specification Required. New entries in the range 32768 to 18446744073709551615 (upper half of "1+2", "1+4", and "1+8") are assigned by First Come First Served. The template for registration requests is:

* Data item
* Semantics (short form)

In addition, First Come First Served requests should include:

* Point of contact
* Description of semantics (URL) -- This description is optional; the URL can point to something like an Internet-Draft or a web page.

Applicants exercising the First Come First Served range and making a suggestion for a tag number that is not representable in 32 bits (i.e., larger than 4294967295) should be aware that this could reduce interoperability with implementations that do not support 64-bit numbers.
</details>

IANA 在 [IANA.cbor-tags][IANA.cbor-tags] 处创建了“简明二进制对象表示（CBOR）标签”注册表。在 [RFC7049][RFC7049] 中定义的标签在第 3.4 节中详细描述，此后已经定义了其他标签。

范围 0 至 23（“1+0”）的新条目由标准操作分配。范围 24 至 255（“1+1”）和 256 至 32767（“1+2”下半部分）的新条目由规范要求分配。范围 32768 至 18446744073709551615（“1+2”上半部分、“1+4”和“1+8”）的新条目由先到先得分配。注册请求的模板是：

* 数据项（Data item）
* 语义（Semantics，简短形式）

此外，先到先得的请求还应包括：

* 联系人（Point of contact）
* 语义描述（Description of semantics URL）-- 该描述是可选的；URL 可以指向类似于 Internet-Draft 或网页的内容。

在先到先得范围内申请并提议一个无法用 32 位表示的标签号（即大于 4294967295 ）的申请人应注意，这可能会降低与不支持 64 位数字的实现的互操作性。

### 9.3. Media Types Registry（略，见原文）
### 9.4. CoAP Content-Format Registry（略，见原文）
### 9.5. Structured Syntax Suffix Registry（略，见原文）

## 10. 安全性考虑（Security Considerations）

<details>
  <summary>Expand original text</summary>

A network-facing application can exhibit vulnerabilities in its processing logic for incoming data. Complex parsers are well known as a likely source of such vulnerabilities, such as the ability to remotely crash a node, or even remotely execute arbitrary code on it. CBOR attempts to narrow the opportunities for introducing such vulnerabilities by reducing parser complexity, by giving the entire range of encodable values a meaning where possible.

Because CBOR decoders are often used as a first step in processing unvalidated input, they need to be fully prepared for all types of hostile input that may be designed to corrupt, overrun, or achieve control of the system decoding the CBOR data item. A CBOR decoder needs to assume that all input may be hostile even if it has been checked by a firewall, has come over a secure channel such as TLS, is encrypted or signed, or has come from some other source that is presumed trusted.

Section 4.1 gives examples of limitations in interoperability when using a constrained CBOR decoder with input from a CBOR encoder that uses a non-preferred serialization. When a single data item is consumed both by such a constrained decoder and a full decoder, it can lead to security issues that can be exploited by an attacker who can inject or manipulate content.

As discussed throughout this document, there are many values that can be considered "equivalent" in some circumstances and "not equivalent" in others. As just one example, the numeric value for the number "one" might be expressed as an integer or a bignum. A system interpreting CBOR input might accept either form for the number "one", or might reject one (or both) forms. Such acceptance or rejection can have security implications in the program that is using the interpreted input.

Hostile input may be constructed to overrun buffers, to overflow or underflow integer arithmetic, or to cause other decoding disruption. CBOR data items might have lengths or sizes that are intentionally extremely large or too short. Resource exhaustion attacks might attempt to lure a decoder into allocating very big data items (strings, arrays, maps, or even arbitrary precision numbers) or exhaust the stack depth by setting up deeply nested items. Decoders need to have appropriate resource management to mitigate these attacks. (Items for which very large sizes are given can also attempt to exploit integer overflow vulnerabilities.)

A CBOR decoder, by definition, only accepts well-formed CBOR; this is the first step to its robustness. Input that is not well-formed CBOR causes no further processing from the point where the lack of well-formedness was detected. If possible, any data decoded up to this point should have no impact on the application using the CBOR decoder.

In addition to ascertaining well-formedness, a CBOR decoder might also perform validity checks on the CBOR data. Alternatively, it can leave those checks to the application using the decoder. This choice needs to be clearly documented in the decoder. Beyond the validity at the CBOR level, an application also needs to ascertain that the input is in alignment with the application protocol that is serialized in CBOR.

The input check itself may consume resources. This is usually linear in the size of the input, which means that an attacker has to spend resources that are commensurate to the resources spent by the defender on input validation. However, an attacker might be able to craft inputs that will take longer for a target decoder to process than for the attacker to produce. Processing for arbitrary-precision numbers may exceed linear effort. Also, some hash-table implementations that are used by decoders to build in-memory representations of maps can be attacked to spend quadratic effort, unless a secret key (see Section 7 of [SIPHASH_LNCS][SIPHASH_LNCS], also [SIPHASH_OPEN][SIPHASH_OPEN]) or some other mitigation is employed. Such superlinear efforts can be exploited by an attacker to exhaust resources at or before the input validator; they therefore need to be avoided in a CBOR decoder implementation. Note that tag number definitions and their implementations can add security considerations of this kind; this should then be discussed in the security considerations of the tag number definition.

CBOR encoders do not receive input directly from the network and are thus not directly attackable in the same way as CBOR decoders. However, CBOR encoders often have an API that takes input from another level in the implementation and can be attacked through that API. The design and implementation of that API should assume the behavior of its caller may be based on hostile input or on coding mistakes. It should check inputs for buffer overruns, overflow and underflow of integer arithmetic, and other such errors that are aimed to disrupt the encoder.

Protocols should be defined in such a way that potential multiple interpretations are reliably reduced to a single interpretation. For example, an attacker could make use of invalid input such as duplicate keys in maps, or exploit different precision in processing numbers to make one application base its decisions on a different interpretation than the one that will be used by a second application. To facilitate consistent interpretation, encoder and decoder implementations should provide a validity-checking mode of operation (Section 5.4). Note, however, that a generic decoder cannot know about all requirements that an application poses on its input data; it is therefore not relieving the application from performing its own input checking. Also, since the set of defined tag numbers evolves, the application may employ a tag number that is not yet supported for validity checking by the generic decoder it uses. Generic decoders therefore need to document which tag numbers they support and what validity checking they provide for those tag numbers as well as for basic CBOR (UTF-8 checking, duplicate map key checking).

Section 3.4.3 notes that using the non-preferred choice of a bignum representation instead of a basic integer for encoding a number is not intended to have application semantics, but it can have such semantics if an application receiving CBOR data is using a decoder in the basic generic data model. This disparity causes a security issue if the two sets of semantics differ. Thus, applications using CBOR need to specify the data model that they are using for each use of CBOR data.

It is common to convert CBOR data to other formats. In many cases, CBOR has more expressive types than other formats; this is particularly true for the common conversion to JSON. The loss of type information can cause security issues for the systems that are processing the less-expressive data.

Section 6.2 describes a possibly common usage scenario of converting between CBOR and JSON that could allow an attack if the attacker knows that the application is performing the conversion.

Security considerations for the use of base16 and base64 from [RFC4648][RFC4648], and the use of UTF-8 from [RFC3629], are relevant to CBOR as well.
</details>

面向网络的应用程序在处理传入数据的逻辑时可能会暴露出漏洞。复杂的解析器被公认为可能导致诸如远程崩溃节点或甚至在其上远程执行任意代码等漏洞的来源。CBOR 试图通过降低解析器复杂性来减少引入这类漏洞的机会，尽可能为可编码值的整个范围赋予意义。

由于 CBOR 解码器通常用作处理未经验证输入的第一步，因此它们需要充分准备应对所有可能设计用于破坏、越界或实现对解码 CBOR 数据项的系统控制的恶意输入。CBOR 解码器需要假设所有输入都可能是敌对的，即使它已经过防火墙检查，通过了像 TLS 这样的安全通道，是加密或签名的，或者来自其他被认为可信的来源。

第 4.1 节举例说明了当使用受限制的 CBOR 解码器处理来自使用非优选序列化的 CBOR 编码器的输入时，互操作性方面的限制。当单个数据项既被这样的受限解码器也被完整解码器消耗时，可能导致安全问题，攻击者可以利用这些安全问题注入或操纵内容。

正如本文档所讨论的，在某些情况下，许多值可以被认为是“等价”的，而在其他情况下则被认为是“不等价”的。举一个例子，数字“一”的数值值可以表示为整数或大数。解释 CBOR 输入的系统可能接受数字“一”的任一形式，也可能拒绝其中一个（或两个）形式。在使用解释输入的程序中，这种接受或拒绝可能具有安全性含义。

恶意输入可能被构造为越过缓冲区，溢出或下溢整数运算，或导致其他解码中断。CBOR 数据项可能具有故意非常大或太短的长度或大小。资源耗尽攻击可能试图引诱解码器分配非常大的数据项（字符串、数组、映射或甚至任意精度数字）或通过设置深度嵌套的数据项来耗尽堆栈深度。解码器需要具有适当的资源管理来抵御这些攻击。（给出非常大尺寸的数据项也可能试图利用整数溢出漏洞。）

CBOR 解码器，顾名思义，只接受格式良好的 CBOR；这是其健壮性的第一步。不符合 CBOR 格式的输入将不会从发现格式错误的地方开始进行进一步处理。如果可能的话，在此点之前解码的任何数据都不应对使用 CBOR 解码器的应用程序产生影响。

除了确定格式良好之外，CBOR 解码器还可以对 CBOR 数据进行有效性检查。或者，它可以将这些检查留给使用解码器的应用程序。这个选择需要在解码器中明确记录。除了 CBOR 级别的有效性之外，应用程序还需要确保输入与使用 CBOR 序列化的应用程序协议保持一致。

输入检查本身可能会消耗资源。这通常与输入的大小成线性关系，这意味着攻击者必须花费与防御者在输入验证上花费的资源相当的资源。然而，攻击者可能能够制作一些输入，使目标解码器处理所需的时间比攻击者制作所需的时间更长。处理任意精度数字的过程可能超过线性努力。此外，一些由解码器用于构建内存映射表示的哈希表实现可能会被攻击以消耗二次方努力，除非使用秘密密钥（参见 [SIPHASH_LNCS][SIPHASH_LNCS] 第7节，也参见 [SIPHASH_OPEN][SIPHASH_OPEN]）或其他一些缓解措施。这种超线性努力可能会被攻击者利用，以耗尽输入验证器处或之前的资源；因此，在 CBOR 解码器实现中需要避免这种情况。请注意，标签号定义及其实现可能会增加这类安全性考虑；这应该在标签号定义的安全性考虑中讨论。

CBOR 编码器不直接从网络接收输入，因此与 CBOR 解码器不同，它们无法直接受到攻击。然而，CBOR 编码器通常具有一个从实现的另一个层次接收输入的 API，并且可以通过该 API 受到攻击。该API的设计和实现应假设其调用者的行为可能基于恶意输入或编码错误。它应该检查输入是否存在缓冲区越界、整数运算溢出和下溢以及其他旨在破坏编码器的错误。

协议应该以这样一种方式定义，以便可靠地将潜在的多重解释减少为单一解释。例如，攻击者可以利用无效输入，例如地图中的重复键，或者利用处理数字时的不同精度，使一个应用程序根据与第二个应用程序所使用的解释不同的解释来做出决策。为了便于一致的解释，编码器和解码器实现应提供有效性检查的操作模式（第 5.4 节）。但是，请注意，通用解码器无法了解应用程序对其输入数据的所有要求；因此，它并不能免除应用程序执行自己的输入检查。此外，由于已定义的标签号集合在不断演进，应用程序可能使用尚未得到通用解码器支持的标签号进行有效性检查。通用解码器因此需要记录它们支持哪些标签号以及为这些标签号以及基本 CBOR（UTF-8 检查，重复映射键检查）提供哪些有效性检查。

第 3.4.3 节指出，使用大数表示法而不是基本整数对数字进行编码的非首选选择并不是为了具有应用程序语义，但如果接收 CBOR 数据的应用程序使用基本通用数据模型中的解码器，它可能具有这样的语义。如果两组语义不同，则此差异会导致安全问题。因此，使用 CBOR 的应用程序需要指定它们对每个 CBOR 数据使用的数据模型。

将 CBOR 数据转换为其他格式是很常见的。在许多情况下，CBOR 具有比其他格式更具表现力的类型；这对于通常转换为 JSON 尤为正确。类型信息的丢失可能会给处理表达能力较弱数据的系统带来安全问题。

第 6.2 节描述了一个可能的常见使用场景，即在 CBOR 和 JSON 之间进行转换，如果攻击者知道应用程序正在执行转换，可能允许攻击。

[RFC4648][RFC4648] 中关于 base16 和 base64 的使用以及 [RFC3629][RFC3629] 中关于 UTF-8 的使用的安全性考虑也与 CBOR 相关。

## 原文：[Concise Binary Object Representation (CBOR)](https://datatracker.ietf.org/doc/html/rfc8949)

[C]: https://www.iso.org/standard/74528.html
[Cplusplus20]: https://isocpp.org/files/papers/N4860.pdf
[IEEE754]: https://ieeexplore.ieee.org/document/8766229
[RFC2045]: https://www.rfc-editor.org/info/rfc2045
[RFC2119]: https://www.rfc-editor.org/info/rfc2119
[RFC3339]: https://www.rfc-editor.org/info/rfc3339
[RFC3629]: https://www.rfc-editor.org/info/rfc3629
[RFC3986]: https://www.rfc-editor.org/info/rfc3986
[RFC4287]: https://www.rfc-editor.org/info/rfc4287
[RFC4648]: https://www.rfc-editor.org/info/rfc4648
[RFC8126]: https://www.rfc-editor.org/info/rfc8126
[RFC8174]: https://www.rfc-editor.org/info/rfc8174
[TIME_T]: https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap04.html#tag_04_16
[ASN.1]: https://www.itu.int/rec/T-REC-X.690-201508-I/en
[BSON]: http://bsonspec.org/
[CBOR-TAGS]: https://tools.ietf.org/html/draft-bormann-cbor-notable-tags-02
[ECMA262]: https://www.ecma-international.org/publications/standards/Ecma-262.htm
[Err3764]: https://www.rfc-editor.org/errata/eid3764
[Err3770]: https://www.rfc-editor.org/errata/eid3770
[Err4294]: https://www.rfc-editor.org/errata/eid4294
[Err4409]: https://www.rfc-editor.org/errata/eid4409
[Err4963]: https://www.rfc-editor.org/errata/eid4963
[Err4964]: https://www.rfc-editor.org/errata/eid4964
[Err5434]: https://www.rfc-editor.org/errata/eid5434
[Err5763]: https://www.rfc-editor.org/errata/eid5763
[Err5917]: https://www.rfc-editor.org/errata/eid5917
[IANA.cbor-simple-values]: https://www.iana.org/assignments/cbor-simple-values
[IANA.cbor-tags]: https://www.iana.org/assignments/cbor-tags
[IANA.core-parameters]: https://www.iana.org/assignments/core-parameters
[IANA.media-types]: https://www.iana.org/assignments/media-types
[IANA.structured-suffix]: https://www.iana.org/assignments/media-type-structured-suffix
[MessagePack]: https://msgpack.org/
[PCRE]: https://www.pcre.org/
[RFC0713]: https://www.rfc-editor.org/info/rfc713
[RFC6838]: https://www.rfc-editor.org/info/rfc6838
[RFC7049]: https://www.rfc-editor.org/info/rfc7049
[RFC7228]: https://www.rfc-editor.org/info/rfc7228
[RFC7493]: https://www.rfc-editor.org/info/rfc7493
[RFC7991]: https://www.rfc-editor.org/info/rfc7991
[RFC8259]: https://www.rfc-editor.org/info/rfc8259
[RFC8610]: https://www.rfc-editor.org/info/rfc8610
[RFC8618]: https://www.rfc-editor.org/info/rfc8618
[RFC8742]: https://www.rfc-editor.org/info/rfc8742
[RFC8746]: https://www.rfc-editor.org/info/rfc8746
[SIPHASH_LNCS]: https://doi.org/10.1007/978-3-642-34931-7_28
[SIPHASH_OPEN]: https://www.aumasson.jp/siphash/siphash.pdf
[YAML]: https://www.yaml.org/spec/1.2/spec.html
[Table 7]: https://datatracker.ietf.org/doc/html/rfc8949#jumptable