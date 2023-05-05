---
sidebar_position: 2
---

# JSON Schema 规范

本文使用 GPT-4 翻译，做了一些微调和排版优化，原文：[JSON Schema: A Media Type for Describing JSON Documents](https://json-schema.org/draft/2020-12/json-schema-core.html).

## 1. 引言（Introduction）

<details>
  <summary>Expand original text</summary>

JSON Schema is a JSON media type for defining the structure of JSON data. JSON Schema is intended to define validation, documentation, hyperlink navigation, and interaction control of JSON data.

This specification defines JSON Schema core terminology and mechanisms, including pointing to another JSON Schema by reference, dereferencing a JSON Schema reference, specifying the dialect being used, specifying a dialect's vocabulary requirements, and defining the expected output.

Other specifications define the vocabularies that perform assertions about validation, linking, annotation, navigation, and interaction.
</details>

JSON 模式（JSON Schema）是一种用于定义 JSON 数据结构的 JSON 媒体类型。JSON Schema 旨在定义 JSON 数据的验证、文档、超链接导航和交互控制。

本规范定义了 JSON Schema 核心术语和机制，包括通过引用指向另一个 JSON Schema、取消引用 JSON Schema 引用、指定正在使用的方言（dialect）、指定方言的词汇表（vocabulary）要求以及定义预期输出。

其它规范定义了用于执行关于验证（validation）、链接（linking）、注释（annotation）、导航（navigation）和交互（interaction）的断言的词汇表。

## 2. 约定和术语（Conventions and Terminology）

<details>
  <summary>Expand original text</summary>

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC2119].

The terms "JSON", "JSON text", "JSON value", "member", "element", "object", "array", "number", "string", "boolean", "true", "false", and "null" in this document are to be interpreted as defined in [RFC 8259][RFC8259].
</details>

本文档中的关键词 "必须"（MUST），"必须不"（MUST NOT），"要求"（REQUIRED），"应"（SHALL），"不应"（SHALL NOT），"应该"（SHOULD），"不应该"（SHOULD NOT），"建议"（RECOMMENDED），"可以"（MAY）和 "可选"（OPTIONAL）应按照 [RFC 2119][RFC2119] 中的描述进行解释。

本文档中的术语 "JSON"、"JSON text"、"JSON value"、"member"、"element"、"object"、"array"、"number"、"string"、"boolean"、"true"、"false" 和 "null" 应按照 [RFC 8259][RFC8259] 中的定义进行解释。

## 3. 概述（Overview）

<details>
  <summary>Expand original text</summary>

This document proposes a new media type "application/schema+json" to identify a JSON Schema for describing JSON data. It also proposes a further optional media type, "application/schema-instance+json", to provide additional integration features. JSON Schemas are themselves JSON documents. This, and related specifications, define keywords allowing authors to describe JSON data in several ways.

JSON Schema uses keywords to assert constraints on JSON instances or annotate those instances with additional information. Additional keywords are used to apply assertions and annotations to more complex JSON data structures, or based on some sort of condition.

To facilitate re-use, keywords can be organized into vocabularies. A vocabulary consists of a list of keywords, together with their syntax and semantics. A dialect is defined as a set of vocabularies and their required support identified in a meta-schema.

JSON Schema can be extended either by defining additional vocabularies, or less formally by defining additional keywords outside of any vocabulary. Unrecognized individual keywords simply have their values collected as annotations, while the behavior with respect to an unrecognized vocabulary can be controlled when declaring which vocabularies are in use.

This document defines a core vocabulary that MUST be supported by any implementation, and cannot be disabled. Its keywords are each prefixed with a "$" character to emphasize their required nature. This vocabulary is essential to the functioning of the "application/schema+json" media type, and is used to bootstrap the loading of other vocabularies.

Additionally, this document defines a RECOMMENDED vocabulary of keywords for applying subschemas conditionally, and for applying subschemas to the contents of objects and arrays. Either this vocabulary or one very much like it is required to write schemas for non-trivial JSON instances, whether those schemas are intended for assertion validation, annotation, or both. While not part of the required core vocabulary, for maximum interoperability this additional vocabulary is included in this document and its use is strongly encouraged.

Further vocabularies for purposes such as structural validation or hypermedia annotation are defined in other documents. These other documents each define a dialect collecting the standard sets of vocabularies needed to write schemas for that document's purpose.
</details>

本文档提议一种新的媒体类型 `application/schema+json`，用于标识描述 JSON 数据的 JSON Schema 。它还提议了另一种可选的媒体类型 `application/schema-instance+json`，以提供其它集成功能。JSON Schema 本身是 JSON 文档。此类及相关规范定义了允许作者以多种方式描述 JSON 数据的关键字。

JSON Schema 使用关键字（keywords）对 JSON 实例进行约束或用附加信息对这些实例进行注释。其它关键字用于对更复杂的 JSON 数据结构应用断言（assertions）和注释（annotations），或基于某种条件。

为了便于重用，关键字可以组织成词汇表（vocabularies）。一个词汇表由一组关键字及其语法和语义组成。一种方言（dialect）被定义为一组词汇表及其在 meta-schema 中确定的必须支持。

JSON Schema 可以通过定义额外的词汇表或者在词汇表之外非正式地定义额外的关键字来扩展。无法识别的单个关键字会将其值作为注释收集，而在声明正在使用的词汇表时，可以控制对无法识别的词汇表的行为。

本文档定义了一个核心词汇表，必须（MUST）由任何实现（implementation）支持，并且不能被禁用。其关键字都以 `$` 字符为前缀，以强调其必需性。这个词汇表对于 `application/schema+json` 媒体类型的功能至关重要，并用于引导其它词汇表的加载。

此外，本文档定义了一个建议（RECOMMENDED）的关键字词汇表，用于有条件地应用 subschemas，以及将 subschemas 应用于对象和数组的内容。为了编写用于断言验证、注释或两者兼而有之的非平凡 JSON 实例的 JSON Schema，需要使用这个词汇表或类似的词汇表。虽然不是必需的核心词汇表的一部分，但为了最大的互操作性，本文档包含了这个附加词汇表，并强烈推荐使用。

其它文档中定义了用于结构验证或超媒体注释等目的的更多词汇表。这些其它文档中的每一个都定义了一个方言，收集编写用于该文档目的的 JSON Schema 所需的标准词汇表集合。

## 4. 定义（Definitions）

### 4.1 JSON 文档

<details>
  <summary>Expand original text</summary>

A JSON document is an information resource (series of octets) described by the application/json media type.

In JSON Schema, the terms "JSON document", "JSON text", and "JSON value" are interchangeable because of the data model it defines.

JSON Schema is only defined over JSON documents. However, any document or memory structure that can be parsed into or processed according to the JSON Schema data model can be interpreted against a JSON Schema, including media types like [CBOR][RFC8949].
</details>

JSON 文档是一个由 `application/json` 媒体类型描述的信息资源（一串字节数据）。

在 JSON Schema 中，术语 "JSON document"、"JSON text" 和 "JSON value" 是可以互换的，因为它定义了数据模型（data model）。

JSON Schema 只针对 JSON 文档进行定义。然而，任何可以解析为 JSON Schema 数据模型的文档或内存结构都可以根据 JSON Schema 进行解释，包括像 [CBOR][RFC8949] 这样的媒体类型。

### 4.2 实例（Instance）

<details>
  <summary>Expand original text</summary>

A JSON document to which a schema is applied is known as an "instance".

JSON Schema is defined over "application/json" or compatible documents, including media types with the "+json" structured syntax suffix.

Among these, this specification defines the "application/schema-instance+json" media type which defines handling for fragments in the URI.
</details>

应用了一个模式（schema）的 JSON 文档被称为 "实例"。

JSON Schema 是针对 `application/json` 或兼容文档定义的，包括具有 `+json` 结构化语法后缀的媒体类型。

在这些媒体类型中，本规范定义了 `application/schema-instance+json` 媒体类型，它为 URI 中的片段定义了处理规则。

#### 4.2.1 实例数据模型（Instance Data Model）

<details>
  <summary>Expand original text</summary>

JSON Schema interprets documents according to a data model. A JSON value interpreted according to this data model is called an "instance".

An instance has one of six primitive types, and a range of possible values depending on the type:

- null: A JSON "null" value
- boolean: A "true" or "false" value, from the JSON "true" or "false" value
- object: An unordered set of properties mapping a string to an instance, from the JSON "object" value
- array: An ordered list of instances, from the JSON "array" value
- number: An arbitrary-precision, base-10 decimal number value, from the JSON "number" value
- string: A string of Unicode code points, from the JSON "string" value

Whitespace and formatting concerns, including different lexical representations of numbers that are equal within the data model, are thus outside the scope of JSON Schema. JSON Schema vocabularies (Section 8.1) that wish to work with such differences in lexical representations SHOULD define keywords to precisely interpret formatted strings within the data model rather than relying on having the original JSON representation Unicode characters available.

Since an object cannot have two properties with the same key, behavior for a JSON document that tries to define two properties with the same key in a single object is undefined.

Note that JSON Schema vocabularies are free to define their own extended type system. This should not be confused with the core data model types defined here. As an example, "integer" is a reasonable type for a vocabulary to define as a value for a keyword, but the data model makes no distinction between integers and other numbers.
</details>

JSON Schema 根据数据模型解释文档。根据这个数据模型解释的 JSON 值被称为 "实例"。

实例有六种基本类型（primitive types），取决于类型，可以有一系列可能的值：
- `null`：JSON "null" 值
- `boolean`：JSON "true" 或 "false" 值的 "true" 或 "false" 值
- `object`：一个将字符串映射到实例的无序属性集，来自 JSON "object" 值
- `array`：实例的有序列表，来自 JSON "array" 值
- `number`：任意精度、基数为10的十进制数字值，来自 JSON "number" 值
- `string`：Unicode 字符串，来自 JSON "string" 值

因此，空白和格式问题（例如，数据模型中相等的数字的不同词法表示）不在 JSON Schema 的范围内。希望处理词法表示差异的 JSON Schema 词汇表（第 8.1 节）应该定义关键字，以便在数据模型中精确解释格式化字符串，而不是依赖于原始 JSON 表示的 Unicode 字符。

由于对象不能有两个具有相同键的属性，所以在单个对象中尝试定义两个具有相同键的属性的 JSON 文档的行为是未定义的。

请注意，JSON Schema 词汇表可以自由地定义自己的扩展类型系统（type system）。这不应与此处定义的核心数据模型类型混淆。例如，"integer" 是一个合理的类型，词汇表可以将其定义为关键字的值，但数据模型不区分整数和其它数字。

#### 4.2.2 实例相等（Instance Equality）

<details>
  <summary>Expand original text</summary>

Two JSON instances are said to be equal if and only if they are of the same type and have the same value according to the data model. Specifically, this means:

```txt
both are null; or
both are true; or
both are false; or
both are strings, and are the same codepoint-for-codepoint; or
both are numbers, and have the same mathematical value; or
both are arrays, and have an equal value item-for-item; or
both are objects, and each property in one has exactly one property with a key equal to the other's, and that other property has an equal value.
```

Implied in this definition is that arrays must be the same length, objects must have the same number of members, properties in objects are unordered, there is no way to define multiple properties with the same key, and mere formatting differences (indentation, placement of commas, trailing zeros) are insignificant.
</details>

如果两个 JSON 实例具有相同的类型，并根据数据模型具有相同的值，则它们被认为是相等的。具体来说，这意味着：

```txt
两者都是 null；或
两者都是 true；或
两者都是 false；或
两者都是字符串，并且在每个代码点（codepoint）上相同；或
两者都是数字，并具有相同的数学值；或
两者都是数组，并且按项具有相等的值；或
两者都是对象，并且其中一个对象的每个属性都有一个键等于另一个对象的属性，且另一个属性具有相等的值
```

这个定义暗示，数组必须具有相同的长度，对象必须具有相同数量的成员，对象中的属性是无序的，不能定义具有相同键的多个属性，仅格式差异（缩进、逗号位置、尾随零）是无关紧要的。

#### 4.2.3 非 JSON 实例（Non-JSON Instances）

<details>
  <summary>Expand original text</summary>

It is possible to use JSON Schema with a superset of the JSON Schema data model, where an instance may be outside any of the six JSON data types.

In this case, annotations still apply; but most validation keywords will not be useful, as they will always pass or always fail.

A custom vocabulary may define support for a superset of the core data model. The schema itself may only be expressible in this superset; for example, to make use of the "const" keyword.
</details>

可以在 JSON Schema 数据模型的超集（superset）中使用 JSON Schema，其中实例可能超出 JSON 的六种数据类型之外。

在这种情况下，注释仍然适用；但大多数验证关键字可能没有用处，因为它们总是通过或总是失败。

自定义词汇表可以定义对核心数据模型的超集的支持。模式本身可能只能在这个超集中表示；例如，使用 `const` 关键字。

### 4.3. JSON Schema 文档

<details>
  <summary>Expand original text</summary>

A JSON Schema document, or simply a schema, is a JSON document used to describe an instance. A schema can itself be interpreted as an instance, but SHOULD always be given the media type "application/schema+json" rather than "application/schema-instance+json". The "application/schema+json" media type is defined to offer a superset of the fragment identifier syntax and semantics provided by "application/schema-instance+json".

A JSON Schema MUST be an object or a boolean.
</details>

JSON Schema 文档，或简称为模式，是用于描述实例的 JSON 文档。模式本身可以被解释为实例，但应始终使用媒体类型 `application/schema+json` 而不是 `application/schema-instance+json`。`application/schema+json` 媒体类型被定义为提供超过 `application/schema-instance+json` 提供的片段标识符语法和语义的超集。

JSON Schema 必须是一个对象（object）或布尔值（boolean）。

#### 4.3.1. JSON Schema 对象（Objects）和关键字（Keywords）

<details>
  <summary>Expand original text</summary>

Object properties that are applied to the instance are called keywords, or schema keywords. Broadly speaking, keywords fall into one of five categories:

```txt
identifiers: control schema identification through setting a URI for the schema and/or changing how the base URI is determined
assertions: produce a boolean result when applied to an instance
annotations: attach information to an instance for application use
applicators: apply one or more subschemas to a particular location in the instance, and combine or modify their results
reserved locations: do not directly affect results, but reserve a place for a specific purpose to ensure interoperability
```

Keywords may fall into multiple categories, although applicators SHOULD only produce assertion results based on their subschemas' results. They should not define additional constraints independent of their subschemas.

Keywords which are properties within the same schema object are referred to as adjacent keywords.

Extension keywords, meaning those defined outside of this document and its companions, are free to define other behaviors as well.

A JSON Schema MAY contain properties which are not schema keywords. Unknown keywords SHOULD be treated as annotations, where the value of the keyword is the value of the annotation.

An empty schema is a JSON Schema with no properties, or only unknown properties.
</details>

应用于实例的对象属性称为关键字（keywords）或模式关键字（schema keywords）。大致上，关键字可分为五类：

```txt
标识符（identifiers）：通过为模式设置 URI 和/或更改 base URI 的确定方式来控制模式识别
断言（assertions）：在应用于实例时产生布尔结果
注解（annotations）：为应用程序使用附加信息到实例
应用器（applicators）：将一个或多个子模式（subschemas）应用于实例中的特定位置，并组合或修改它们的结果
保留位置（reserved locations）：不直接影响结果，但为确保互操作性保留一个特定目的的位置
```

关键字可能属于多个类别，尽管应用程序应（SHOULD）仅根据其子模式的结果产生断言结果。它们不应定义与其子模式无关的其它约束。

在同一模式对象中的属性的关键字被称为相邻关键字。

扩展关键字，即在本文档及其附件之外定义的关键字，也可以自由定义其它行为。

JSON Schema 可能（MAY）包含非模式关键字的属性。未知关键字应（SHOULD）被视为注解，其中关键字的值是注解的值。

空模式是一个没有属性或只有未知属性的 JSON Schema。

#### 4.3.2. 布尔 JSON Schemas

<details>
  <summary>Expand original text</summary>

The boolean schema values "true" and "false" are trivial schemas that always produce themselves as assertion results, regardless of the instance value. They never produce annotation results.

These boolean schemas exist to clarify schema author intent and facilitate schema processing optimizations. They behave identically to the following schema objects (where "not" is part of the subschema application vocabulary defined in this document).

- true: Always passes validation, as if the empty schema {}
- false: Always fails validation, as if the schema { "not": {} }

While the empty schema object is unambiguous, there are many possible equivalents to the "false" schema. Using the boolean values ensures that the intent is clear to both human readers and implementations.
</details>

布尔模式值 `true` 和 `false` 是始终产生它们自身作为断言结果的简单模式，无论实例值如何。它们从不产生注解结果。

这些布尔模式存在是为了澄清模式作者的意图并促进模式处理优化。它们的行为与以下模式对象完全相同（其中 `not` 是本文档中定义的子模式应用词汇的一部分）。

- `true`：始终通过验证，就像空模式 `{}` 一样
- `false`：始终无法通过验证，就像模式 `{ "not": {} }` 一样

虽然空模式对象是明确的，但与 "false" 模式等价的可能有很多。使用布尔值确保意图对人类阅读者和实现都是清晰的。

#### 4.3.3. 模式词汇表（Schema Vocabularies）

<details>
  <summary>Expand original text</summary>

A schema vocabulary, or simply a vocabulary, is a set of keywords, their syntax, and their semantics. A vocabulary is generally organized around a particular purpose. Different uses of JSON Schema, such as validation, hypermedia, or user interface generation, will involve different sets of vocabularies.

Vocabularies are the primary unit of re-use in JSON Schema, as schema authors can indicate what vocabularies are required or optional in order to process the schema. Since vocabularies are identified by URIs in the meta-schema, generic implementations can load extensions to support previously unknown vocabularies. While keywords can be supported outside of any vocabulary, there is no analogous mechanism to indicate individual keyword usage.

A schema vocabulary can be defined by anything from an informal description to a standards proposal, depending on the audience and interoperability expectations. In particular, in order to facilitate vocabulary use within non-public organizations, a vocabulary specification need not be published outside of its scope of use.
</details>

模式词汇表，或简称为词汇表，是一组关键字、它们的语法和语义。词汇表通常围绕特定目的组织。JSON Schema 的不同用途，如验证、超媒体或用户界面生成，将涉及不同的词汇表集合。

词汇表是 JSON Schema 中主要的可重用单元，因为模式作者可以指示处理模式所需或可选的词汇表。由于元模式（meta-schema）中的词汇表由 URI 标识，通用实现可以加载扩展以支持以前未知的词汇表。虽然关键字可以在任何词汇表之外得到支持，但没有类似的机制来指示单个关键字的使用。

模式词汇表可以由从非正式描述到标准提案的任何内容定义，具体取决于受众和互操作性（interoperability）期望。特别是为了促进非公共组织（non-public organizations）内的词汇表使用，词汇表规范不需要在其使用范围之外发布。

#### 4.3.4. 元模式（Meta-Schemas）

<details>
  <summary>Expand original text</summary>

A schema that itself describes a schema is called a meta-schema. Meta-schemas are used to validate JSON Schemas and specify which vocabularies they are using.

Typically, a meta-schema will specify a set of vocabularies, and validate schemas that conform to the syntax of those vocabularies. However, meta-schemas and vocabularies are separate in order to allow meta-schemas to validate schema conformance more strictly or more loosely than the vocabularies' specifications call for. Meta-schemas may also describe and validate additional keywords that are not part of a formal vocabulary.
</details>

用来描述模式的模式被称为元模式。元模式用于验证 JSON Schema 并指定它们正在使用的词汇表。

通常，元模式会指定一组词汇表，并验证符合这些词汇表语法的模式。然而，元模式和词汇表是分开的，以便允许元模式比词汇表的规范要求更严格或更宽松地验证模式一致性。元模式还可以描述和验证不属于正式词汇表的额外关键字。

#### 4.3.5. 根模式、子模式和资源（Root Schema and Subschemas and Resources）

<details>
  <summary>Expand original text</summary>

A JSON Schema resource is a schema which is canonically [RFC 6596][RFC6596] identified by an absolute [RFC 3986][RFC3986]. Schema resources MAY also be identified by URIs, including URIs with fragments, if the resulting secondary resource (as defined by section 3.5 of [RFC 3986][RFC3986]) is identical to the primary resource. This can occur with the empty fragment, or when one schema resource is embedded in another. Any such URIs with fragments are considered to be non-canonical.

The root schema is the schema that comprises the entire JSON document in question. The root schema is always a schema resource, where the URI is determined as described in section 9.1.1. Note that documents that embed schemas in another format will not have a root schema resource in this sense. Exactly how such usages fit with the JSON Schema document and resource concepts will be clarified in a future draft.

Some keywords take schemas themselves, allowing JSON Schemas to be nested:

```json
{
    "title": "root",
    "items": {
        "title": "array item"
    }
}
```

In this example document, the schema titled "array item" is a subschema, and the schema titled "root" is the root schema.

As with the root schema, a subschema is either an object or a boolean.

As discussed in section 8.2.1, a JSON Schema document can contain multiple JSON Schema resources. When used without qualification, the term "root schema" refers to the document's root schema. In some cases, resource root schemas are discussed. A resource's root schema is its top-level schema object, which would also be a document root schema if the resource were to be extracted to a standalone JSON Schema document.

Whether multiple schema resources are embedded or linked with a reference, they are processed in the same way, with the same available behaviors.
</details>

JSON Schema 资源是由绝对 URI [RFC 3986][RFC3986] 规范化 [RFC 6596][RFC6596] 标识的模式。模式资源还可以通过 URI 标识，包括带有片段的 URI，如果由此产生的辅助资源（如 [RFC 3986][RFC3986] 第 3.5 节定义的）与主资源相同。这可以发生在空片段中，或者当一个模式资源嵌入到另一个模式资源中。任何这样的带有片段的 URI 都被认为是非规范的。

根模式（root schema）是组成整个 JSON 文档的模式。根模式始终是一个模式资源（schema resource），URI 是按照第 9.1.1 节的描述确定的。请注意，将模式嵌入到其它格式的文档在这个意义上不会有一个根模式资源。这种用法与 JSON Schema 文档和资源概念如何适应将在未来的草案中得到澄清。

某些关键字本身采用模式，允许 JSON Schema 嵌套：

```json
{
    "title": "root",
    "items": {
        "title": "array item"
    }
}
```

在这个示例文档中，标题为 "array item" 的模式是一个子模式，标题为 "root" 的模式是根模式。

与根模式一样，子模式可以是对象或布尔值。

如第 8.2.1 节所讨论的，一个 JSON Schema 文档可以包含多个 JSON Schema 资源。在未经限定的情况下使用术语“根模式”是指文档的根模式。在某些情况下，讨论了资源的根模式。资源的根模式是其顶级模式对象，如果资源被提取到一个独立的 JSON Schema 文档中，它也将成为一个文档根模式。

无论是嵌入多个模式资源还是通过引用链接它们，它们都以相同的方式进行处理，具有相同的可用行为。

## 5. 片段标识符（Fragment Identifiers）

<details>
  <summary>Expand original text</summary>

In accordance with section 3.1 of [RFC 6839][RFC6839], the syntax and semantics of fragment identifiers specified for any +json media type SHOULD be as specified for "application/json". (At publication of this document, there is no fragment identification syntax defined for "application/json".)

Additionally, the "application/schema+json" media type supports two fragment identifier structures: plain names and JSON Pointers. The "application/schema-instance+json" media type supports one fragment identifier structure: JSON Pointers.

The use of JSON Pointers as URI fragment identifiers is described in [RFC 6901][RFC6901]. For "application/schema+json", which supports two fragment identifier syntaxes, fragment identifiers matching the JSON Pointer syntax, including the empty string, MUST be interpreted as JSON Pointer fragment identifiers.

Per the W3C's best practices for fragment identifiers [W3C.WD-fragid-best-practices-20121025][W3C.WD-fragid-best-practices-20121025], plain name fragment identifiers in "application/schema+json" are reserved for referencing locally named schemas. All fragment identifiers that do not match the JSON Pointer syntax MUST be interpreted as plain name fragment identifiers.

Defining and referencing a plain name fragment identifier within an "application/schema+json" document are specified in the "$anchor" keyword (Section 8.2.2) section.
</details>

根据 [RFC 6839][RFC6839] 第 3.1 节的规定，任何 `+json` 媒体类型的片段标识符的语法和语义应与 `application/json` 的规定相同。（在本文档发布时，尚未为 `application/json` 定义片段标识符语法。）
此外，`application/schema+json` 媒体类型支持两种片段标识符结构：纯名称（plain names）和 JSON 指针（JSON Pointers）。`application/schema-instance+json` 媒体类型支持一种片段标识符结构：JSON 指针。

在 [RFC 6901][RFC6901] 中描述了将 JSON 指针用作 URI 片段标识符。对于 `application/schema+json`，它支持两种片段标识符语法，匹配 JSON 指针语法的片段标识符（包括空字符串）必须（MUST）被解释为 JSON 指针片段标识符。

根据 W3C 关于片段标识符的最佳实践 [W3C.WD-fragid-best-practices-20121025][W3C.WD-fragid-best-practices-20121025]，`application/schema+json` 中的纯名称片段标识符保留用于引用本地命名的模式。所有不符合 JSON 指针语法的片段标识符必须被解释为纯名称片段标识符。

在 `application/schema+json` 文档中定义和引用纯名称片段标识符的规定在 `$anchor` 关键字（第 8.2.2 节）部分。

## 6. 一般考虑因素（General Considerations）
### 6.1. JSON 值的范围（Range of JSON Values）

<details>
  <summary>Expand original text</summary>

An instance may be any valid JSON value as defined by JSON [RFC 8259][RFC8259]. JSON Schema imposes no restrictions on type: JSON Schema can describe any JSON value, including, for example, null.
</details>

实例可以是由 JSON [RFC 8259][RFC8259] 定义的任何有效 JSON 值。JSON Schema 对类型没有限制：JSON Schema 可以描述任何 JSON 值，包括例如 `null`。

### 6.2. 编程语言独立性（Programming Language Independence）

<details>
  <summary>Expand original text</summary>

JSON Schema is programming language agnostic, and supports the full range of values described in the data model. Be aware, however, that some languages and JSON parsers may not be able to represent in memory the full range of values describable by JSON.
</details>

JSON Schema 与编程语言无关，并支持数据模型中描述的全部值范围。但请注意，某些语言和 JSON 解析器可能无法在内存中表示 JSON 可描述的全部值范围。

### 6.3. 数学整数（Mathematical Integers）

<details>
  <summary>Expand original text</summary>

Some programming languages and parsers use different internal representations for floating point numbers than they do for integers.

For consistency, integer JSON numbers SHOULD NOT be encoded with a fractional part.
</details>

某些编程语言和解析器对浮点数的内部表示与整数的内部表示不同。

为了保持一致性，整数 JSON 数字不应（SHOULD NOT）编码为带有小数部分的数字。

### 6.4. 正则表达式（Regular Expressions）

<details>
  <summary>Expand original text</summary>

Keywords MAY use regular expressions to express constraints, or constrain the instance value to be a regular expression. These regular expressions SHOULD be valid according to the regular expression dialect described in [ECMA-262][ecma262], section 21.2.1.

Regular expressions SHOULD be built with the "u" flag (or equivalent) to provide Unicode support, or processed in such a way which provides Unicode support as defined by ECMA-262.

Furthermore, given the high disparity in regular expression constructs support, schema authors SHOULD limit themselves to the following regular expression tokens:

```txt
individual Unicode characters, as defined by the JSON specification RFC 8259;
simple character classes ([abc]), range character classes ([a-z]);
complemented character classes ([^abc], [^a-z]);
simple quantifiers: "+" (one or more), "*" (zero or more), "?" (zero or one), and their lazy versions ("+?", "*?", "??");
range quantifiers: "{x}" (exactly x occurrences), "{x,y}" (at least x, at most y, occurrences), {x,} (x occurrences or more), and their lazy versions;
the beginning-of-input ("^") and end-of-input ("$") anchors;
simple grouping ("(...)") and alternation ("|").
```

Finally, implementations MUST NOT take regular expressions to be anchored, neither at the beginning nor at the end. This means, for instance, the pattern "es" matches "expression".
</details>

关键字可以（MAY）使用正则表达式来表示约束，或将实例值约束为正则表达式。这些正则表达式应根据 [ECMA-262][ecma262] 第 21.2.1 节中描述的正则表达式方言有效。

构建正则表达式时，应（SHOULD）使用 `u` 标志（或等效功能）提供 Unicode 支持，或以 ECMA-262 定义的提供 Unicode 支持的方式处理。

此外，考虑到正则表达式结构支持的高差异性，模式作者应（SHOULD）将自己限制在以下正则表达式标记：

- 由 JSON 规范 [RFC 8259][RFC8259] 定义的单个 Unicode 字符；
- 简单字符类（`[abc]`）、范围字符类（`[a-z]`）；
- 补充字符类（`[^abc]`，`[^a-z]`）；
- 简单量词：`+`（一个或多个），`*`（零个或多个），`?`（零个或一个），以及它们的懒惰版本（`+?`，`?`，`??`）；
- 范围量词：`{x}`（恰好 x 次出现），`{x,y}`（至少 x 次，最多 y 次出现），`{x,}`（x 次或更多出现），以及它们的懒惰版本；
- 输入开始（`^`）和输入结束（`$`）锚点；
- 简单分组（`(...)`）和交替（`|`）。

最后，实现必须不（MUST NOT）将正则表达式视为锚定在开头或结尾。这意味着，例如，模式 `es` 能匹配 "expression"。

### 6.5. 扩展 JSON Schema（Extending JSON Schema）

<details>
  <summary>Expand original text</summary>

Additional schema keywords and schema vocabularies MAY be defined by any entity. Save for explicit agreement, schema authors SHALL NOT expect these additional keywords and vocabularies to be supported by implementations that do not explicitly document such support. Implementations SHOULD treat keywords they do not support as annotations, where the value of the keyword is the value of the annotation.

Implementations MAY provide the ability to register or load handlers for vocabularies that they do not support directly. The exact mechanism for registering and implementing such handlers is implementation-dependent.
</details>

任何实体都可以（MAY）定义额外的模式关键字和模式词汇表。除非有明确的协议，模式作者不应（SHALL NOT）期望这些额外的关键字和词汇表被不明确记录此类支持的实现支持。实现应（SHOULD）将其不支持的关键字视为注解，其中关键字的值是注解的值。

实现可以（MAY）提供注册或加载它们不直接支持的词汇表的处理器的能力。注册和实现此类处理器的确切机制取决于实现。

## 7. 关键字行为（Keyword Behaviors）

<details>
  <summary>Expand original text</summary>

JSON Schema keywords fall into several general behavior categories. Assertions validate that an instance satisfies constraints, producing a boolean result. Annotations attach information that applications may use in any way they see fit. Applicators apply subschemas to parts of the instance and combine their results.

Extension keywords SHOULD stay within these categories, keeping in mind that annotations in particular are extremely flexible. Complex behavior is usually better delegated to applications on the basis of annotation data than implemented directly as schema keywords. However, extension keywords MAY define other behaviors for specialized purposes.

Evaluating an instance against a schema involves processing all of the keywords in the schema against the appropriate locations within the instance. Typically, applicator keywords are processed until a schema object with no applicators (and therefore no subschemas) is reached. The appropriate location in the instance is evaluated against the assertion and annotation keywords in the schema object, and their results are gathered into the parent schema according to the rules of the applicator.

Evaluation of a parent schema object can complete once all of its subschemas have been evaluated, although in some circumstances evaluation may be short-circuited due to assertion results. When annotations are being collected, some assertion result short-circuiting is not possible due to the need to examine all subschemas for annotation collection, including those that cannot further change the assertion result.
</details>

JSON Schema 关键字分为几个一般行为类别。断言（Assertions）验证实例是否满足约束，产生布尔结果。注释（Annotations）附加应用程序可以自由使用的信息。应用器（Applicators）将子模式应用到实例的部分并合并它们的结果。

扩展关键字应该（SHOULD）保持在这些类别中，特别要注意注释具有极大的灵活性。将复杂行为委托给基于注释数据的应用程序通常比直接作为模式关键字实现更好。然而，扩展关键字可以为特殊目的定义其它行为。

根据模式评估（evaluate）实例涉及将模式中的所有关键字处理到实例内的适当位置。通常，处理应用器关键字，直到没有应用器（因此没有子模式）的模式对象为止。根据应用器的规则，将实例的适当位置根据模式对象中的断言和注释关键字进行评估，将结果汇集到父模式中。

一旦评估了所有子模式，父模式对象的评估就可以完成，尽管在某些情况下，由于断言结果，评估可能会被短路。当收集注释时，由于需要检查所有子模式以收集注释，包括那些无法进一步更改断言结果的子模式，因此无法进行某些断言结果短路。

### 7.1. 词汇范围和动态范围（Lexical Scope and Dynamic Scope）

<details>
  <summary>Expand original text</summary>

While most JSON Schema keywords can be evaluated on their own, or at most need to take into account the values or results of adjacent keywords in the same schema object, a few have more complex behavior.

The lexical scope of a keyword is determined by the nested JSON data structure of objects and arrays. The largest such scope is an entire schema document. The smallest scope is a single schema object with no subschemas.

Keywords MAY be defined with a partial value, such as a URI-reference, which must be resolved against another value, such as another URI-reference or a full URI, which is found through the lexical structure of the JSON document. The "$id", "$ref", and "$dynamicRef" core keywords, and the "base" JSON Hyper-Schema keyword, are examples of this sort of behavior.

Note that some keywords, such as "$schema", apply to the lexical scope of the entire schema resource, and therefore MUST only appear in a schema resource's root schema.

Other keywords may take into account the dynamic scope that exists during the evaluation of a schema, typically together with an instance document. The outermost dynamic scope is the schema object at which processing begins, even if it is not a schema resource root. The path from this root schema to any particular keyword (that includes any "$ref" and "$dynamicRef" keywords that may have been resolved) is considered the keyword's "validation path."

Lexical and dynamic scopes align until a reference keyword is encountered. While following the reference keyword moves processing from one lexical scope into a different one, from the perspective of dynamic scope, following a reference is no different from descending into a subschema present as a value. A keyword on the far side of that reference that resolves information through the dynamic scope will consider the originating side of the reference to be their dynamic parent, rather than examining the local lexically enclosing parent.

The concept of dynamic scope is primarily used with "$dynamicRef" and "$dynamicAnchor", and should be considered an advanced feature and used with caution when defining additional keywords. It also appears when reporting errors and collected annotations, as it may be possible to revisit the same lexical scope repeatedly with different dynamic scopes. In such cases, it is important to inform the user of the dynamic path that produced the error or annotation.
</details>

虽然大多数 JSON Schema 关键字可以单独评估，或者最多需要考虑相同模式对象中相邻关键字的值或结果，但有些关键字具有更复杂的行为。

关键字的词汇范围（lexical scope）由对象和数组的嵌套 JSON 数据结构确定。最大的范围是整个模式文档。最小范围是没有子模式的单个模式对象。

关键字可以（MAY）通过部分值（如 URI 引用）进行定义，这些值必须解析为通过 JSON 文档的词汇结构找到的另一个值（如另一个 URI 引用或完整的 URI）。核心关键字 `$id`、`$ref` 和 `$dynamicRef` 以及JSON 超模式（Hyper-Schema）关键字 `base` 就是此类行为的例子。

请注意，某些关键字（如 `$schema`）适用于整个模式资源的词汇范围，因此必须仅出现在模式资源的根模式中。

其它关键字可能会考虑在模式评估期间存在的动态范围（dynamic scope），通常与实例文档一起。最外层的动态范围是处理开始的模式对象，即使它不是模式资源的根。从根模式到任何特定关键字（包括可能已解析的任何 `$ref` 和 `$dynamicRef` 关键字）的路径被认为是关键字的验证路径（validation path）。

词汇范围和动态范围在遇到引用关键字之前是一致的。尽管遵循引用关键字会将处理从一个词汇范围移动到另一个词汇范围，但从动态范围的角度来看，遵循引用与降入（descending into）作为值存在的子模式没有区别。在引用的另一侧的关键字通过动态范围解析信息时，将考虑引用的起始侧作为它们的动态父级，而不是检查本地词汇封闭的父级。

动态范围的概念主要用于 `$dynamicRef` 和 `$dynamicAnchor`，在定义其它关键字时应谨慎使用，应将其视为高级功能。在报告错误和收集的注释时，它还会出现，因为在不同的动态范围下可能会重复访问相同的词汇范围。在这种情况下，告知用户产生错误或注释的动态路径非常重要。

### 7.2. 关键字交互（Keyword Interactions）

<details>
  <summary>Expand original text</summary>

Keyword behavior MAY be defined in terms of the annotation results of subschemas (Section 4.3.5) and/or adjacent keywords (keywords within the same schema object) and their subschemas. Such keywords MUST NOT result in a circular dependency. Keywords MAY modify their behavior based on the presence or absence of another keyword in the same schema object (Section 4.3).
</details>

关键字行为可以（MAY）根据子模式（第 4.3.5 节）的注解结果和/或相邻关键字（同一模式对象内的关键字）及其子模式来定义。这样的关键字一定不能（MUST NOT）导致循环依赖。关键字可以（MAY）根据同一模式对象中另一个关键字的存在或不存在来修改它们的行为（第 4.3 节）。

### 7.3. 默认行为（Default Behaviors）

<details>
  <summary>Expand original text</summary>

A missing keyword MUST NOT produce a false assertion result, MUST NOT produce annotation results, and MUST NOT cause any other schema to be evaluated as part of its own behavioral definition. However, given that missing keywords do not contribute annotations, the lack of annotation results may indirectly change the behavior of other keywords.

In some cases, the missing keyword assertion behavior of a keyword is identical to that produced by a certain value, and keyword definitions SHOULD note such values where known. However, even if the value which produces the default behavior would produce annotation results if present, the default behavior still MUST NOT result in annotations.

Because annotation collection can add significant cost in terms of both computation and memory, implementations MAY opt out of this feature. Keywords that are specified in terms of collected annotations SHOULD describe reasonable alternate approaches when appropriate. This approach is demonstrated by the "items" and "additionalProperties" keywords in this document.

Note that when no such alternate approach is possible for a keyword, implementations that do not support annotation collections will not be able to support those keywords or vocabularies that contain them.
</details>

缺少关键字一定不会（MUST NOT）产生错误的断言结果，也不会（MUST NOT）产生注解结果，且不会（MUST NOT）导致任何其它模式作为其自身行为定义的一部分被评估。然而，考虑到缺失关键字不会产生注解，缺少注解结果可能会间接改变其它关键字的行为。

在某些情况下，缺少关键字的断言行为与关键字产生的某个值相同，关键字定义应（SHOULD）在已知情况下注明这样的值。然而，即使产生默认行为的值在存在时会产生注解结果，但默认行为仍然一定不能（MUST NOT）产生注解。

由于注解收集在计算和内存方面可能增加显著成本，实现可以选择不使用此功能。以收集注解为条件的关键字应在适当时描述合理的备选方法。本文档中的 `items` 和 `additionalProperties` 关键字就演示了这种方法。

请注意，对于某个关键字，如果没有其它可行的替代方法，那么不支持注解收集的实现将无法支持包含它们的关键字或词汇表。

### 7.4. 标识符（Identifiers）

<details>
  <summary>Expand original text</summary>

Identifiers define URIs for a schema, or affect how such URIs are resolved in references (Section 8.2.3), or both. The Core vocabulary defined in this document defines several identifying keywords, most notably "$id".

Canonical schema URIs MUST NOT change while processing an instance, but keywords that affect URI-reference resolution MAY have behavior that is only fully determined at runtime.

While custom identifier keywords are possible, vocabulary designers should take care not to disrupt the functioning of core keywords. For example, the "$dynamicAnchor" keyword in this specification limits its URI resolution effects to the matching "$dynamicRef" keyword, leaving the behavior of "$ref" undisturbed.
</details>

标识符为模式定义 URI，或影响引用中如何解析这些 URI（第 8.2.3 节），或两者兼有。本文档定义的核心词汇表定义了几个标识关键字，尤其是 `$id`。

在处理实例时，标准的模式 URI 一定不能（MUST NOT）更改，但影响 URI 引用解析的关键字可能（MAY）在运行时才完全确定其行为。

虽然自定义标识符关键字是可能的，但词汇表设计者应注意不要干扰核心关键字的功能。例如，本规范中的 `$dynamicAnchor` 关键字将其 URI 解析效果限制在匹配的 `$dynamicRef` 关键字上，保持 `$ref` 的行为不受干扰。

### 7.5. 应用器（Applicators）

<details>
  <summary>Expand original text</summary>

Applicators allow for building more complex schemas than can be accomplished with a single schema object. Evaluation of an instance against a schema document (Section 4.3) begins by applying the root schema (Section 4.3.5) to the complete instance document. From there, keywords known as applicators are used to determine which additional schemas are applied. Such schemas may be applied in-place to the current location, or to a child location.

The schemas to be applied may be present as subschemas comprising all or part of the keyword's value. Alternatively, an applicator may refer to a schema elsewhere in the same schema document, or in a different one. The mechanism for identifying such referenced schemas is defined by the keyword.

Applicator keywords also define how subschema or referenced schema boolean assertion (Section 7.6) results are modified and/or combined to produce the boolean result of the applicator. Applicators may apply any boolean logic operation to the assertion results of subschemas, but MUST NOT introduce new assertion conditions of their own.

Annotation (Section 7.7) results are preserved along with the instance location and the location of the schema keyword, so that applications can decide how to interpret multiple values.
</details>

应用器允许构建比单个模式对象更复杂的模式。针对模式文档（第 4.3 节）对实例的评估从将根模式（第 4.3.5 节）应用于完整的实例文档开始。从那里开始，使用称为应用器的关键字来确定应用哪些额外的模式。这些模式可以就地应用于当前位置，也可以应用于子位置。

要应用的模式可以作为子模式出现，包括关键字值的全部或部分。或者，应用器可以引用同一模式文档中的其它地方或不同文档中的模式。识别这些引用模式的机制由关键字定义。

应用器关键字还定义如何修改和/或组合子模式或引用模式的布尔断言（第 7.6 节）结果，以产生应用器的布尔结果。应用器可以将任何布尔逻辑操作应用于子模式的断言结果，但不能（MUST NOT）引入新的断言条件。

注解（第 7.7 节）结果连同实例位置和模式关键字位置一起保留，以便应用程序可以决定如何解释多个值。

#### 7.5.1. 引用和引用模式（Referenced and Referencing Schemas）

<details>
  <summary>Expand original text</summary>

As noted in Section 7.5, an applicator keyword may refer to a schema to be applied, rather than including it as a subschema in the applicator's value. In such situations, the schema being applied is known as the referenced schema, while the schema containing the applicator keyword is the referencing schema.

While root schemas and subschemas are static concepts based on a schema's position within a schema document, referenced and referencing schemas are dynamic. Different pairs of schemas may find themselves in various referenced and referencing arrangements during the evaluation of an instance against a schema.

For some by-reference applicators, such as "$ref" (Section 8.2.3.1), the referenced schema can be determined by static analysis of the schema document's lexical scope. Others, such as "$dynamicRef" (with "$dynamicAnchor"), may make use of dynamic scoping, and therefore only be resolvable in the process of evaluating the schema with an instance.
</details>

如第 7.5 节所述，应用器关键字可以引用要应用的模式，而不是将其作为应用器值中的子模式。在这种情况下，正在应用的模式称为被引用模式（referenced schema），而包含应用器关键字的模式是引用模式（referencing schema）。

虽然根模式和子模式是基于模式在模式文档中的位置的静态概念，但引用和引用模式是动态的。在针对模式评估实例过程中，不同的模式对可能会发现自己处于各种引用和引用安排中。

对于一些按引用应用器，如 `$ref`（第 8.2.3.1 节），可以通过对模式文档的词法范围进行静态分析来确定被引用的模式。其它应用器，如 `$dynamicRef`（与 `$dynamicAnchor`），可能会使用动态作用域，在评估实例的模式过程中才能解析。

### 7.6. 断言（Assertions）

<details>
  <summary>Expand original text</summary>

JSON Schema can be used to assert constraints on a JSON document, which either passes or fails the assertions. This approach can be used to validate conformance with the constraints, or document what is needed to satisfy them.

JSON Schema implementations produce a single boolean result when evaluating an instance against schema assertions.

An instance can only fail an assertion that is present in the schema.
</details>

JSON Schema 可以用于对 JSON 文档进行断言约束，该文档可以通过或不通过断言。这种方法可用于验证是否符合约束条件，或记录满足约束条件所需的内容。

在针对模式断言评估实例时，JSON Schema 实现产生一个布尔结果。

实例只能在模式中存在的断言上失败。

#### 7.6.1. 断言和实例基本类型（Assertions and Instance Primitive Types）

<details>
  <summary>Expand original text</summary>

Most assertions only constrain values within a certain primitive type. When the type of the instance is not of the type targeted by the keyword, the instance is considered to conform to the assertion.

For example, the "maxLength" keyword from the companion validation vocabulary [json-schema-validation][json-schema-validation]: will only restrict certain strings (that are too long) from being valid. If the instance is a number, boolean, null, array, or object, then it is valid against this assertion.

This behavior allows keywords to be used more easily with instances that can be of multiple primitive types. The companion validation vocabulary also includes a "type" keyword which can independently restrict the instance to one or more primitive types. This allows for a concise expression of use cases such as a function that might return either a string of a certain length or a null value:

```json
{
    "type": ["string", "null"],
    "maxLength": 255
}
```

If "maxLength" also restricted the instance type to be a string, then this would be substantially more cumbersome to express because the example as written would not actually allow null values. Each keyword is evaluated separately unless explicitly specified otherwise, so if "maxLength" restricted the instance to strings, then including "null" in "type" would not have any useful effect.
</details>

大多数断言仅限制某一基本类型内的值。当实例的类型不是关键字针对的类型时，实例被认为符合断言。

例如，伴随验证词汇表 [json-schema-validation][json-schema-validation] 中的 `maxLength` 关键字：只会限制某些字符串（过长的字符串）的有效性。如果实例是数字、布尔值、空值、数组或对象，那么它将符合此断言。

这种行为使得关键字更容易与可以是多种基本类型的实例一起使用。伴随验证词汇表还包括一个 `type` 关键字，可以独立地将实例限制为一个或多个基本类型。这允许简洁地表示诸如函数可能返回某个长度的字符串或空值的用例：

```json
{
    "type": ["string", "null"],
    "maxLength": 255
}
```

如果 `maxLength` 也将实例类型限制为字符串，那么这将更加繁琐，因为书写的示例实际上不允许空值。除非另有明确规定，否则每个关键字都将单独评估，因此如果 `maxLength` 将实例限制为字符串，那么在 `type` 中包含 "null" 将没有任何实际效果。

### 7.7. 注解（Annotations）

<details>
  <summary>Expand original text</summary>

JSON Schema can annotate an instance with information, whenever the instance validates against the schema object containing the annotation, and all of its parent schema objects. The information can be a simple value, or can be calculated based on the instance contents.

Annotations are attached to specific locations in an instance. Since many subschemas can be applied to any single location, applications may need to decide how to handle differing annotation values being attached to the same instance location by the same schema keyword in different schema objects.

Unlike assertion results, annotation data can take a wide variety of forms, which are provided to applications to use as they see fit. JSON Schema implementations are not expected to make use of the collected information on behalf of applications.

Unless otherwise specified, the value of an annotation keyword is the keyword's value. However, other behaviors are possible. For example, JSON Hyper-Schema's [json-hyper-schema][json-hyper-schema] "links" keyword is a complex annotation that produces a value based in part on the instance data.

While "short-circuit" evaluation is possible for assertions, collecting annotations requires examining all schemas that apply to an instance location, even if they cannot change the overall assertion result. The only exception is that subschemas of a schema object that has failed validation MAY be skipped, as annotations are not retained for failing schemas.
</details>

JSON Schema 可以在实例验证通过包含注解的模式对象及其所有父模式对象时，为实例附加信息。信息可以是一个简单值，也可以基于实例内容计算得出。

注解附加到实例的特定位置。由于许多子模式可以应用于单个位置，应用程序可能需要决定如何处理由不同模式对象中的相同模式关键字附加到相同实例位置的不同注解值。

与断言结果不同，注解数据可以采用各种形式，供应用程序根据需要使用。不需要 JSON Schema 实现代表应用程序使用收集到的信息。

除非另有规定，注解关键字的值就是关键字的值。然而，其它行为也是可能的。例如，JSON Hyper-Schema [json-hyper-schema][json-hyper-schema] 的 `links` 关键字是一个复杂注解，其值部分基于实例数据。

尽管断言可以进行“短路”求值，但收集注解需要检查适用于实例位置的所有模式，即使它们无法改变整体断言结果。唯一的例外是，可以跳过未通过验证的模式对象的子模式，因为未通过验证的模式不保留注解。

#### 7.7.1. 收集注解（Collecting Annotations）

<details>
  <summary>Expand original text</summary>

Annotations are collected by keywords that explicitly define annotation-collecting behavior. Note that boolean schemas cannot produce annotations as they do not make use of keywords.

A collected annotation MUST include the following information:

- The name of the keyword that produces the annotation
- The instance location to which it is attached, as a JSON Pointer
- The schema location path, indicating how reference keywords such as "$ref" were followed to reach the absolute schema location.
- The absolute schema location of the attaching keyword, as a URI. This MAY be omitted if it is the same as the schema location path from above.
- The attached value(s)

</details>

注解由明确定义注解收集行为的关键字收集。请注意，布尔模式无法生成注解，因为它们不使用关键字。

收集到的注解必须（MUST）包括以下信息：

- 产生注解的关键字的名称
- 附加的实例位置，作为 JSON Pointer
- 模式位置路径，表示如何跟随诸如 `$ref` 之类的引用关键字以到达绝对模式位置
- 附加关键字的绝对模式位置，作为 URI。如果它与上面的模式位置路径相同，则可以省略
- 附加的值

##### 7.7.1.1. 区分多个值（Distinguishing Among Multiple Values）

<details>
  <summary>Expand original text</summary>

Applications MAY make decisions on which of multiple annotation values to use based on the schema location that contributed the value. This is intended to allow flexible usage. Collecting the schema location facilitates such usage.

For example, consider this schema, which uses annotations and assertions from the Validation specification [json-schema-validation][json-schema-validation]:

Note that some lines are wrapped for clarity.

```json
{
    "title": "Feature list",
    "type": "array",
    "prefixItems": [
        {
            "title": "Feature A",
            "properties": {
                "enabled": {
                    "$ref": "#/$defs/enabledToggle",
                    "default": true
                }
            }
        },
        {
            "title": "Feature B",
            "properties": {
                "enabled": {
                    "description": "If set to null, Feature B
                                    inherits the enabled
                                    value from Feature A",
                    "$ref": "#/$defs/enabledToggle"
                }
            }
        }
    ],
    "$defs": {
        "enabledToggle": {
            "title": "Enabled",
            "description": "Whether the feature is enabled (true),
                            disabled (false), or under
                            automatic control (null)",
            "type": ["boolean", "null"],
            "default": null
        }
    }
}
```

In this example, both Feature A and Feature B make use of the re-usable "enabledToggle" schema. That schema uses the "title", "description", and "default" annotations. Therefore the application has to decide how to handle the additional "default" value for Feature A, and the additional "description" value for Feature B.

The application programmer and the schema author need to agree on the usage. For this example, let's assume that they agree that the most specific "default" value will be used, and any additional, more generic "default" values will be silently ignored. Let's also assume that they agree that all "description" text is to be used, starting with the most generic, and ending with the most specific. This requires the schema author to write descriptions that work when combined in this way.

The application can use the schema location path to determine which values are which. The values in the feature's immediate "enabled" property schema are more specific, while the values under the re-usable schema that is referenced to with "$ref" are more generic. The schema location path will show whether each value was found by crossing a "$ref" or not.

Feature A will therefore use a default value of true, while Feature B will use the generic default value of null. Feature A will only have the generic description from the "enabledToggle" schema, while Feature B will use that description, and also append its locally defined description that explains how to interpret a null value.

Note that there are other reasonable approaches that a different application might take. For example, an application may consider the presence of two different values for "default" to be an error, regardless of their schema locations.
</details>

应用程序可以（MAY）根据提供值的模式位置来决定使用多个注解值中的哪一个。这旨在允许灵活使用。收集模式位置有助于这种使用。

例如，考虑这个模式，它使用了来自验证规范 [json-schema-validation][json-schema-validation] 的注解和断言：

请注意，为了清晰起见，某些行已换行。

```json
{
    "title": "Feature list",
    "type": "array",
    "prefixItems": [
        {
            "title": "Feature A",
            "properties": {
                "enabled": {
                    "$ref": "#/$defs/enabledToggle",
                    "default": true
                }
            }
        },
        {
            "title": "Feature B",
            "properties": {
                "enabled": {
                    "description": "If set to null, Feature B
                                    inherits the enabled
                                    value from Feature A",
                    "$ref": "#/$defs/enabledToggle"
                }
            }
        }
    ],
    "$defs": {
        "enabledToggle": {
            "title": "Enabled",
            "description": "Whether the feature is enabled (true),
                            disabled (false), or under
                            automatic control (null)",
            "type": ["boolean", "null"],
            "default": null
        }
    }
}
```

在此示例中，功能 A 和功能 B 都使用了可重用的 `enabledToggle` 模式。该模式使用 `title`、`description` 和 `default` 注解。因此，应用程序必须决定如何处理功能 A 的附加 `default` 值和功能 B 的附加 `description` 值。

应用程序编程者和模式作者需要就用法达成一致。对于这个例子，假设他们同意使用最具体的 `default` 值，而其它更通用的 `default` 值将被静默地忽略。还假设他们同意使用所有的 `description` 文本，从最通用的开始，到最具体的结束。这要求模式作者编写在这种方式下组合时可行的描述。

应用程序可以使用模式位置路径来确定哪些值是哪些。位于功能的直接 `enabled` 属性模式中的值更具体，而通过 `$ref` 引用的可重用模式中的值更通用。模式位置路径将显示每个值是通过跨越 `$ref` 找到的还是没有跨越。

因此，功能 A 将使用默认值 true，而功能 B 将使用通用默认值 null。功能 A 将仅具有来自 `enabledToggle` 模式的通用描述，而功能 B 将使用该描述，并附加其本地定义的描述，解释如何解释 null 值。

请注意，不同的应用程序可能采用其它合理的方法。例如，应用程序可能认为两个不同的 `default` 值是错误的，而不考虑它们的模式位置。

##### 7.7.1.2. 注释和断言（Annotations and Assertions）

<details>
  <summary>Expand original text</summary>

Schema objects that produce a false assertion result MUST NOT produce any annotation results, whether from their own keywords or from keywords in subschemas.

Note that the overall schema results may still include annotations collected from other schema locations. Given this schema:

```json
{
    "oneOf": [
        {
            "title": "Integer Value",
            "type": "integer"
        },
        {
            "title": "String Value",
            "type": "string"
        }
    ]
}
```

Against the instance "This is a string", the title annotation "Integer Value" is discarded because the type assertion in that schema object fails. The title annotation "String Value" is kept, as the instance passes the string type assertions.
</details>

产生错误断言结果的模式对象必须不（MUST NOT）产生任何注释结果，无论是来自它们自己的关键字还是子模式中的关键字。

请注意，总体模式结果可能仍然包括从其它模式位置收集的注释。给定这个模式：

```json
{
    "oneOf": [
        {
            "title": "Integer Value",
            "type": "integer"
        },
        {
            "title": "String Value",
            "type": "string"
        }
    ]
}
```

针对实例 "This is a string"，标题注释 "Integer Value" 被丢弃，因为该模式对象中的类型断言失败。保留标题注释 "String Value"，因为实例通过了字符串类型断言。

##### 7.7.1.3. 注释和应用器（Annotations and Applicators）

<details>
  <summary>Expand original text</summary>

In addition to possibly defining annotation results of their own, applicator keywords aggregate the annotations collected in their subschema(s) or referenced schema(s).
</details>

除了可能定义自己的注释结果外，应用器关键字还会聚合它们的子模式或引用模式中收集的注释。

### 7.8. 保留位置（Reserved Locations）

<details>
  <summary>Expand original text</summary>

A fourth category of keywords simply reserve a location to hold re-usable components or data of interest to schema authors that is not suitable for re-use. These keywords do not affect validation or annotation results. Their purpose in the core vocabulary is to ensure that locations are available for certain purposes and will not be redefined by extension keywords.

While these keywords do not directly affect results, as explained in section 9.4.2 unrecognized extension keywords that reserve locations for re-usable schemas may have undesirable interactions with references in certain circumstances.
</details>

第四类关键字简单地保留位置以保存可重用组件或模式作者感兴趣的数据，这些数据不适合重用。这些关键字不影响验证或注释结果。它们在核心词汇中的目的是确保某些用途的位置可用，并且不会被扩展关键字重新定义。

虽然这些关键字不直接影响结果，但如第 9.4.2 节所述，保留可重用模式位置的无法识别的扩展关键字在某些情况下可能与引用产生不良的相互作用。

### 7.9. 加载实例数据（Loading Instance Data）

<details>
  <summary>Expand original text</summary>

While none of the vocabularies defined as part of this or the associated documents define a keyword which may target and/or load instance data, it is possible that other vocabularies may wish to do so.

Keywords MAY be defined to use JSON Pointers or Relative JSON Pointers to examine parts of an instance outside the current evaluation location.

Keywords that allow adjusting the location using a Relative JSON Pointer SHOULD default to using the current location if a default is desireable.
</details>

虽然这个或相关文档中定义的词汇没有定义可能定位和/或加载实例数据的关键字，但其它词汇可能希望这样做。

关键字可以（MAY）定义为使用 JSON 指针或相对 JSON 指针查看当前评估位置之外的实例部分。

允许使用相对 JSON 指针调整位置的关键字应该默认使用当前位置（如果需要默认值的话）。

## 8. JSON Schema Core 词汇表（The JSON Schema Core Vocabulary）

<details>
  <summary>Expand original text</summary>

Keywords declared in this section, which all begin with "$", make up the JSON Schema Core vocabulary. These keywords are either required in order to process any schema or meta-schema, including those split across multiple documents, or exist to reserve keywords for purposes that require guaranteed interoperability.

The Core vocabulary MUST be considered mandatory at all times, in order to bootstrap the processing of further vocabularies. Meta-schemas that use the "$vocabulary" (Section 8.1) keyword to declare the vocabularies in use MUST explicitly list the Core vocabulary, which MUST have a value of true indicating that it is required.

The behavior of a false value for this vocabulary (and only this vocabulary) is undefined, as is the behavior when "$vocabulary" is present but the Core vocabulary is not included. However, it is RECOMMENDED that implementations detect these cases and raise an error when they occur. It is not meaningful to declare that a meta-schema optionally uses Core.

Meta-schemas that do not use "$vocabulary" MUST be considered to require the Core vocabulary as if its URI were present with a value of true.

The current URI for the Core vocabulary is: https://json-schema.org/draft/2020-12/vocab/core.

The current URI for the corresponding meta-schema is: https://json-schema.org/draft/2020-12/meta/core.

While the "$" prefix is not formally reserved for the Core vocabulary, it is RECOMMENDED that extension keywords (in vocabularies or otherwise) begin with a character other than "$" to avoid possible future collisions.
</details>

本节中声明的以 `$` 开头的关键字组成了 JSON Schema Core 词汇表。这些关键字要么是处理任何模式或元模式（包括那些分布在多个文档中的模式）所必需的，要么是为需要确保互操作性的目的保留关键字。

在任何时候，都必须（MUST）将 Core 词汇表视为强制性的，以便启动其它词汇表的处理。使用 `$vocabulary`（第 8.1 节）关键字声明正在使用的词汇表的元模式必须（MUST）明确列出 Core 词汇表，该词汇表必须（MUST）具有表示其为必需的 true 值。

此词汇表（仅此词汇表）的 false 值的行为是未定义的，`$vocabulary` 存在但未包含 Core 词汇表时的行为也是如此。然而，建议（RECOMMENDED）实现检测这些情况，并在发生时引发错误。声明元模式可选使用 Core 是没有意义的。

不使用 `$vocabulary` 的元模式必须（MUST）被视为需要 Core 词汇表，就像其 URI 存在并具有 true 值一样。

Core 词汇表的当前 URI 是：https://json-schema.org/draft/2020-12/vocab/core。

相应元模式的当前 URI 是：https://json-schema.org/draft/2020-12/meta/core。

虽然 `$` 前缀并未正式保留给 Core 词汇表，但建议（RECOMMENDED）扩展关键字（在词汇表或其它地方）以 `$` 之外的其它字符开头，以避免将来可能发生的冲突。

### 8.1. 元模式和词汇表（Meta-Schemas and Vocabularies）

<details>
  <summary>Expand original text</summary>

Two concepts, meta-schemas and vocabularies, are used to inform an implementation how to interpret a schema. Every schema has a meta-schema, which can be declared using the "$schema" keyword.

The meta-schema serves two purposes:

- Declaring the vocabularies in use:
The "$vocabulary" keyword, when it appears in a meta-schema, declares which vocabularies are available to be used in schemas that refer to that meta-schema. Vocabularies define keyword semantics, as well as their general syntax.

- Describing valid schema syntax:
A schema MUST successfully validate against its meta-schema, which constrains the syntax of the available keywords. The syntax described is expected to be compatible with the vocabularies declared; while it is possible to describe an incompatible syntax, such a meta-schema would be unlikely to be useful.

Meta-schemas are separate from vocabularies to allow for vocabularies to be combined in different ways, and for meta-schema authors to impose additional constraints such as forbidding certain keywords, or performing unusually strict syntactical validation, as might be done during a development and testing cycle. Each vocabulary typically identifies a meta-schema consisting only of the vocabulary's keywords.

Meta-schema authoring is an advanced usage of JSON Schema, so the design of meta-schema features emphasizes flexibility over simplicity.
</details>

元模式和词汇表这两个概念用于告知实现如何解释模式。每个模式都有一个元模式，可以使用 `$schema` 关键字声明。

元模式有两个目的：

- 声明正在使用的词汇表：
当出现在元模式中时，`$vocabulary` 关键字声明了可以在引用该元模式的模式中使用的词汇表。词汇表定义关键字语义及其一般语法。
- 描述有效的模式语法：
模式必须（MUST）成功地针对其元模式进行验证，该元模式约束了可用关键字的语法。所描述的语法预期与声明的词汇表兼容；虽然可以描述不兼容的语法，但这样的元模式不太可能有用。

元模式与词汇表分开，是为了允许以不同方式组合词汇表，以及允许元模式作者施加额外约束，例如禁止某些关键字，或执行异常严格的语法验证，这可能在开发和测试周期中完成。每个词汇表通常标识仅包含词汇表关键字的元模式。

元模式编写是 JSON Schema 的高级用法，因此元模式特性的设计强调灵活性而非简单性。

#### 8.1.1. "$schema" 关键字（The "$schema" Keyword）

<details>
  <summary>Expand original text</summary>

The "$schema" keyword is both used as a JSON Schema dialect identifier and as the identifier of a resource which is itself a JSON Schema, which describes the set of valid schemas written for this particular dialect.

The value of this keyword MUST be a URI [RFC 3986][RFC3986] (containing a scheme) and this URI MUST be normalized. The current schema MUST be valid against the meta-schema identified by this URI.

If this URI identifies a retrievable resource, that resource SHOULD be of media type "application/schema+json".

The "$schema" keyword SHOULD be used in the document root schema object, and MAY be used in the root schema objects of embedded schema resources. It MUST NOT appear in non-resource root schema objects. If absent from the document root schema, the resulting behavior is implementation-defined.

Values for this property are defined elsewhere in this and other documents, and by other parties.
</details>

`$schema` 关键字既用作 JSON Schema 方言标识符，也用作资源标识符，该资源本身是一个 JSON Schema，描述了为这种特定方言编写的一组有效模式。

此关键字的值必须（MUST）是 URI [RFC 3986][RFC3986]（包含方案），并且此 URI 必须（MUST）是标准化的。当前模式必须（MUST）根据此 URI 标识的元模式有效。

如果此 URI 标识可检索的资源，则该资源的媒体类型应（SHOULD）为 `application/schema+json`。

`$schema` 关键字应该（SHOULD）在文档根模式对象中使用，并且可以在嵌入式模式资源的根模式对象中使用。它不得（MUST NOT）出现在非资源根模式对象中。如果在文档根模式中缺失，产生的行为是实现定义的（implementation-defined）。

此属性的值在本文档和其它文档中以及其它方定义。

#### 8.1.2. "$vocabulary" 关键字（The "$vocabulary" Keyword）

<details>
  <summary>Expand original text</summary>

The "$vocabulary" keyword is used in meta-schemas to identify the vocabularies available for use in schemas described by that meta-schema. It is also used to indicate whether each vocabulary is required or optional, in the sense that an implementation MUST understand the required vocabularies in order to successfully process the schema. Together, this information forms a dialect. Any vocabulary that is understood by the implementation MUST be processed in a manner consistent with the semantic definitions contained within the vocabulary.

The value of this keyword MUST be an object. The property names in the object MUST be URIs (containing a scheme) and this URI MUST be normalized. Each URI that appears as a property name identifies a specific set of keywords and their semantics.

The URI MAY be a URL, but the nature of the retrievable resource is currently undefined, and reserved for future use. Vocabulary authors MAY use the URL of the vocabulary specification, in a human-readable media type such as text/html or text/plain, as the vocabulary URI. Vocabulary documents may be added in forthcoming drafts. For now, identifying the keyword set is deemed sufficient as that, along with meta-schema validation, is how the current "vocabularies" work today. Any future vocabulary document format will be specified as a JSON document, so using text/html or other non-JSON formats in the meantime will not produce any future ambiguity.

The values of the object properties MUST be booleans. If the value is true, then implementations that do not recognize the vocabulary MUST refuse to process any schemas that declare this meta-schema with "$schema". If the value is false, implementations that do not recognize the vocabulary SHOULD proceed with processing such schemas. The value has no impact if the implementation understands the vocabulary.

Per 6.5, unrecognized keywords SHOULD be treated as annotations. This remains the case for keywords defined by unrecognized vocabularies. It is not currently possible to distinguish between unrecognized keywords that are defined in vocabularies from those that are not part of any vocabulary.

The "$vocabulary" keyword SHOULD be used in the root schema of any schema document intended for use as a meta-schema. It MUST NOT appear in subschemas.

The "$vocabulary" keyword MUST be ignored in schema documents that are not being processed as a meta-schema. This allows validating a meta-schema M against its own meta-schema M' without requiring the validator to understand the vocabularies declared by M.
</details>

`$vocabulary` 关键字用于元模式中，用于标识元模式描述的模式可用的词汇表。它还用于指示每个词汇表是必需的还是可选的，实现必须（MUST）了解所需的词汇表才能成功处理模式。这些信息共同构成了一种方言。实现理解的任何词汇表都必须按照词汇表中包含的语义定义以一致的方式处理。

此关键字的值必须（MUST）是一个对象。对象中的属性名称必须（MUST）是 URI（包含方案），并且此 URI 必须（MUST）是标准化的。作为属性名称出现的每个 URI 标识一组特定的关键字及其语义。

URI 可能（MAY）是 URL，但是可检索资源的性质目前尚未定义，为未来使用而保留。词汇表作者可以（MAY）使用词汇表规范的 URL，以人类可读的媒体类型（如 `text/html` 或 `text/plain`）作为词汇表 URI。以后的草案可能会添加词汇表文档。目前，标识关键字集被认为是足够的，因为与元模式验证一起，这就是当前 "词汇表" 的工作方式。任何未来的词汇表文档格式将指定为 JSON 文档，因此同时使用 `text/html` 或其它非 JSON 格式不会产生任何未来的歧义。

对象属性的值必须（MUST）是布尔值。如果值为 true，则不识别词汇表的实现必须（MUST）拒绝处理声明了带有 `$schema` 的元模式的任何模式。如果值为 false，那么不识别词汇表的实现应（SHOULD）继续处理这些模式。如果实现理解词汇表，则该值无影响。

根据第 6.5 节，未识别的关键字应（SHOULD）被视为注释。对于由未识别词汇表定义的关键字，情况仍然如此。目前无法区分在词汇表中定义的未识别关键字与不属于任何词汇表的关键字。

`$vocabulary` 关键字应该（SHOULD）用于任何预期用作元模式的模式文档的根模式。它不得（MUST NOT）出现在子模式中。

在未作为元模式处理的模式文档中，必须（MUST）忽略 `$vocabulary` 关键字。这允许将元模式 M 验证到其自身的元模式 M'，而无需要求验证器了解 M 声明的词汇表。

##### 8.1.2.1. 默认词汇表（Default vocabularies）

<details>
  <summary>Expand original text</summary>

If "$vocabulary" is absent, an implementation MAY determine behavior based on the meta-schema if it is recognized from the URI value of the referring schema's "$schema" keyword. This is how behavior (such as Hyper-Schema usage) has been recognized prior to the existence of vocabularies.

If the meta-schema, as referenced by the schema, is not recognized, or is missing, then the behavior is implementation-defined. If the implementation proceeds with processing the schema, it MUST assume the use of the core vocabulary. If the implementation is built for a specific purpose, then it SHOULD assume the use of all of the most relevant vocabularies for that purpose.

For example, an implementation that is a validator SHOULD assume the use of all vocabularies in this specification and the companion Validation specification.
</details>

如果 `$vocabulary` 不存在，实现可以（MAY）根据元模式确定行为，如果从引用模式的 `$schema` 关键字的 URI 值中识别到元模式。这就是在词汇表存在之前识别行为（例如超模式使用）的方式。

如果模式引用的元模式无法识别或缺失，则行为由实现定义。如果实现继续处理模式，它必须（MUST）假定使用核心词汇表。如果实现针对特定目的而构建，则应（SHOULD）假定使用该目的所有最相关的词汇表。

例如，作为验证器的实现应该（SHOULD）假定使用本规范和配套验证规范中的所有词汇表。

##### 8.1.2.2. 词汇表的不可继承性（Non-inheritability of vocabularies）

<details>
  <summary>Expand original text</summary>

Note that the processing restrictions on "$vocabulary" mean that meta-schemas that reference other meta-schemas using "$ref" or similar keywords do not automatically inherit the vocabulary declarations of those other meta-schemas. All such declarations must be repeated in the root of each schema document intended for use as a meta-schema. This is demonstrated in the example meta-schema (Appendix D.2). This requirement allows implementations to find all vocabulary requirement information in a single place for each meta-schema. As schema extensibility means that there are endless potential ways to combine more fine-grained meta-schemas by reference, requiring implementations to anticipate all possibilities and search for vocabularies in referenced meta-schemas would be overly burdensome.
</details>

请注意，关于 `$vocabulary` 的处理限制意味着使用 `$ref` 或类似关键字引用其它元模式的元模式不会自动继承这些其它元模式的词汇表声明。所有这些声明必须在预期用作元模式的每个模式文档的根中重复。这在示例元模式（附录 D.2）中进行了演示。这个要求允许实现为每个元模式在一个地方找到所有词汇表需求信息。由于模式可扩展性意味着通过引用组合更细粒度元模式的无限可能性，要求实现预测所有可能性并在引用的元模式中搜索词汇表将是过于繁琐的。

#### 8.1.3. 元模式和词汇表 URI 的更新（Updates to Meta-Schema and Vocabulary URIs）

<details>
  <summary>Expand original text</summary>

Updated vocabulary and meta-schema URIs MAY be published between specification drafts in order to correct errors. Implementations SHOULD consider URIs dated after this specification draft and before the next to indicate the same syntax and semantics as those listed here.
</details>

在规范草案之间，可以（MAY）发布更新后的词汇表和元模式 URI 以纠正错误。实现应该（SHOULD）认为在此规范草案之后和下一个之前发布的 URI 表示与此处列出的相同的语法和语义。

### 8.2. 基本 URI、锚点和解引用（Base URI, Anchors, and Dereferencing）

<details>
  <summary>Expand original text</summary>

To differentiate between schemas in a vast ecosystem, schemas are identified by URI [RFC 3986][RFC3986], and can embed references to other schemas by specifying their URI.

Several keywords can accept a relative URI-reference [RFC 3986][RFC3986], or a value used to construct a relative URI-reference. For these keywords, it is necessary to establish a base URI in order to resolve the reference.
</details>

为了在庞大的生态系统中区分模式，模式由 URI [RFC 3986][RFC3986] 标识，并通过指定其 URI 嵌入对其它模式的引用。

几个关键字可以接受相对 URI-reference [RFC 3986][RFC3986]，或用于构造相对 URI-reference 的值。对于这些关键字，有必要建立一个基本 URI 以便解析引用。

#### 8.2.1. "$id" 关键字（The "$id" Keyword）

<details>
  <summary>Expand original text</summary>

The "$id" keyword identifies a schema resource with its canonical [RFC 6596][RFC6596] URI.

Note that this URI is an identifier and not necessarily a network locator. In the case of a network-addressable URL, a schema need not be downloadable from its canonical URI.

If present, the value for this keyword MUST be a string, and MUST represent a valid URI-reference [RFC 3986][RFC3986]. This URI-reference SHOULD be normalized, and MUST resolve to an absolute-URI [RFC 3986][RFC3986] (without a fragment), or to a URI with an empty fragment.

The empty fragment form is NOT RECOMMENDED and is retained only for backwards compatibility, and because the application/schema+json media type defines that a URI with an empty fragment identifies the same resource as the same URI with the fragment removed. However, since this equivalence is not part of the RFC 3986 normalization process [RFC 3986][RFC3986], implementers and schema authors cannot rely on generic URI libraries understanding it.

Therefore, "$id" MUST NOT contain a non-empty fragment, and SHOULD NOT contain an empty fragment. The absolute-URI form MUST be considered the canonical URI, regardless of the presence or absence of an empty fragment. An empty fragment is currently allowed because older meta-schemas have an empty fragment in their $id (or previously, id). A future draft may outright forbid even empty fragments in "$id".

The absolute-URI also serves as the base URI for relative URI-references in keywords within the schema resource, in accordance with RFC 3986 section 5.1.1 [RFC 3986][RFC3986] regarding base URIs embedded in content.

The presence of "$id" in a subschema indicates that the subschema constitutes a distinct schema resource within a single schema document. Furthermore, in accordance with [RFC 3986][RFC3986] section 5.1.2 regarding encapsulating entities, if an "$id" in a subschema is a relative URI-reference, the base URI for resolving that reference is the URI of the parent schema resource.

If no parent schema object explicitly identifies itself as a resource with "$id", the base URI is that of the entire document, as established by the steps given in the previous section. (Section 9.1.1)
</details>

`$id` 关键字用其规范 [RFC 6596][RFC6596] URI 标识一个模式资源。

请注意，此 URI 是一个标识符，而不一定是网络定位符。在网络可寻址 URL 的情况下，模式不必从其规范 URI 下载。

如果存在，此关键字的值必须（MUST）是字符串，并且必须（MUST）表示有效的 URI-reference [RFC 3986][RFC3986]。此 URI-reference 应（SHOULD）被规范化，并且必须（MUST）解析为 absolute-URI [RFC 3986][RFC3986]（无片段），或解析为具有空片段的 URI。

空片段形式不推荐（NOT RECOMMENDED）使用，仅为了向后兼容性而保留，因为 `application/schema+json` 媒体类型规定具有空片段的 URI 与去掉片段的相同 URI 标识相同资源。然而，由于这种等价性不是 RFC 3986 规范化过程 [RFC 3986][RFC3986] 的一部分，实现者和模式作者不能依赖通用 URI 库理解它。

因此，`$id` 不能（MUST NOT）包含非空片段，且不应（SHOULD NOT）包含空片段。absolute-URI 形式必须（MUST）被视为规范 URI，无论存在或不存在空片段。目前允许使用空片段，因为旧元模式的 `$id`（或之前的 id）中有空片段。未来的草案可能会直接禁止在 `$id` 中使用空片段。

absolute-URI 还用作模式资源中关键字的相对 URI-references 的基本 URI，根据 [RFC 3986][RFC3986] 第 5.1.1 节关于嵌入内容的基本 URI。

子模式中的 `$id` 存在表示该子模式在单个模式文档中构成一个独立的模式资源。此外，根据 [RFC 3986][RFC3986] 第 5.1.2 节关于封装实体，如果子模式中的 `$id` 是相对 URI-reference，解析该引用的基本 URI 是父模式资源的 URI。

如果没有父模式对象使用 `$id` 明确标识自己为资源，那么基本 URI 就是整个文档的基本 URI，由前一部分（第 9.1.1 节）给出的步骤确定。

##### 8.2.1.1. 标识根模式（Identifying the root schema）

<details>
  <summary>Expand original text</summary>

The root schema of a JSON Schema document SHOULD contain an "$id" keyword with an absolute-URI [RFC 3986][RFC3986] (containing a scheme, but no fragment).
</details>

JSON Schema 文档的根模式应（SHOULD）包含一个具有绝对 URI [RFC 3986][RFC3986]（包含方案，但不包含片段）的 `$id` 关键字。

#### 8.2.2. 定义与位置无关的标识符（Defining location-independent identifiers）

<details>
  <summary>Expand original text</summary>

Using JSON Pointer fragments requires knowledge of the structure of the schema. When writing schema documents with the intention to provide re-usable schemas, it may be preferable to use a plain name fragment that is not tied to any particular structural location. This allows a subschema to be relocated without requiring JSON Pointer references to be updated.

The "$anchor" and "$dynamicAnchor" keywords are used to specify such fragments. They are identifier keywords that can only be used to create plain name fragments, rather than absolute URIs as seen with "$id".

The base URI to which the resulting fragment is appended is the canonical URI of the schema resource containing the "$anchor" or "$dynamicAnchor" in question. As discussed in the previous section, this is either the nearest "$id" in the same or parent schema object, or the base URI for the document as determined according to RFC 3986.

Separately from the usual usage of URIs, "$dynamicAnchor" indicates that the fragment is an extension point when used with the "$dynamicRef" keyword. This low-level, advanced feature makes it easier to extend recursive schemas such as the meta-schemas, without imposing any particular semantics on that extension. See the section on "$dynamicRef" (Section 8.2.3.2) for details.

In most cases, the normal fragment behavior both suffices and is more intuitive. Therefore it is RECOMMENDED that "$anchor" be used to create plain name fragments unless there is a clear need for "$dynamicAnchor".

If present, the value of this keyword MUST be a string and MUST start with a letter ([A-Za-z]) or underscore ("\_"), followed by any number of letters, digits ([0-9]), hyphens ("-"), underscores ("\_"), and periods ("."). This matches the US-ASCII part of XML's NCName production [xml-names]. Note that the anchor string does not include the "#" character, as it is not a URI-reference. An "$anchor": "foo" becomes the fragment "#foo" when used in a URI. See below for full examples.

The effect of specifying the same fragment name multiple times within the same resource, using any combination of "$anchor" and/or "$dynamicAnchor", is undefined. Implementations MAY raise an error if such usage is detected.
</details>

使用 JSON Pointer 片段需要了解模式的结构。在编写具有提供可重用模式意图的模式文档时，可能更倾向于使用与任何特定结构位置无关的简单名称片段。这允许在不需要更新 JSON Pointer 引用的情况下重新定位子模式。

`$anchor` 和 `$dynamicAnchor` 关键字用于指定此类片段。它们是标识符关键字，只能用于创建简单名称片段，而不是像 `$id` 那样的绝对 URI。

将生成的片段附加到的基本 URI 是包含有关 `$anchor` 或 `$dynamicAnchor` 的模式资源的规范 URI。如前一节所述，这要么是相同或父模式对象中最近的 `$id`，要么是根据 RFC 3986 确定的文档的基本 URI。

与通常使用 URI 的情况分开，`$dynamicAnchor` 表示该片段在与 `$dynamicRef` 关键字一起使用时是扩展点。这种低级别、高级功能使得更容易扩展递归模式，例如元模式，而无需对该扩展施加任何特定的语义。有关详细信息，请参阅关于 `$dynamicRef`（第 8.2.3.2 节）的部分。

在大多数情况下，正常的片段行为既足够又更直观。因此，建议（RECOMMENDED）使用 `$anchor` 创建简单名称片段，除非明确需要 `$dynamicAnchor`。

如果存在，此关键字的值必须是一个字符串，并且必须（MUST）以字母 "[A-Za-z]" 或下划线 "\_" 开头，后跟任意数量的字母、数字 "[0-9]"、连字符 "-"、下划线 "\_" 和句点 "."。这与 XML 的 NCName 生成 [xml-names] 的 US-ASCII 部分相匹配。请注意，锚点字符串不包括 `#` 字符，因为它不是 URI 参考。`"$anchor": "foo"` 在 URI 中使用时变为片段 `#foo`。请参阅下面的完整示例。

在同一资源中多次指定相同的片段名称的效果，使用任何 `$anchor` 和/或 `$dynamicAnchor` 的组合，是未定义的。如果检测到此类使用，实现可能（MAY）会引发错误。

#### 8.2.3. 模式引用（Schema References）

<details>
  <summary>Expand original text</summary>

Several keywords can be used to reference a schema which is to be applied to the current instance location. "$ref" and "$dynamicRef" are applicator keywords, applying the referenced schema to the instance.

As the values of "$ref" and "$dynamicRef" are URI References, this allows the possibility to externalise or divide a schema across multiple files, and provides the ability to validate recursive structures through self-reference.

The resolved URI produced by these keywords is not necessarily a network locator, only an identifier. A schema need not be downloadable from the address if it is a network-addressable URL, and implementations SHOULD NOT assume they should perform a network operation when they encounter a network-addressable URI.
</details>

有几个关键字可以用于引用要应用于当前实例位置的模式。`$ref` 和 `$dynamicRef` 是应用器关键字，将引用的模式应用于实例。

由于 `$ref` 和 `$dynamicRef` 的值是 URI 引用，这允许在多个文件之间外部化或划分模式，并提供通过自引用验证递归结构的能力。

这些关键字生成的解析 URI 不一定是网络定位器，只是标识符。如果模式是网络可寻址的 URL，它不需要从地址下载，实现不应该（SHOULD NOT）假设在遇到网络可寻址的 URI 时应该执行网络操作。

##### 8.2.3.1. 使用 "$ref" 的直接引用（Direct References with "$ref"）

<details>
  <summary>Expand original text</summary>

The "$ref" keyword is an applicator that is used to reference a statically identified schema. Its results are the results of the referenced schema. Note that this definition of how the results are determined means that other keywords can appear alongside of "$ref" in the same schema object.

The value of the "$ref" keyword MUST be a string which is a URI-Reference. Resolved against the current URI base, it produces the URI of the schema to apply. This resolution is safe to perform on schema load, as the process of evaluating an instance cannot change how the reference resolves.
</details>

`$ref` 关键字是一个应用器，用于引用静态标识的模式。其结果是引用模式的结果。请注意，这种确定结果的方式意味着其它关键字可以出现在同一模式对象中的 `$ref` 旁边。

`$ref` 关键字的值必须（MUST）是一个字符串，该字符串是一个 URI-Reference。与当前基本 URI 解析，生成要应用的模式的 URI。这种解析在模式加载时是安全的，因为评估实例的过程不能改变引用的解析方式。

##### 8.2.3.2. 使用 "$dynamicRef" 的动态引用（Dynamic References with "$dynamicRef"）

<details>
  <summary>Expand original text</summary>

The "$dynamicRef" keyword is an applicator that allows for deferring the full resolution until runtime, at which point it is resolved each time it is encountered while evaluating an instance.

Together with "$dynamicAnchor", "$dynamicRef" implements a cooperative extension mechanism that is primarily useful with recursive schemas (schemas that reference themselves). Both the extension point and the runtime-determined extension target are defined with "$dynamicAnchor", and only exhibit runtime dynamic behavior when referenced with "$dynamicRef".

The value of the "$dynamicRef" property MUST be a string which is a URI-Reference. Resolved against the current URI base, it produces the URI used as the starting point for runtime resolution. This initial resolution is safe to perform on schema load.

If the initially resolved starting point URI includes a fragment that was created by the "$dynamicAnchor" keyword, the initial URI MUST be replaced by the URI (including the fragment) for the outermost schema resource in the dynamic scope (Section 7.1) that defines an identically named fragment with "$dynamicAnchor".

Otherwise, its behavior is identical to "$ref", and no runtime resolution is needed.

For a full example using these keyword, see appendix C. The difference between the hyper-schema meta-schema in pre-2019 drafts and an this draft dramatically demonstrates the utility of these keywords.
</details>

`$dynamicRef` 关键字是一个应用器，允许将完全解析推迟到运行时，在运行时每次遇到它时解析。

结合 `$dynamicAnchor`，`$dynamicRef` 实现了一个合作扩展机制，主要用于递归模式（引用自身的模式）。扩展点和运行时确定的扩展目标都使用 `$dynamicAnchor` 定义，并且仅在使用 `$dynamicRef` 引用时表现出运行时动态行为。

`$dynamicRef` 属性的值必须（MUST）是一个字符串，该字符串是一个 URI-Reference。与当前基本 URI 解析，生成用作运行时解析起点的 URI。这种初始解析在模式加载时是安全的。

如果最初解析的起点 URI 包含由 `$dynamicAnchor` 关键字创建的片段，则初始 URI 必须（MUST）替换为动态范围（第 7.1 节）中最外层模式资源的 URI（包括片段），该资源使用 `$dynamicAnchor` 定义具有相同名称的片段。

否则，其行为与 `$ref` 相同，无需运行时解析。

有关使用这些关键字的完整示例，请参阅附录 pre-2019 草案中的超模式元模式与本草案之间的区别极大地展示了这些关键字的实用性。

#### 8.2.4. 使用 "$defs" 进行模式重用（Schema Re-Use With "$defs"）

<details>
  <summary>Expand original text</summary>

The "$defs" keyword reserves a location for schema authors to inline re-usable JSON Schemas into a more general schema. The keyword does not directly affect the validation result.

This keyword's value MUST be an object. Each member value of this object MUST be a valid JSON Schema.

As an example, here is a schema describing an array of positive integers, where the positive integer constraint is a subschema in "$defs":

```json
{
    "type": "array",
    "items": { "$ref": "#/$defs/positiveInteger" },
    "$defs": {
        "positiveInteger": {
            "type": "integer",
            "exclusiveMinimum": 0
        }
    }
}
```

</details>

`$defs` 关键字为模式作者保留了一个位置，以将可重用的 JSON Schema 内联到更通用的模式中。该关键字不直接影响验证结果。

此关键字的值必须（MUST）是一个对象。该对象的每个成员值必须（MUST）是有效的 JSON Schema 。

作为一个例子，这里有一个描述正整数数组的模式，其中正整数约束是 `$defs` 中的一个子模式：

```json
{
    "type": "array",
    "items": { "$ref": "#/$defs/positiveInteger" },
    "$defs": {
        "positiveInteger": {
            "type": "integer",
            "exclusiveMinimum": 0
        }
    }
}
```

### 8.3. 使用 "$comment" 进行注释（Comments With "$comment"）

<details>
  <summary>Expand original text</summary>

This keyword reserves a location for comments from schema authors to readers or maintainers of the schema.

The value of this keyword MUST be a string. Implementations MUST NOT present this string to end users. Tools for editing schemas SHOULD support displaying and editing this keyword. The value of this keyword MAY be used in debug or error output which is intended for developers making use of schemas.

Schema vocabularies SHOULD allow "$comment" within any object containing vocabulary keywords. Implementations MAY assume "$comment" is allowed unless the vocabulary specifically forbids it. Vocabularies MUST NOT specify any effect of "$comment" beyond what is described in this specification.

Tools that translate other media types or programming languages to and from application/schema+json MAY choose to convert that media type or programming language's native comments to or from "$comment" values. The behavior of such translation when both native comments and "$comment" properties are present is implementation-dependent.

Implementations MAY strip "$comment" values at any point during processing. In particular, this allows for shortening schemas when the size of deployed schemas is a concern.

Implementations MUST NOT take any other action based on the presence, absence, or contents of "$comment" properties. In particular, the value of "$comment" MUST NOT be collected as an annotation result.
</details>

此关键字为模式作者保留了一个位置，用于向模式的读者或维护者发表评论。

此关键字的值必须（MUST）是一个字符串。实现不得（MUST NOT）向终端用户呈现此字符串。编辑模式的工具应（SHOULD）支持显示和编辑此关键字。此关键字的值可以（MAY）用于调试或错误输出，供使用模式的开发人员使用。

模式词汇表应（SHOULD）允许在包含词汇表关键字的任何对象中使用 `$comment`。实现可以（MAY）假设允许使用 `$comment`，除非词汇表特意禁止它。词汇表不能（MUST NOT）规定 `$comment` 的任何效果，除了本规范中描述的内容。

将其它媒体类型或编程语言转换为 `application/schema+json` 及其反向转换的工具可以（MAY）选择将该媒体类型或编程语言的原生注释转换为或从 `$comment` 值。当存在原生注释和 `$comment` 属性时，此类翻译的行为取决于实现。

实现可以（MAY）在处理过程中的任何时候去除 `$comment` 值。特别是，当部署模式的大小是一个问题时，这允许缩短模式。

实现不得（MUST NOT）根据 `$comment` 属性的存在、不存在或内容采取任何其它操作。特别是，`$comment` 的值不能（MUST NOT）作为注解结果收集。

## 9. 加载和处理模式（Loading and Processing Schemas）
### 9.1. 加载模式（Loading a Schema）
#### 9.1.1. 初始化基本 URI（Initial Base URI）

<details>
  <summary>Expand original text</summary>

RFC3986 Section 5.1 [RFC3986] defines how to determine the default base URI of a document.

Informatively, the initial base URI of a schema is the URI at which it was found, whether that was a network location, a local filesystem, or any other situation identifiable by a URI of any known scheme.

If a schema document defines no explicit base URI with "$id" (embedded in content), the base URI is that determined per RFC 3986 section 5 [RFC3986].

If no source is known, or no URI scheme is known for the source, a suitable implementation-specific default URI MAY be used as described in RFC 3986 Section 5.1.4 [RFC3986]. It is RECOMMENDED that implementations document any default base URI that they assume.

If a schema object is embedded in a document of another media type, then the initial base URI is determined according to the rules of that media type.

Unless the "$id" keyword described in an earlier section is present in the root schema, this base URI SHOULD be considered the canonical URI of the schema document's root schema resource.
</details>

RFC3986 第5.1节定义了如何确定文档的默认基本 URI。
作为信息，模式的初始基本 URI 是找到它的 URI，无论是网络位置、本地文件系统还是任何其它可以通过已知方案的 URI 标识的情况。

如果模式文档未使用 `$id`（嵌入在内容中）定义明确的基本 URI，则基本 URI 是根据 RFC 3986 第5节确定的。

如果没有已知的来源，或者没有已知的 URI 方案，可以使用 RFC 3986 第5.1.4节中描述的适当的实现特定的默认 URI。建议实现记录它们所假设的任何默认基本 URI。

如果模式对象嵌入在另一种媒体类型的文档中，则初始基本 URI 根据该媒体类型的规则确定。

除非根模式中存在前面部分中描述的 `$id` 关键字，否则应将此基本 URI 视为模式文档的根模式资源的规范 URI。

#### 9.1.2. 加载引用的模式（Loading a referenced schema）

<details>
  <summary>Expand original text</summary>

The use of URIs to identify remote schemas does not necessarily mean anything is downloaded, but instead JSON Schema implementations SHOULD understand ahead of time which schemas they will be using, and the URIs that identify them.

When schemas are downloaded, for example by a generic user-agent that does not know until runtime which schemas to download, see Usage for Hypermedia (Section 9.5.1).

Implementations SHOULD be able to associate arbitrary URIs with an arbitrary schema and/or automatically associate a schema's "$id"-given URI, depending on the trust that the validator has in the schema. Such URIs and schemas can be supplied to an implementation prior to processing instances, or may be noted within a schema document as it is processed, producing associations as shown in appendix A.

A schema MAY (and likely will) have multiple URIs, but there is no way for a URI to identify more than one schema. When multiple schemas try to identify as the same URI, validators SHOULD raise an error condition.
</details>

使用 URI 标识远程模式并不一定意味着下载任何内容，而是 JSON Schema 实现应该（SHOULD）提前了解它们将使用哪些模式，以及标识它们的 URI。

当模式被下载时，例如一个通用用户代理在运行时才知道要下载哪些模式，请参阅超媒体的使用（第9.5.1节）。

实现应该（SHOULD）能够将任意 URI 与任意模式关联起来，或者自动关联模式的 `$id` 给定的 URI，具体取决于验证器对模式的信任。这些 URI 和模式可以在处理实例之前提供给实现，或者在处理模式文档时记录，如附录 A 中所示。

模式可能（并且很可能）具有多个 URI，但是一个 URI 无法标识多个模式。当多个模式尝试标识为相同的 URI 时，验证器应该（SHOULD）引发错误条件。

#### 9.1.3. 检测元模式（Detecting a Meta-Schema）

<details>
  <summary>Expand original text</summary>

Implementations MUST recognize a schema as a meta-schema if it is being examined because it was identified as such by another schema's "$schema" keyword. This means that a single schema document might sometimes be considered a regular schema, and other times be considered a meta-schema.

In the case of examining a schema which is its own meta-schema, when an implementation begins processing it as a regular schema, it is processed under those rules. However, when loaded a second time as a result of checking its own "$schema" value, it is treated as a meta-schema. So the same document is processed both ways in the course of one session.

Implementations MAY allow a schema to be explicitly passed as a meta-schema, for implementation-specific purposes, such as pre-loading a commonly used meta-schema and checking its vocabulary support requirements up front. Meta-schema authors MUST NOT expect such features to be interoperable across implementations.
</details>

如果一个模式是因为另一个模式的 `$schema` 关键字将其识别为元模式而被检查的，实现必须（MUST）将其识别为元模式。这意味着单个模式文档有时可能被视为常规模式，而其它时候被视为元模式。

在检查模式本身就是其元模式的情况下，当实现开始将其视为常规模式进行处理时，将按照这些规则进行处理。然而，当第二次加载检查其自己的 `$schema` 值时，它被视为元模式。因此，在一次会话过程中，相同的文档会以两种方式进行处理。

实现可以（MAY）允许模式被显式地作为元模式传递，用于实现特定的目的，例如预加载常用的元模式并在前端检查其词汇表支持要求。元模式作者不能期望这些功能在实现之间具有互操作性。

### 9.2. 取消引用（Dereferencing）

<details>
  <summary>Expand original text</summary>

Schemas can be identified by any URI that has been given to them, including a JSON Pointer or their URI given directly by "$id". In all cases, dereferencing a "$ref" reference involves first resolving its value as a URI reference against the current base URI per [RFC 3986][RFC3986].

If the resulting URI identifies a schema within the current document, or within another schema document that has been made available to the implementation, then that schema SHOULD be used automatically.

For example, consider this schema:

```json
{
    "$id": "https://example.net/root.json",
    "items": {
        "type": "array",
        "items": { "$ref": "#item" }
    },
    "$defs": {
        "single": {
            "$anchor": "item",
            "type": "object",
            "additionalProperties": { "$ref": "other.json" }
        }
    }
}
```

When an implementation encounters the <#/$defs/single> schema, it resolves the "$anchor" value as a fragment name against the current base URI to form <https://example.net/root.json#item>.

When an implementation then looks inside the <#/items> schema, it encounters the <#item> reference, and resolves this to <https://example.net/root.json#item>, which it has seen defined in this same document and can therefore use automatically.

When an implementation encounters the reference to "other.json", it resolves this to <https://example.net/other.json>, which is not defined in this document. If a schema with that identifier has otherwise been supplied to the implementation, it can also be used automatically. What should implementations do when the referenced schema is not known? Are there circumstances in which automatic network dereferencing is allowed? A same origin policy? A user-configurable option? In the case of an evolving API described by Hyper-Schema, it is expected that new schemas will be added to the system dynamically, so placing an absolute requirement of pre-loading schema documents is not feasible.
</details>

模式可以由赋予它们的任何 URI 来识别，包括 JSON 指针或由 `$id` 直接给出的 URI。在所有情况下，取消引用 `$ref` 引用首先涉及根据 [RFC 3986][RFC3986] 将其值解析为相对于当前基本 URI 的 URI 引用。

如果生成的 URI 在当前文档中标识了一个模式，或者在已提供给实现的其它模式文档中，那么应该（SHOULD）自动使用该模式。

例如，考虑这个模式：

```json
{
    "$id": "https://example.net/root.json",
    "items": {
        "type": "array",
        "items": { "$ref": "#item" }
    },
    "$defs": {
        "single": {
            "$anchor": "item",
            "type": "object",
            "additionalProperties": { "$ref": "other.json" }
        }
    }
}
```

当实现遇到 `<#/$defs/single>` 模式时，它会根据当前基本 URI 解析 `$anchor` 值作为片段名称，形成 https://example.net/root.json#item。

当实现然后查看 `<#/items>` 模式时，它会遇到 `<#item>` 引用，并将其解析为 https://example.net/root.json#item，它已在本文档中看到过定义，因此可以自动使用。

当实现遇到对 `other.json` 的引用时，它将其解析为 https://example.net/other.json，这在本文档中未定义。如果一个具有该标识符的模式已经提供给实现，它也可以自动使用。当引用的模式未知时，实现应该怎么做？是否有允许自动网络取消引用的情况？相同源策略？用户可配置选项？在超模式描述的不断发展的 API 的情况下，预计系统会动态添加新的模式，因此要求预加载模式文档是不可行的。

当引用的模式未知时，实现应该根据需要决定是否下载模式。例如，它们可以选择遵循同源策略，或者允许用户配置选项以启用或禁用网络取消引用。在超模式描述的不断发展的 API 的情况下，预计系统会动态添加新的模式，因此要求预加载模式文档是不可行的。

#### 9.2.1. JSON 指针片段和嵌入式模式资源（JSON Pointer fragments and embedded schema resources）

<details>
  <summary>Expand original text</summary>

Since JSON Pointer URI fragments are constructed based on the structure of the schema document, an embedded schema resource and its subschemas can be identified by JSON Pointer fragments relative to either its own canonical URI, or relative to any containing resource's URI.

Conceptually, a set of linked schema resources should behave identically whether each resource is a separate document connected with schema references (Section 8.2.3), or is structured as a single document with one or more schema resources embedded as subschemas.

Since URIs involving JSON Pointer fragments relative to the parent schema resource's URI cease to be valid when the embedded schema is moved to a separate document and referenced, applications and schemas SHOULD NOT use such URIs to identify embedded schema resources or locations within them.

Consider the following schema document that contains another schema resource embedded within it:

```json
{
    "$id": "https://example.com/foo",
    "items": {
        "$id": "https://example.com/bar",
        "additionalProperties": { }
    }
}
```

The URI "https://example.com/foo#/items" points to the "items" schema, which is an embedded resource. The canonical URI of that schema resource, however, is "https://example.com/bar".

For the "additionalProperties" schema within that embedded resource, the URI "https://example.com/foo#/items/additionalProperties" points to the correct object, but that object's URI relative to its resource's canonical URI is "https://example.com/bar#/additionalProperties".

Now consider the following two schema resources linked by reference using a URI value for "$ref":

```json
{
    "$id": "https://example.com/foo",
    "items": {
        "$ref": "bar"
    }
}

{
    "$id": "https://example.com/bar",
    "additionalProperties": { }
}
```

Here we see that "https://example.com/bar#/additionalProperties", using a JSON Pointer fragment appended to the canonical URI of the "bar" schema resource, is still valid, while "https://example.com/foo#/items/additionalProperties", which relied on a JSON Pointer fragment appended to the canonical URI of the "foo" schema resource, no longer resolves to anything.

Note also that "https://example.com/foo#/items" is valid in both arrangements, but resolves to a different value. This URI ends up functioning similarly to a retrieval URI for a resource. While this URI is valid, it is more robust to use the "$id" of the embedded or referenced resource unless it is specifically desired to identify the object containing the "$ref" in the second (non-embedded) arrangement.

An implementation MAY choose not to support addressing schema resource contents by URIs using a base other than the resource's canonical URI, plus a JSON Pointer fragment relative to that base. Therefore, schema authors SHOULD NOT rely on such URIs, as using them may reduce interoperability. This is to avoid requiring implementations to keep track of a whole stack of possible base URIs and JSON Pointer fragments for each, given that all but one will be fragile if the schema resources are reorganized. Some have argued that this is easy so there is no point in forbidding it, while others have argued that it complicates schema identification and should be forbidden. Feedback on this topic is encouraged. After some discussion, we feel that we need to remove the use of "canonical" in favour of talking about JSON Pointers which reference across schema resource boundaries as undefined or even forbidden behavior (https://github.com/json-schema-org/json-schema-spec/issues/937, https://github.com/json-schema-org/json-schema-spec/issues/1183)

Further examples of such non-canonical URI construction, as well as the appropriate canonical URI-based fragments to use instead, are provided in appendix A.
</details>

由于 JSON 指针 URI 片段是基于模式文档的结构构建的，所以可以通过相对于其自身规范 URI 或相对于任何包含资源 URI 的 JSON 指针片段来标识嵌入式模式资源及其子模式。

从概念上讲，一组链接的模式资源在每个资源是用模式引用（第 8.2.3 节）连接的单独文档或作为一个或多个模式资源嵌入为子模式的单个文档的结构下，它们的行为应该是相同的。

由于涉及相对于父模式资源 URI 的 JSON 指针片段的 URI 在嵌入式模式被移动到单独的文档并引用时不再有效，因此应用程序和模式不应（SHOULD NOT）使用此类 URI 来标识嵌入式模式资源或其中的位置。

考虑以下包含另一个嵌入式模式资源的模式文档：

```json
{
    "$id": "https://example.com/foo",
    "items": {
        "$id": "https://example.com/bar",
        "additionalProperties": { }
    }
}
```

URI "https://example.com/foo#/items" 指向 "items" 模式，它是一个嵌入式资源。然而，该模式资源的规范 URI 是 "https://example.com/bar"。

对于嵌入式资源中的 "additionalProperties" 模式，URI "https://example.com/foo#/items/additionalProperties" 指向正确的对象，但相对于资源规范 URI 的对象 URI 是 "https://example.com/bar#/additionalProperties"。

现在考虑以下两个模式资源，它们通过使用 `$ref` 的 URI 值进行引用链接：

```json
{
    "$id": "https://example.com/foo",
    "items": {
        "$ref": "bar"
    }
}

{
    "$id": "https://example.com/bar",
    "additionalProperties": { }
}
```

在这里，我们看到 "https://example.com/bar#/additionalProperties"，使用附加到 "bar" 模式资源的规范 URI 的 JSON 指针片段仍然有效，而 "https://example.com/foo#/items/additionalProperties"，它依赖于附加到 "foo" 模式资源的规范 URI 的 JSON 指针片段，不再解析为任何内容。

还要注意，"https://example.com/foo#/items" 在两种安排中都有效，但解析为不同的值。这个 URI 最终类似于用于检索资源的 URI。虽然这个 URI 有效，但在第二种（非嵌入式）安排中，除非特别需要标识包含 `$ref` 的对象，否则使用嵌入式或引用资源的 `$id` 更为稳健。

实现可以选择不支持使用除资源规范 URI 之外的基础以及相对于该基础的 JSON 指针片段来解析模式资源内容的 URI。因此，模式作者不应依赖这样的 URI，因为使用它们可能会降低互操作性。这是为了避免要求实现跟踪每个可能的基本 URI 和每个 JSON 指针片段的整个堆栈，因为在模式资源重新组织时，除了一个之外的所有内容都会变得脆弱。有些人认为这很容易，所以没有禁止的必要，而另一些人则认为这使模式识别变得复杂，应该被禁止。鼓励对这个主题提供反馈。经过一些讨论，我们认为需要取消使用“规范”这个词，而是谈论跨模式资源边界引用的 JSON 指针，将其视为未定义甚至是禁止的行为（https://github.com/json-schema-org/json-schema-spec/issues/937, https://github.com/json-schema-org/json-schema-spec/issues/1183）

附录 A 提供了此类非规范 URI 构造的进一步示例，以及相应的基于规范 URI 的片段替代方法。

### 9.3. 复合文档（Compound Documents）

<details>
  <summary>Expand original text</summary>

A Compound Schema Document is defined as a JSON document (sometimes called a "bundled" schema) which has multiple embedded JSON Schema Resources bundled into the same document to ease transportation.

Each embedded Schema Resource MUST be treated as an individual Schema Resource, following standard schema loading and processing requirements, including determining vocabulary support.
</details>

复合模式文档定义为一个 JSON 文档（有时称为“捆绑”模式），其中包含多个嵌入式 JSON Schema 资源，捆绑到同一文档中以方便传输。

每个嵌入式模式资源必须作为单独的模式资源进行处理，遵循标准模式加载和处理要求，包括确定词汇表支持。

#### 9.3.1. 打包（Bundling）

<details>
  <summary>Expand original text</summary>

The bundling process for creating a Compound Schema Document is defined as taking references (such as "$ref") to an external Schema Resource and embedding the referenced Schema Resources within the referring document. Bundling SHOULD be done in such a way that all URIs (used for referencing) in the base document and any referenced/embedded documents do not require altering.

Each embedded JSON Schema Resource MUST identify itself with a URI using the "$id" keyword, and SHOULD make use of the "$schema" keyword to identify the dialect it is using, in the root of the schema resource. It is RECOMMENDED that the URI identifier value of "$id" be an Absolute URI.

When the Schema Resource referenced by a by-reference applicator is bundled, it is RECOMMENDED that the Schema Resource be located as a value of a "$defs" object at the containing schema's root. The key of the "$defs" for the now embedded Schema Resource MAY be the "$id" of the bundled schema or some other form of application defined unique identifer (such as a UUID). This key is not intended to be referenced in JSON Schema, but may be used by an application to aid the bundling process.

A Schema Resource MAY be embedded in a location other than "$defs" where the location is defined as a schema value.

A Bundled Schema Resource MUST NOT be bundled by replacing the schema object from which it was referenced, or by wrapping the Schema Resource in other applicator keywords.

In order to produce identical output, references in the containing schema document to the previously external Schema Resources MUST NOT be changed, and now resolve to a schema using the "$id" of an embedded Schema Resource. Such identical output includes validation evaluation and URIs or paths used in resulting annotations or errors.

While the bundling process will often be the main method for creating a Compound Schema Document, it is also possible and expected that some will be created by hand, potentially without individual Schema Resources existing on their own previously.
</details>

创建复合模式文档的打包过程定义为获取对外部模式资源的引用（如 `$ref`），并将引用的模式资源嵌入到引用文档中。打包应该（SHOULD）以这样的方式进行，以便基本文档和任何引用/嵌入文档中的所有 URI（用于引用）不需要更改。

每个嵌入式 JSON Schema 资源都必须（MUST）使用 `$id` 关键字以 URI 标识自己，并且应（SHOULD）在模式资源的根部使用 `$schema` 关键字来标识所使用的方言。建议（RECOMMENDED） `$id` 的 URI 标识符值为绝对 URI。

当通过引用应用器引用的模式资源被捆绑时，建议（RECOMMENDED）将模式资源作为包含模式根目录下 `$defs` 对象的值。现在嵌入式模式资源的 `$defs` 键可以（MAY）是捆绑模式的 `$id`，也可以是其它形式的应用程序定义的唯一标识符（如 UUID）。此键不打算在 JSON Schema 中引用，但可以由应用程序使用，以帮助打包过程。

模式资源可以（MAY）嵌入在除 `$defs` 之外的其它位置，其中该位置定义为模式值。

捆绑的模式资源不得（MUST NOT）通过替换从中引用的模式对象或将模式资源包装在其它应用程序关键字中来捆绑。

为了产生相同的输出，包含模式文档中对先前外部模式资源的引用不得（MUST NOT）更改，现在使用嵌入式模式资源的 `$id` 解析为模式。这种相同的输出包括验证评估以及用于结果注释或错误的 URI 或路径。

尽管打包过程通常是创建复合模式文档的主要方法，但预计还有一些将通过手工创建，可能在此之前没有单独的模式资源存在。

#### 9.3.2. 不同和默认方言（Differing and Default Dialects）

<details>
  <summary>Expand original text</summary>

When multiple schema resources are present in a single document, schema resources which do not define with which dialect they should be processed MUST be processed with the same dialect as the enclosing resource.

Since any schema that can be referenced can also be embedded, embedded schema resources MAY specify different processing dialects using the "$schema" values from their enclosing resource.
</details>

当一个文档中存在多个模式资源时，没有定义应该用哪种方言处理的模式资源必须（MUST）与包含它们的资源使用相同的方言进行处理。

由于任何可以被引用的模式也可以被嵌入，嵌入式模式资源可以（MAY）使用来自其包含资源的 `$schema` 值指定不同的处理方言。

#### 9.3.3. 验证（Validating）

<details>
  <summary>Expand original text</summary>

Given that a Compound Schema Document may have embedded resources which identify as using different dialects, these documents SHOULD NOT be validated by applying a meta-schema to the Compound Schema Document as an instance. It is RECOMMENDED that an alternate validation process be provided in order to validate Schema Documents. Each Schema Resource SHOULD be separately validated against its associated meta-schema. If you know a schema is what's being validated, you can identify if the schemas is a Compound Schema Document or not, by way of use of "$id", which identifies an embedded resource when used not at the document's root.

A Compound Schema Document in which all embedded resources identify as using the same dialect, or in which "$schema" is omitted and therefore defaults to that of the enclosing resource, MAY be validated by applying the appropriate meta-schema.
</details>

鉴于复合模式文档可能具有使用不同方言的嵌入式资源，这些文档不应（SHOULD NOT）通过将元模式应用于复合模式文档实例来进行验证。建议（RECOMMENDED）提供另一种验证过程来验证模式文档。每个模式资源应（SHOULD）分别针对其关联的元模式进行验证。如果你知道正在验证的模式，你可以通过使用 `$id` 来确定模式是否为复合模式文档，`$id` 在文档根以外的地方使用时，用于标识嵌入式资源。

所有嵌入式资源都使用相同方言的复合模式文档，或者省略了 `$schema`，因此默认为包含资源的方言，可以（MAY）通过应用相应的元模式进行验证。

### 9.4. 注意事项（Caveats）
#### 9.4.1. 防止无限递归（Guarding Against Infinite Recursion）

<details>
  <summary>Expand original text</summary>

A schema MUST NOT be run into an infinite loop against an instance. For example, if two schemas "#alice" and "#bob" both have an "allOf" property that refers to the other, a naive validator might get stuck in an infinite recursive loop trying to validate the instance. Schemas SHOULD NOT make use of infinite recursive nesting like this; the behavior is undefined.
</details>

模式不能（MUST NOT）在实例中陷入无限循环。例如，如果两个模式 `#alice` 和 `#bob` 都具有指向对方的 `allOf` 属性，一个简单的验证器可能会陷入无限递归循环，试图验证实例。模式不应（SHOULD NOT）使用这种无限递归嵌套；这种行为是未定义的。

#### 9.4.2. 可能非模式的引用（References to Possible Non-Schemas）

<details>
  <summary>Expand original text</summary>

Subschema objects (or booleans) are recognized by their use with known applicator keywords or with location-reserving keywords such as "$defs" (Section 8.2.4) that take one or more subschemas as a value. These keywords may be "$defs" and the standard applicators from this document, or extension keywords from a known vocabulary, or implementation-specific custom keywords.

Multi-level structures of unknown keywords are capable of introducing nested subschemas, which would be subject to the processing rules for "$id". Therefore, having a reference target in such an unrecognized structure cannot be reliably implemented, and the resulting behavior is undefined. Similarly, a reference target under a known keyword, for which the value is known not to be a schema, results in undefined behavior in order to avoid burdening implementations with the need to detect such targets. These scenarios are analogous to fetching a schema over HTTP but receiving a response with a Content-Type other than application/schema+json. An implementation can certainly try to interpret it as a schema, but the origin server offered no guarantee that it actually is any such thing. Therefore, interpreting it as such has security implications and may produce unpredictable results.

Note that single-level custom keywords with identical syntax and semantics to "$defs" do not allow for any intervening "$id" keywords, and therefore will behave correctly under implementations that attempt to use any reference target as a schema. However, this behavior is implementation-specific and MUST NOT be relied upon for interoperability.
</details>

通过使用已知的应用器关键字或采用一个或多个子模式作为值的位置保留关键字（如 `$defs`（第8.2.4节））来识别子模式对象（或布尔值）。这些关键字可能是本文档中的 `$defs` 和标准应用器，或已知词汇表中的扩展关键字，或特定于实现的自定义关键字。

未知关键字的多级结构能够引入嵌套子模式，这些子模式将受到 `$id` 处理规则的约束。因此，在这样一个无法识别的结构中具有引用目标是无法可靠实现的，其结果行为是未定义的。类似地，在已知关键字下的引用目标，已知其值不是模式，会导致未定义的行为，以避免实现需要检测这样的目标的负担。这些场景类似于通过 HTTP 获取模式，但接收到的响应的 Content-Type 不是 `application/schema+json`。实现当然可以尝试将其解释为模式，但原始服务器并未保证它实际上就是这样的东西。因此，将其解释为此类具有安全性含义，并可能产生不可预测的结果。

请注意，具有与 `$defs` 相同语法和语义的单级自定义关键字不允许有任何介入的 `$id` 关键字，因此在实现中尝试将任何引用目标用作模式时将表现正确。然而，这种行为是特定于实现的，不得（MUST NOT）依赖于互操作性。

### 9.5. 关联实例和模式（Associating Instances and Schemas）
#### 9.5.1. 超媒体使用（Usage for Hypermedia）

<details>
  <summary>Expand original text</summary>

JSON has been adopted widely by HTTP servers for automated APIs and robots. This section describes how to enhance processing of JSON documents in a more RESTful manner when used with protocols that support media types and Web linking [RFC8288].
</details>

JSON 已经被广泛应用于 HTTP 服务器，用于自动化 API 和机器人。本节介绍如何在支持媒体类型和 Web 链接 [RFC8288] 的协议中使用 JSON 文档时，以更符合 REST 风格的方式增强处理。

##### 9.5.1.1. 链接到模式（Linking to a Schema）

<details>
  <summary>Expand original text</summary>

It is RECOMMENDED that instances described by a schema provide a link to a downloadable JSON Schema using the link relation "describedby", as defined by Linked Data Protocol 1.0, section 8.1 [W3C.REC-ldp-20150226][W3C.REC-ldp-20150226].

In HTTP, such links can be attached to any response using the Link header [RFC8288]. An example of such a header would be:

```txt
Link: <https://example.com/my-hyper-schema>; rel="describedby"
```

</details>

建议（RECOMMENDED）由模式描述的实例提供一个链接，链接到可下载的 JSON 模式，使用链接关系 `describedby"`，如 Linked Data Protocol 1.0的第8.1节 [W3C.REC-ldp-20150226][W3C.REC-ldp-20150226] 所定义。

在 HTTP 中，这样的链接可以使用 Link 头 [RFC8288] 附加到任何响应。这样一个头的示例是：

```txt
Link: <https://example.com/my-hyper-schema>; rel="describedby"
```

##### 9.5.1.2. HTTP使用（Usage Over HTTP）

<details>
  <summary>Expand original text</summary>

When used for hypermedia systems over a network, HTTP [RFC7231] is frequently the protocol of choice for distributing schemas. Misbehaving clients can pose problems for server maintainers if they pull a schema over the network more frequently than necessary, when it's instead possible to cache a schema for a long period of time.

HTTP servers SHOULD set long-lived caching headers on JSON Schemas. HTTP clients SHOULD observe caching headers and not re-request documents within their freshness period. Distributed systems SHOULD make use of a shared cache and/or caching proxy.

Clients SHOULD set or prepend a User-Agent header specific to the JSON Schema implementation or software product. Since symbols are listed in decreasing order of significance, the JSON Schema library name/version should precede the more generic HTTP library name (if any). For example:

```txt
User-Agent: product-name/5.4.1 so-cool-json-schema/1.0.2 curl/7.43.0
```

Clients SHOULD be able to make requests with a "From" header so that server operators can contact the owner of a potentially misbehaving script.
</details>

在网络上的超媒体系统中使用时，HTTP [RFC7231] 通常是分发模式的首选协议。如果客户端在不必要的情况下频繁地从网络上获取模式，而不是将模式缓存很长时间，这可能会给服务器维护者带来问题。

HTTP 服务器应（SHOULD）在 JSON 模式上设置长期缓存头。HTTP 客户端应（SHOULD）遵守缓存头，不在其新鲜度期内重新请求文档。分布式系统应（SHOULD）使用共享缓存和/或缓存代理。

客户端应（SHOULD）设置或添加特定于 JSON 模式实现或软件产品的 User-Agent 头。由于符号按照重要性递减的顺序排列，JSON 模式库名称/版本应位于更通用的 HTTP 库名称（如果有的话）之前。例如：

```txt
User-Agent: product-name/5.4.1 so-cool-json-schema/1.0.2 curl/7.43.0
```

客户端应（SHOULD）能够使用 `From` 头发送请求，以便服务器操作员可以联系到可能存在问题的脚本的所有者。

## 10. 应用子模式的词汇表（A Vocabulary for Applying Subschemas）

<details>
  <summary>Expand original text</summary>

This section defines a vocabulary of applicator keywords that are RECOMMENDED for use as the basis of other vocabularies.

Meta-schemas that do not use "$vocabulary" SHOULD be considered to require this vocabulary as if its URI were present with a value of true.

The current URI for this vocabulary, known as the Applicator vocabulary, is: <https://json-schema.org/draft/2020-12/vocab/applicator>.

The current URI for the corresponding meta-schema is: https://json-schema.org/draft/2020-12/meta/applicator.
</details>

本节定义了一个应用器关键字词汇表，建议（RECOMMENDED）将其作为其它词汇表的基础。
不使用 `$vocabulary` 的元模式应（SHOULD）被视为需要这个词汇表，就像其 URI 存在且值为 true 一样。

该词汇表的当前 URI，称为应用器词汇表，是：https://json-schema.org/draft/2020-12/vocab/applicator。

相应元模式的当前 URI 是：https://json-schema.org/draft/2020-12/meta/applicator。

### 10.1. 关键字独立性（Keyword Independence）

<details>
  <summary>Expand original text</summary>

Schema keywords typically operate independently, without affecting each other's outcomes.

For schema author convenience, there are some exceptions among the keywords in this vocabulary:

- "additionalProperties", whose behavior is defined in terms of "properties" and "patternProperties"
- "items", whose behavior is defined in terms of "prefixItems"
- "contains", whose behavior is affected by the presence and value of "minContains", in the Validation vocabulary

</details>

模式关键字通常独立操作，不会影响彼此的结果。

为方便模式作者，这个词汇表中的关键字存在一些例外：

- `additionalProperties`，其行为是基于 `properties` 和 `patternProperties` 定义的
- `items`，其行为是基于 `prefixItems` 定义的
- `contains`，其行为受到验证词汇表中 `minContains` 存在及其值的影响

### 10.2. 应用子模式的关键字（Keywords for Applying Subschemas in Place）

<details>
  <summary>Expand original text</summary>

These keywords apply subschemas to the same location in the instance as the parent schema is being applied. They allow combining or modifying the subschema results in various ways.

Subschemas of these keywords evaluate the instance completely independently such that the results of one such subschema MUST NOT impact the results of sibling subschemas. Therefore subschemas may be applied in any order.
</details>

这些关键字将子模式应用到实例中与父模式相同的位置。它们允许以各种方式组合或修改子模式结果。

这些关键字的子模式独立评估实例，以致一个这样的子模式的结果不得（MUST NOT）影响同级子模式的结果。因此，子模式可以按任意顺序应用。

#### 10.2.1. 逻辑应用子模式的关键字（Keywords for Applying Subschemas With Logic）

<details>
  <summary>Expand original text</summary>

These keywords correspond to logical operators for combining or modifying the boolean assertion results of the subschemas. They have no direct impact on annotation collection, although they enable the same annotation keyword to be applied to an instance location with different values. Annotation keywords define their own rules for combining such values.
</details>

这些关键字对应于组合或修改子模式的布尔断言结果的逻辑运算符。它们对注释收集没有直接影响，尽管它们允许将相同的注释关键字应用于具有不同值的实例位置。注释关键字为组合这些值定义了它们自己的规则。

##### 10.2.1.1. allOf

<details>
  <summary>Expand original text</summary>

This keyword's value MUST be a non-empty array. Each item of the array MUST be a valid JSON Schema.

An instance validates successfully against this keyword if it validates successfully against all schemas defined by this keyword's value.
</details>

此关键字的值必须（MUST）是非空数组。数组的每个项目必须（MUST）是有效的 JSON 模式。

如果一个实例成功地验证了此关键字的值所定义的所有模式，则它成功地验证了此关键字。

##### 10.2.1.2. anyOf

<details>
  <summary>Expand original text</summary>

This keyword's value MUST be a non-empty array. Each item of the array MUST be a valid JSON Schema.

An instance validates successfully against this keyword if it validates successfully against at least one schema defined by this keyword's value. Note that when annotations are being collected, all subschemas MUST be examined so that annotations are collected from each subschema that validates successfully.
</details>

此关键字的值必须（MUST）是非空数组。数组的每个项目必须（MUST）是有效的 JSON 模式。

如果一个实例成功地验证了此关键字的值所定义的至少一个模式，则它成功地验证了此关键字。请注意，当正在收集注释时，必须检查所有子模式，以便从每个成功验证的子模式中收集注释。

##### 10.2.1.3. oneOf

<details>
  <summary>Expand original text</summary>

This keyword's value MUST be a non-empty array. Each item of the array MUST be a valid JSON Schema.

An instance validates successfully against this keyword if it validates successfully against exactly one schema defined by this keyword's value.
</details>

此关键字的值必须（MUST）是非空数组。数组的每个项目必须（MUST）是有效的 JSON 模式。

如果一个实例成功地验证了此关键字的值所定义的恰好一个模式，则它成功地验证了此关键字。

##### 10.2.1.4. not

<details>
  <summary>Expand original text</summary>

This keyword's value MUST be a valid JSON Schema.

An instance is valid against this keyword if it fails to validate successfully against the schema defined by this keyword.
</details>

此关键字的值必须（MUST）是有效的 JSON 模式。

如果一个实例未能成功地验证此关键字所定义的模式，则它对此关键字有效。

#### 10.2.2. 根据条件应用子模式的关键字（Keywords for Applying Subschemas Conditionally）

<details>
  <summary>Expand original text</summary>

Three of these keywords work together to implement conditional application of a subschema based on the outcome of another subschema. The fourth is a shortcut for a specific conditional case.

"if", "then", and "else" MUST NOT interact with each other across subschema boundaries. In other words, an "if" in one branch of an "allOf" MUST NOT have an impact on a "then" or "else" in another branch.

There is no default behavior for "if", "then", or "else" when they are not present. In particular, they MUST NOT be treated as if present with an empty schema, and when "if" is not present, both "then" and "else" MUST be entirely ignored.
</details>

这三个关键字共同实现了基于另一个子模式的结果有条件地应用子模式。第四个是特定条件情况的快捷方式。

`if`、`then` 和 `else` 必须不（MUST NOT）在子模式边界之间相互作用。换句话说，`allOf` 中的一个分支中的 `if` 不得（MUST NOT）对另一个分支中的 `then` 或 `else` 产生影响。

当 `if`、`then` 或 `else` 不出现时，没有默认行为。特别地，它们必须不（MUST NOT）被视为带有空模式的存在，当 `if` 不出现时， `then` 和 `else` 必须（MUST）完全被忽略。

##### 10.2.2.1. if

<details>
  <summary>Expand original text</summary>

This keyword's value MUST be a valid JSON Schema.

This validation outcome of this keyword's subschema has no direct effect on the overall validation result. Rather, it controls which of the "then" or "else" keywords are evaluated.

Instances that successfully validate against this keyword's subschema MUST also be valid against the subschema value of the "then" keyword, if present.

Instances that fail to validate against this keyword's subschema MUST also be valid against the subschema value of the "else" keyword, if present.

If annotations (Section 7.7) are being collected, they are collected from this keyword's subschema in the usual way, including when the keyword is present without either "then" or "else".
</details>

此关键字的值必须（MUST）是有效的 JSON 模式。

此关键字子模式的验证结果对整体验证结果没有直接影响。相反，它控制评估 `then` 或 `else` 关键字中的哪一个。

成功验证此关键字子模式的实例也必须（MUST）对 `then` 关键字的子模式值（如果存在）有效。

未能验证此关键字子模式的实例也必须（MUST）对 `else` 关键字的子模式值（如果存在）有效。

如果正在收集注释（第 7.7 节），则以通常的方式从该关键字的子模式中收集，包括在没有 `then` 或 `else` 的情况下出现该关键字。

##### 10.2.2.2. then

<details>
  <summary>Expand original text</summary>

This keyword's value MUST be a valid JSON Schema.

When "if" is present, and the instance successfully validates against its subschema, then validation succeeds against this keyword if the instance also successfully validates against this keyword's subschema.

This keyword has no effect when "if" is absent, or when the instance fails to validate against its subschema. Implementations MUST NOT evaluate the instance against this keyword, for either validation or annotation collection purposes, in such cases.
</details>

此关键字的值必须（MUST）是有效的 JSON 模式。

当 `if` 存在且实例成功验证其子模式时，如果实例还成功验证此关键字的子模式，则验证成功。

当 `if` 不存在，或者实例未能验证其子模式时，此关键字无效。在这种情况下，实现必须不（MUST NOT）针对此关键字评估实例，无论是用于验证还是注释收集。

##### 10.2.2.3. else

<details>
  <summary>Expand original text</summary>

This keyword's value MUST be a valid JSON Schema.

When "if" is present, and the instance fails to validate against its subschema, then validation succeeds against this keyword if the instance successfully validates against this keyword's subschema.

This keyword has no effect when "if" is absent, or when the instance successfully validates against its subschema. Implementations MUST NOT evaluate the instance against this keyword, for either validation or annotation collection purposes, in such cases.
</details>

此关键字的值必须（MUST）是有效的 JSON 模式。

当 `if` 存在且实例未能验证其子模式时，如果实例成功验证此关键字的子模式，则验证成功。

当 `if` 不存在，或者实例成功验证其子模式时，此关键字无效。在这种情况下，实现必须不（MUST NOT）针对此关键字评估实例，无论是用于验证还是注释收集。

##### 10.2.2.4. dependentSchemas

<details>
  <summary>Expand original text</summary>

This keyword specifies subschemas that are evaluated if the instance is an object and contains a certain property.

This keyword's value MUST be an object. Each value in the object MUST be a valid JSON Schema.

If the object key is a property in the instance, the entire instance must validate against the subschema. Its use is dependent on the presence of the property.

Omitting this keyword has the same behavior as an empty object.
</details>

此关键字指定了如果实例是对象并包含某个属性，则对其进行评估的子模式。

此关键字的值必须（MUST）是一个对象。对象中的每个值必须（MUST）是有效的 JSON 模式。

如果对象键是实例中的属性，则整个实例必须验证该子模式。它的使用取决于属性的存在。

省略此关键字的行为与空对象相同。

### 10.3. 将子模式应用于子实例的关键字（Keywords for Applying Subschemas to Child Instances）

<details>
  <summary>Expand original text</summary>

Each of these keywords defines a rule for applying its subschema(s) to child instances, specifically object properties and array items, and combining their results.
</details>

这些关键字中的每一个都定义了一个将其子模式应用于子实例的规则，具体来说是对象属性和数组项，并合并它们的结果。

#### 10.3.1. 将子模式应用于数组的关键字（Keywords for Applying Subschemas to Arrays）
##### 10.3.1.1. prefixItems

<details>
  <summary>Expand original text</summary>

The value of "prefixItems" MUST be a non-empty array of valid JSON Schemas.

Validation succeeds if each element of the instance validates against the schema at the same position, if any. This keyword does not constrain the length of the array. If the array is longer than this keyword's value, this keyword validates only the prefix of matching length.

This keyword produces an annotation value which is the largest index to which this keyword applied a subschema. The value MAY be a boolean true if a subschema was applied to every index of the instance, such as is produced by the "items" keyword. This annotation affects the behavior of "items" and "unevaluatedItems".

Omitting this keyword has the same assertion behavior as an empty array.
</details>

`prefixItems` 的值必须（MUST）是有效 JSON 模式的非空数组。

如果实例的每个元素与相同位置的模式（如果有的话）都验证成功，则验证成功。此关键字不限制数组的长度。如果数组长度大于此关键字的值，此关键字仅验证匹配长度的前缀。

此关键字产生一个注释值，该值是应用了子模式的最大索引。如果应用了一个子模式到实例的每个索引，例如通过 `items` 关键字产生的，那么该值可以是布尔值 `true`。此注释会影响 `items` 和 `unevaluatedItems` 的行为。

省略此关键字具有与空数组相同的断言行为。

##### 10.3.1.2. items

<details>
  <summary>Expand original text</summary>

The value of "items" MUST be a valid JSON Schema.

This keyword applies its subschema to all instance elements at indexes greater than the length of the "prefixItems" array in the same schema object, as reported by the annotation result of that "prefixItems" keyword. If no such annotation result exists, "items" applies its subschema to all instance array elements. Note that the behavior of "items" without "prefixItems" is identical to that of the schema form of "items" in prior drafts. When "prefixItems" is present, the behavior of "items" is identical to the former "additionalItems" keyword.

If the "items" subschema is applied to any positions within the instance array, it produces an annotation result of boolean true, indicating that all remaining array elements have been evaluated against this keyword's subschema. This annotation affects the behavior of "unevaluatedItems" in the Unevaluated vocabulary.

Omitting this keyword has the same assertion behavior as an empty schema.

Implementations MAY choose to implement or optimize this keyword in another way that produces the same effect, such as by directly checking for the presence and size of a "prefixItems" array. Implementations that do not support annotation collection MUST do so.
</details>

`items` 的值必须（MUST）是一个有效的 JSON 模式。

此关键字将其子模式应用于实例元素在大于相同模式对象中的 `prefixItems` 数组长度的索引处，如该 `prefixItems` 关键字的注释结果所报告。如果没有这样的注释结果， `items` 将其子模式应用于所有实例数组元素。请注意，没有 `prefixItems` 的 `items` 的行为与先前草案中的 `items` 的模式形式相同。当存在 `prefixItems` 时， `items` 的行为与以前的 `additionalItems` 关键字相同。

如果 `items` 子模式应用于实例数组内的任何位置，它会产生一个布尔值 `true` 的注释结果，表示已针对此关键字的子模式评估了所有剩余的数组元素。这个注释影响了未评估词汇表中 `unevaluatedItems` 的行为。

省略此关键字具有与空模式相同的断言行为。

实现可以（MAY）选择以另一种方式实现或优化此关键字以产生相同的效果，例如通过直接检查 `prefixItems` 数组的存在和大小。不支持注释收集的实现必须（MUST）这样做。

##### 10.3.1.3. contains

<details>
  <summary>Expand original text</summary>

The value of this keyword MUST be a valid JSON Schema.

An array instance is valid against "contains" if at least one of its elements is valid against the given schema, except when "minContains" is present and has a value of 0, in which case an array instance MUST be considered valid against the "contains" keyword, even if none of its elements is valid against the given schema.

This keyword produces an annotation value which is an array of the indexes to which this keyword validates successfully when applying its subschema, in ascending order. The value MAY be a boolean "true" if the subschema validates successfully when applied to every index of the instance. The annotation MUST be present if the instance array to which this keyword's schema applies is empty.

This annotation affects the behavior of "unevaluatedItems" in the Unevaluated vocabulary, and MAY also be used to implement the "minContains" and "maxContains" keywords in the Validation vocabulary.

The subschema MUST be applied to every array element even after the first match has been found, in order to collect annotations for use by other keywords. This is to ensure that all possible annotations are collected.
</details>

此关键字的值必须（MUST）是有效的 JSON 模式。

如果数组实例中至少有一个元素根据给定的模式验证成功，则该实例对 `contains` 有效，但当 `minContains` 存在并且值为0时，数组实例必须被认为对 `contains` 关键字有效，即使它的所有元素都没有根据给定的模式验证成功。

此关键字产生一个注释值，该值是一个数组，其中包含应用其子模式时此关键字成功验证的索引，按升序排列。如果子模式在应用到实例的每个索引时都验证成功，那么该值可以（MAY）是布尔值 `true`。如果此关键字的模式应用的实例数组为空，则注释必须（MUST）存在。

此注释影响了未评估词汇表中 `unevaluatedItems` 的行为，并且还可以用于实现验证词汇表中的 `minContains` 和 `maxContains` 关键字。

在找到第一个匹配之后，子模式必须（MUST）应用于每个数组元素，以便为其它关键字收集注释。这是为了确保收集到所有可能的注释。

#### 10.3.2. 应用子模式到对象的关键字（Keywords for Applying Subschemas to Objects）
##### 10.3.2.1. properties

<details>
  <summary>Expand original text</summary>

The value of "properties" MUST be an object. Each value of this object MUST be a valid JSON Schema.

Validation succeeds if, for each name that appears in both the instance and as a name within this keyword's value, the child instance for that name successfully validates against the corresponding schema.

The annotation result of this keyword is the set of instance property names matched by this keyword. This annotation affects the behavior of "additionalProperties" (in this vocabulary) and "unevaluatedProperties" in the Unevaluated vocabulary.

Omitting this keyword has the same assertion behavior as an empty object.
</details>

`properties` 的值必须（MUST）是一个对象。此对象的每个值必须是一个有效的 JSON 模式。

如果在实例和此关键字值内的名称中同时出现的每个名称，该名称的子实例都能成功验证对应的模式，则验证成功。

此关键字的注释结果是由此关键字匹配的实例属性名称集合。此注释影响了此词汇表中 `additionalProperties` 的行为和未评估词汇表中 `unevaluatedProperties` 的行为。

省略此关键字具有与空对象相同的断言行为。

##### 10.3.2.2. patternProperties

<details>
  <summary>Expand original text</summary>

The value of "patternProperties" MUST be an object. Each property name of this object SHOULD be a valid regular expression, according to the ECMA-262 regular expression dialect. Each property value of this object MUST be a valid JSON Schema.

Validation succeeds if, for each instance name that matches any regular expressions that appear as a property name in this keyword's value, the child instance for that name successfully validates against each schema that corresponds to a matching regular expression.

The annotation result of this keyword is the set of instance property names matched by this keyword. This annotation affects the behavior of "additionalProperties" (in this vocabulary) and "unevaluatedProperties" (in the Unevaluated vocabulary).

Omitting this keyword has the same assertion behavior as an empty object.
</details>

`patternProperties` 的值必须（MUST）是一个对象。此对象的每个属性名应该是一个有效的正则表达式，根据 ECMA-262 正则表达式方言。此对象的每个属性值必须是一个有效的 JSON 模式。

如果实例名称与出现在此关键字值的属性名中的任何正则表达式匹配，那么该名称的子实例成功验证与匹配的正则表达式相对应的每个模式，则验证成功。

此关键字的注释结果是由此关键字匹配的实例属性名称集合。此注释影响了此词汇表中 `additionalProperties` 的行为和未评估词汇表中 `unevaluatedProperties` 的行为。

省略此关键字具有与空对象相同的断言行为。

##### 10.3.2.3. additionalProperties

<details>
  <summary>Expand original text</summary>

The value of "additionalProperties" MUST be a valid JSON Schema.

The behavior of this keyword depends on the presence and annotation results of "properties" and "patternProperties" within the same schema object. Validation with "additionalProperties" applies only to the child values of instance names that do not appear in the annotation results of either "properties" or "patternProperties".

For all such properties, validation succeeds if the child instance validates against the "additionalProperties" schema.

The annotation result of this keyword is the set of instance property names validated by this keyword's subschema. This annotation affects the behavior of "unevaluatedProperties" in the Unevaluated vocabulary.

Omitting this keyword has the same assertion behavior as an empty schema.

Implementations MAY choose to implement or optimize this keyword in another way that produces the same effect, such as by directly checking the names in "properties" and the patterns in "patternProperties" against the instance property set. Implementations that do not support annotation collection MUST do so. In defining this option, it seems there is the potential for ambiguity in the output format. The ambiguity does not affect validation results, but it does affect the resulting output format. The ambiguity allows for multiple valid output results depending on whether annotations are used or a solution that "produces the same effect" as draft-07. It is understood that annotations from failing schemas are dropped. See our [Decision Record](https://github.com/json-schema-org/json-schema-spec/tree/HEAD/adr/2022-04-08-cref-for-ambiguity-and-fix-later-gh-spec-issue-1172.md) for further details.
</details>

`additionalProperties` 的值必须（MUST）是一个有效的 JSON 模式。

此关键字的行为取决于同一模式对象中 `properties` 和 `patternProperties` 的存在和注释结果。带有 `additionalProperties` 的验证仅适用于实例名称的子值，这些名称不出现在 `properties` 或 `patternProperties` 的注释结果中。

对于所有这样的属性，如果子实例通过 `additionalProperties` 模式验证，则验证成功。

此关键字的注释结果是由此关键字的子模式验证的实例属性名称集合。此注释影响了未评估词汇表中 `unevaluatedProperties` 的行为。

省略此关键字具有与空模式相同的断言行为。

实现可以（MAY）选择以产生相同效果的另一种方式来实现或优化此关键字，例如直接将 `properties` 中的名称和 `patternProperties` 中的模式与实例属性集合进行比较。不支持注释收集的实现必须（MUST）这样做。在定义此选项时，似乎输出格式存在潜在的模糊性。这种模糊性不影响验证结果，但会影响生成的输出格式。这种模糊性允许根据是否使用注释或产生与草案-07相同效果的解决方案，产生多个有效的输出结果。已知来自失败模式的注释被丢弃。有关详细信息，请参阅我们的[决策记录](https://github.com/json-schema-org/json-schema-spec/tree/HEAD/adr/2022-04-08-cref-for-ambiguity-and-fix-later-gh-spec-issue-1172.md)。

##### 10.3.2.4. propertyNames

<details>
  <summary>Expand original text</summary>

The value of "propertyNames" MUST be a valid JSON Schema.

If the instance is an object, this keyword validates if every property name in the instance validates against the provided schema. Note the property name that the schema is testing will always be a string.

Omitting this keyword has the same behavior as an empty schema.
</details>

`propertyNames` 的值必须（MUST）是一个有效的 JSON 模式。

如果实例是一个对象，当实例中的每个属性名称都符合提供的模式时，此关键字验证通过。请注意，模式正在测试的属性名称将始终是一个字符串。

省略此关键字具有与空模式相同的行为。

## 11. 用于未评估位置的词汇表（A Vocabulary for Unevaluated Locations）

<details>
  <summary>Expand original text</summary>

The purpose of these keywords is to enable schema authors to apply subschemas to array items or object properties that have not been successfully evaluated against any dynamic-scope subschema of any adjacent keywords.

These instance items or properties may have been unsuccessfully evaluated against one or more adjacent keyword subschemas, such as when an assertion in a branch of an "anyOf" fails. Such failed evaluations are not considered to contribute to whether or not the item or property has been evaluated. Only successful evaluations are considered.

If an item in an array or an object property is "successfully evaluated", it is logically considered to be valid in terms of the representation of the object or array that's expected. For example if a subschema represents a car, which requires between 2-4 wheels, and the value of "wheels" is 6, the instance object is not "evaluated" to be a car, and the "wheels" property is considered "unevaluated (successfully as a known thing)", and does not retain any annotations.

Recall that adjacent keywords are keywords within the same schema object, and that the dynamic-scope subschemas include reference targets as well as lexical subschemas.

The behavior of these keywords depend on the annotation results of adjacent keywords that apply to the instance location being validated.

Meta-schemas that do not use "$vocabulary" SHOULD be considered to require this vocabulary as if its URI were present with a value of true.

The current URI for this vocabulary, known as the Unevaluated Applicator vocabulary, is: <https://json-schema.org/draft/2020-12/vocab/unevaluated>.

The current URI for the corresponding meta-schema is: https://json-schema.org/draft/2020-12/meta/unevaluated.
</details>

这些关键字的目的是使模式作者能够将子模式应用于未成功针对任何相邻关键字的任何动态作用域子模式进行评估的数组项或对象属性。

这些实例项或属性可能已经针对一个或多个相邻关键字子模式进行了不成功的评估，例如在 `anyOf` 的一个分支中的断言失败时。这样的失败评估不被认为有助于判断项目或属性是否被评估。只有成功的评估才被考虑。

如果数组中的一个项目或对象属性是“成功评估”（successfully evaluated）的，则在逻辑上认为它在表示所期望的对象或数组方面是有效的。例如，如果一个子模式表示一辆汽车，需要 2-4 个轮子，而“轮子”的值是 6，那么实例对象不会被“评估”为一辆汽车，“轮子”属性被认为是“未评估（作为已知事物成功）”（unevaluated (successfully as a known thing)），并且不保留任何注释。

请回忆一下，相邻关键字是在同一模式对象中的关键字，动态作用域子模式包括引用目标以及词法子模式。

这些关键字的行为取决于应用于正在验证的实例位置的相邻关键字的注释结果。

不使用 `$vocabulary` 的元模式应该（SHOULD）被认为需要此词汇表，就像其 URI 存在并具有 true 值一样。

此词汇表的当前 URI，称为 Unevaluated Applicator 词汇表，是：<https://json-schema.org/draft/2020-12/vocab/unevaluated>。

相应元模式的当前 URI 是：https://json-schema.org/draft/2020-12/meta/unevaluated。

### 11.1. 关键字独立性（Keyword Independence）

<details>
  <summary>Expand original text</summary>

Schema keywords typically operate independently, without affecting each other's outcomes. However, the keywords in this vocabulary are notable exceptions:

- "unevaluatedItems", whose behavior is defined in terms of annotations from "prefixItems", "items", "contains", and itself
- "unevaluatedProperties", whose behavior is defined in terms of annotations from "properties", "patternProperties", "additionalProperties" and itself

</details>

模式关键字通常独立操作，不影响彼此的结果。然而，本词汇表中的关键字是值得注意的例外：

- `unevaluatedItems`，其行为是根据来自 `prefixItems`、`items`、`contains` 以及其本身的注释定义的
- `unevaluatedProperties`，其行为是根据来自 `properties`、`patternProperties`、`additionalProperties` 和其本身的注释定义的

### 11.2. unevaluatedItems

<details>
  <summary>Expand original text</summary>

The value of "unevaluatedItems" MUST be a valid JSON Schema.

The behavior of this keyword depends on the annotation results of adjacent keywords that apply to the instance location being validated. Specifically, the annotations from "prefixItems", "items", and "contains", which can come from those keywords when they are adjacent to the "unevaluatedItems" keyword. Those three annotations, as well as "unevaluatedItems", can also result from any and all adjacent in-place applicator (Section 10.2) keywords. This includes but is not limited to the in-place applicators defined in this document.

If no relevant annotations are present, the "unevaluatedItems" subschema MUST be applied to all locations in the array. If a boolean true value is present from any of the relevant annotations, "unevaluatedItems" MUST be ignored. Otherwise, the subschema MUST be applied to any index greater than the largest annotation value for "prefixItems", which does not appear in any annotation value for "contains".

This means that "prefixItems", "items", "contains", and all in-place applicators MUST be evaluated before this keyword can be evaluated. Authors of extension keywords MUST NOT define an in-place applicator that would need to be evaluated after this keyword.

If the "unevaluatedItems" subschema is applied to any positions within the instance array, it produces an annotation result of boolean true, analogous to the behavior of "items". This annotation affects the behavior of "unevaluatedItems" in parent schemas.

Omitting this keyword has the same assertion behavior as an empty schema.
</details>

`unevaluatedItems` 的值必须（MUST）是有效的 JSON 模式。

这个关键字的行为取决于应用于正在验证的实例位置的相邻关键字的注释结果。具体来说，来自 `prefixItems`、`items` 和 `contains` 的注释，它们可以在与 `unevaluatedItems` 关键字相邻时来自这些关键字。这三个注释以及 `unevaluatedItems` 还可以来自任何和所有相邻的就地应用器（第10.2节）关键字。这包括但不限于本文档中定义的就地应用器。

如果没有相关注释存在，那么 `unevaluatedItems` 子模式必须（MUST）应用于数组中的所有位置。如果从任何相关注释中出现布尔真值，`unevaluatedItems` 必须（MUST）被忽略。否则，子模式必须（MUST）应用于大于 `prefixItems` 的最大注释值的任何索引，这些索引不出现在 `contains` 的任何注释值中。

这意味着在评估这个关键字之前，必须（MUST）评估 `prefixItems`、`items`、`contains` 和所有就地应用器。扩展关键字的作者必须不（MUST NOT）定义一个需要在这个关键字之后评估的就地应用器。

如果 `unevaluatedItems` 子模式应用于实例数组中的任何位置，它会产生一个布尔真值的注释结果，类似于 `items` 的行为。这个注释影响了父模式中 `unevaluatedItems` 的行为。

省略这个关键字与空模式具有相同的断言行为。

### 11.3. unevaluatedProperties

<details>
  <summary>Expand original text</summary>

The value of "unevaluatedProperties" MUST be a valid JSON Schema.

The behavior of this keyword depends on the annotation results of adjacent keywords that apply to the instance location being validated. Specifically, the annotations from "properties", "patternProperties", and "additionalProperties", which can come from those keywords when they are adjacent to the "unevaluatedProperties" keyword. Those three annotations, as well as "unevaluatedProperties", can also result from any and all adjacent in-place applicator (Section 10.2) keywords. This includes but is not limited to the in-place applicators defined in this document.

Validation with "unevaluatedProperties" applies only to the child values of instance names that do not appear in the "properties", "patternProperties", "additionalProperties", or "unevaluatedProperties" annotation results that apply to the instance location being validated.

For all such properties, validation succeeds if the child instance validates against the "unevaluatedProperties" schema.

This means that "properties", "patternProperties", "additionalProperties", and all in-place applicators MUST be evaluated before this keyword can be evaluated. Authors of extension keywords MUST NOT define an in-place applicator that would need to be evaluated after this keyword.

The annotation result of this keyword is the set of instance property names validated by this keyword's subschema. This annotation affects the behavior of "unevaluatedProperties" in parent schemas.

Omitting this keyword has the same assertion behavior as an empty schema.
</details>

`unevaluatedProperties` 的值必须（MUST）是有效的 JSON 模式。

这个关键字的行为取决于应用于正在验证的实例位置的相邻关键字的注释结果。具体来说，来自 `properties`、`patternProperties` 和 `additionalProperties` 的注释，它们可以在与 `unevaluatedProperties` 关键字相邻时来自这些关键字。这三个注释以及 `unevaluatedProperties` 还可以来自任何和所有相邻的就地应用器（第10.2节）关键字。这包括但不限于本文档中定义的就地应用器。

使用 `unevaluatedProperties` 进行验证仅适用于在 `properties`、`patternProperties`、`additionalProperties` 或 `unevaluatedProperties` 注释结果中未出现的实例名称的子值，这些注释结果适用于正在验证的实例位置。

对于所有这样的属性，如果子实例通过 `unevaluatedProperties` 模式验证，则验证成功。

这意味着在评估这个关键字之前，必须（MUST）评估 `properties`、`patternProperties`、`additionalProperties` 和所有就地应用器。扩展关键字的作者必须不（MUST NOT）定义一个需要在这个关键字之后评估的就地应用器。

这个关键字的注释结果是由这个关键字的子模式验证的实例属性名称集合。这个注释影响了父模式中 `unevaluatedProperties` 的行为。

省略这个关键字与空模式具有相同的断言行为。

## 12. 输出格式化（Output Formatting）

<details>
  <summary>Expand original text</summary>

JSON Schema is defined to be platform-independent. As such, to increase compatibility across platforms, implementations SHOULD conform to a standard validation output format. This section describes the minimum requirements that consumers will need to properly interpret validation results.
</details>

JSON Schema 被定义为平台无关的。为了提高各个平台之间的兼容性，实现应该遵循标准的验证输出格式。本节描述了消费者为了正确解释验证结果所需的最低要求。

### 12.1. 格式（Format）

<details>
  <summary>Expand original text</summary>

JSON Schema output is defined using the JSON Schema data instance model as described in section 4.2.1. Implementations MAY deviate from this as supported by their specific languages and platforms, however it is RECOMMENDED that the output be convertible to the JSON format defined herein via serialization or other means.
</details>

JSON Schema 输出是使用 JSON Schema 数据实例模型定义的，如第4.2.1节所述。实现可以（MAY）根据其特定语言和平台的支持偏离这一点，但建议（RECOMMENDED）输出可以通过序列化或其它方式转换为本文中定义的 JSON 格式。

### 12.2. 输出格式（Output Formats）

<details>
  <summary>Expand original text</summary>

This specification defines four output formats. See the "Output Structure" section for the requirements of each format.

- Flag - A boolean which simply indicates the overall validation result with no further details.
- Basic - Provides validation information in a flat list structure.
- Detailed - Provides validation information in a condensed hierarchical structure based on the structure of the schema.
- Verbose - Provides validation information in an uncondensed hierarchical structure that matches the exact structure of the schema.

An implementation SHOULD provide at least one of the "flag", "basic", or "detailed" format and MAY provide the "verbose" format. If it provides one or more of the "detailed" or "verbose" formats, it MUST also provide the "flag" format. Implementations SHOULD specify in their documentation which formats they support.
</details>

本规范定义了四种输出格式。请参阅“输出结构”部分了解每种格式的要求。

- 标志（Flag）：一个布尔值，简单地表示总体验证结果，没有进一步的细节。
- 基本（Basic）：以平面列表结构提供验证信息。
- 详细（Detailed）：根据模式的结构，以简化的层次结构提供验证信息。
- 冗长（Verbose）：以与模式的确切结构相匹配的未压缩层次结构提供验证信息。

实现应（SHOULD）至少提供 "Flag"、"Basic" 或 "Detailed" 格式中的一个，并且可以（MAY）提供 "Verbose" 格式。如果提供 "Detailed" 或 "Verbose" 格式中的一个或多个，那么也必须（MUST）提供 "Flag" 格式。实现应（SHOULD）在其文档中指定它们支持哪些格式。

### 12.3. 最低信息（Minimum Information）

<details>
  <summary>Expand original text</summary>

Beyond the simplistic "flag" output, additional information is useful to aid in debugging a schema or instance. Each sub-result SHOULD contain the information contained within this section at a minimum.

A single object that contains all of these components is considered an output unit.

Implementations MAY elect to provide additional information.
</details>

除了简单的 "Flag" 输出之外，其它信息对于调试模式或实例也是有用的。每个子结果至少应（SHOULD）包含本节中包含的信息。

包含所有这些组件的单个对象被视为一个输出单元。

实现可以（MAY）选择提供其它信息。

#### 12.3.1. 关键字相对位置（Keyword Relative Location）

<details>
  <summary>Expand original text</summary>

The relative location of the validating keyword that follows the validation path. The value MUST be expressed as a JSON Pointer, and it MUST include any by-reference applicators such as "$ref" or "$dynamicRef".

```txt
/properties/width/$ref/minimum
```

Note that this pointer may not be resolvable by the normal JSON Pointer process due to the inclusion of these by-reference applicator keywords.

The JSON key for this information is "keywordLocation".
</details>

验证关键字的相对位置，遵循验证路径。该值必须（MUST）表示为 JSON Pointer，并且必须（MUST）包括 `$ref` 或 `$dynamicRef` 等引用定位器。

```txt
/properties/width/$ref/minimum
```

请注意，由于包含这些引用定位器关键字，该指针可能无法通过正常的 JSON Pointer 过程解析。

此信息的 JSON 键名为 `keywordLocation`。

#### 12.3.2. 关键字绝对位置（Keyword Absolute Location）

<details>
  <summary>Expand original text</summary>

The absolute, dereferenced location of the validating keyword. The value MUST be expressed as a full URI using the canonical URI of the relevant schema resource with a JSON Pointer fragment, and it MUST NOT include by-reference applicators such as "$ref" or "$dynamicRef" as non-terminal path components. It MAY end in such keywords if the error or annotation is for that keyword, such as an unresolvable reference. Note that "absolute" here is in the sense of "absolute filesystem path" (meaning the complete location) rather than the "absolute-URI" terminology from RFC 3986 (meaning with scheme but without fragment). Keyword absolute locations will have a fragment in order to identify the keyword.

```txt
https://example.com/schemas/common#/$defs/count/minimum
```

This information MAY be omitted only if either the dynamic scope did not pass over a reference or if the schema does not declare an absolute URI as its "$id".

The JSON key for this information is "absoluteKeywordLocation".
</details>

验证关键字的绝对、取消引用的位置。该值必须（MUST）表示为一个完整的 URI，使用相关模式资源的规范 URI 和一个 JSON Pointer 片段表示，且不得（MUST NOT）将 `$ref` 或 `$dynamicRef` 等引用定位器作为非终端路径组件包括在内。如果错误或注释针对的是该关键字，例如无法解析的引用，则可能（MAY）以这样的关键字结束。请注意，这里的“绝对”是指“绝对文件系统路径”（absolute filesystem path）（意味着完整位置），而不是 RFC 3986 中的 `absolute-URI` 术语（意味着带有方案但没有片段）。关键字绝对位置将具有一个片段以便识别关键字。

```txt
https://example.com/schemas/common#/$defs/count/minimum
```

仅当动态范围没有跨越引用或模式没有声明一个绝对 URI 作为其 `$id` 时，才可以（MAY）省略此信息。

此信息的 JSON 键名为 `absoluteKeywordLocation`。

#### 12.3.3. 实例位置（Instance Location）

<details>
  <summary>Expand original text</summary>

The location of the JSON value within the instance being validated. The value MUST be expressed as a JSON Pointer.

The JSON key for this information is "instanceLocation".
</details>

正在验证的实例中 JSON 值的位置。该值必须（MUST）表示为 JSON Pointer。

此信息的 JSON 键名为 `instanceLocation`。

#### 12.3.4. 错误或注解（Error or Annotation）

<details>
  <summary>Expand original text</summary>

The error or annotation that is produced by the validation.

For errors, the specific wording for the message is not defined by this specification. Implementations will need to provide this.

For annotations, each keyword that produces an annotation specifies its format. By default, it is the keyword's value.

The JSON key for failed validations is "error"; for successful validations it is "annotation".
</details>

由验证产生的错误或注解。

对于错误，本规范没有定义消息的具体措辞。实现将需要提供此信息。

对于注解，产生注解的每个关键字都指定其格式。默认情况下，它是关键字的值。

失败验证的 JSON 键名为 `error`；成功验证的键名为 `annotation`。

#### 12.3.5. 嵌套结果（Nested Results）

<details>
  <summary>Expand original text</summary>

For the two hierarchical structures, this property will hold nested errors and annotations.

The JSON key for nested results in failed validations is "errors"; for successful validations it is "annotations". Note the plural forms, as a keyword with nested results can also have a local error or annotation.
</details>

对于两种层次结构，此属性将包含嵌套的错误和注解。

失败验证中嵌套结果的 JSON 键名为 `errors`；成功验证的键名为 `annotations`。注意复数形式，因为具有嵌套结果的关键字也可以具有本地错误或注解。

### 12.4. 输出结构（Output Structure）

<details>
  <summary>Expand original text</summary>

The output MUST be an object containing a boolean property named "valid". When additional information about the result is required, the output MUST also contain "errors" or "annotations" as described below.

- "valid" - a boolean value indicating the overall validation success or failure
- "errors" - the collection of errors or annotations produced by a failed validation
- "annotations" - the collection of errors or annotations produced by a successful validation

For these examples, the following schema and instance will be used.

```json
{
  "$id": "https://example.com/polygon",
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$defs": {
    "point": {
      "type": "object",
      "properties": {
        "x": { "type": "number" },
        "y": { "type": "number" }
      },
      "additionalProperties": false,
      "required": [ "x", "y" ]
    }
  },
  "type": "array",
  "items": { "$ref": "#/$defs/point" },
  "minItems": 3
}

[
  {
    "x": 2.5,
    "y": 1.3
  },
  {
    "x": 1,
    "z": 6.7
  }
]
```

This instance will fail validation and produce errors, but it's trivial to deduce examples for passing schemas that produce annotations.

Specifically, the errors it will produce are:

- The second object is missing a "y" property.
- The second object has a disallowed "z" property.
- There are only two objects, but three are required.

Note that the error message wording as depicted in these examples is not a requirement of this specification. Implementations SHOULD craft error messages tailored for their audience or provide a templating mechanism that allows their users to craft their own messages.
</details>

输出必须（MUST）是一个包含名为 `valid` 的布尔属性的对象。当需要关于结果的其它信息时，输出还必须（MUST）包含下文所述的 `errors` 或 `annotations`。

- `valid` - 表示整体验证成功或失败的布尔值
- `errors` - 失败验证产生的错误或注解集合
- `annotations` - 成功验证产生的错误或注解集合

对于这些示例，将使用以下模式和实例。

```json
{
  "$id": "https://example.com/polygon",
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$defs": {
    "point": {
      "type": "object",
      "properties": {
        "x": { "type": "number" },
        "y": { "type": "number" }
      },
      "additionalProperties": false,
      "required": [ "x", "y" ]
    }
  },
  "type": "array",
  "items": { "$ref": "#/$defs/point" },
  "minItems": 3
}

[
  {
    "x": 2.5,
    "y": 1.3
  },
  {
    "x": 1,
    "z": 6.7
  }
]
```

此实例将无法通过验证并产生错误，但可以轻松推断出通过模式生成注解的示例。

具体来说，它将产生的错误包括：

- 第二个对象缺少 "y" 属性。
- 第二个对象具有不允许的 "z" 属性。
- 只有两个对象，但需要三个。

请注意，这些示例中描绘的错误消息措辞不是本规范的要求。实现应为（SHOULD）其受众定制错误消息，或提供一个模板机制，允许用户定制自己的消息。

#### 12.4.1. 标志（Flag）

<details>
  <summary>Expand original text</summary>

In the simplest case, merely the boolean result for the "valid" valid property needs to be fulfilled.

```json
{
  "valid": false
}
```

Because no errors or annotations are returned with this format, it is RECOMMENDED that implementations use short-circuiting logic to return failure or success as soon as the outcome can be determined. For example, if an "anyOf" keyword contains five sub-schemas, and the second one passes, there is no need to check the other three. The logic can simply return with success.
</details>

在最简单的情况下，只需满足 `valid` 属性的布尔结果。

```json
{
  "valid": false
}
```

因为这种格式不会返回错误或注解，所以建议（RECOMMENDED）实现使用短路逻辑在可以确定结果的情况下尽快返回失败或成功。例如，如果 `anyOf` 关键字包含五个子模式，并且第二个通过了，那么就没有必要检查其它三个。逻辑可以简单地返回成功。

#### 12.4.2. 基本（Basic）

<details>
  <summary>Expand original text</summary>

The "Basic" structure is a flat list of output units.

```json
{
  "valid": false,
  "errors": [
    {
      "keywordLocation": "",
      "instanceLocation": "",
      "error": "A subschema had errors."
    },
    {
      "keywordLocation": "/items/$ref",
      "absoluteKeywordLocation":
        "https://example.com/polygon#/$defs/point",
      "instanceLocation": "/1",
      "error": "A subschema had errors."
    },
    {
      "keywordLocation": "/items/$ref/required",
      "absoluteKeywordLocation":
        "https://example.com/polygon#/$defs/point/required",
      "instanceLocation": "/1",
      "error": "Required property 'y' not found."
    },
    {
      "keywordLocation": "/items/$ref/additionalProperties",
      "absoluteKeywordLocation":
        "https://example.com/polygon#/$defs/point/additionalProperties",
      "instanceLocation": "/1/z",
      "error": "Additional property 'z' found but was invalid."
    },
    {
      "keywordLocation": "/minItems",
      "instanceLocation": "",
      "error": "Expected at least 3 items but found 2"
    }
  ]
}
```

</details>

"Basic" 结构是输出单元的扁平列表。

```json
{
  "valid": false,
  "errors": [
    {
      "keywordLocation": "",
      "instanceLocation": "",
      "error": "A subschema had errors."
    },
    {
      "keywordLocation": "/items/$ref",
      "absoluteKeywordLocation":
        "https://example.com/polygon#/$defs/point",
      "instanceLocation": "/1",
      "error": "A subschema had errors."
    },
    {
      "keywordLocation": "/items/$ref/required",
      "absoluteKeywordLocation":
        "https://example.com/polygon#/$defs/point/required",
      "instanceLocation": "/1",
      "error": "Required property 'y' not found."
    },
    {
      "keywordLocation": "/items/$ref/additionalProperties",
      "absoluteKeywordLocation":
        "https://example.com/polygon#/$defs/point/additionalProperties",
      "instanceLocation": "/1/z",
      "error": "Additional property 'z' found but was invalid."
    },
    {
      "keywordLocation": "/minItems",
      "instanceLocation": "",
      "error": "Expected at least 3 items but found 2"
    }
  ]
}
```

#### 12.4.3. 详细（Detailed）

<details>
  <summary>Expand original text</summary>

The "Detailed" structure is based on the schema and can be more readable for both humans and machines. Having the structure organized this way makes associations between the errors more apparent. For example, the fact that the missing "y" property and the extra "z" property both stem from the same location in the instance is not immediately obvious in the "Basic" structure. In a hierarchy, the correlation is more easily identified.

The following rules govern the construction of the results object:

- All applicator keywords ("*Of", "$ref", "if"/"then"/"else", etc.) require a node.
- Nodes that have no children are removed.
- Nodes that have a single child are replaced by the child.

Branch nodes do not require an error message or an annotation.

```json
{
  "valid": false,
  "keywordLocation": "",
  "instanceLocation": "",
  "errors": [
    {
      "valid": false,
      "keywordLocation": "/items/$ref",
      "absoluteKeywordLocation":
        "https://example.com/polygon#/$defs/point",
      "instanceLocation": "/1",
      "errors": [
        {
          "valid": false,
          "keywordLocation": "/items/$ref/required",
          "absoluteKeywordLocation":
            "https://example.com/polygon#/$defs/point/required",
          "instanceLocation": "/1",
          "error": "Required property 'y' not found."
        },
        {
          "valid": false,
          "keywordLocation": "/items/$ref/additionalProperties",
          "absoluteKeywordLocation":
            "https://example.com/polygon#/$defs/point/additionalProperties",
          "instanceLocation": "/1/z",
          "error": "Additional property 'z' found but was invalid."
        }
      ]
    },
    {
      "valid": false,
      "keywordLocation": "/minItems",
      "instanceLocation": "",
      "error": "Expected at least 3 items but found 2"
    }
  ]
}
```
</details>

"Detailed" 结构基于模式，对人和机器都更具可读性。以这种方式组织结构使得错误之间的关联更加明显。例如，在 "Basic" 结构中，缺少的 "y" 属性和额外的 "z" 属性都来自实例中的相同位置，这一点并不是一目了然。在层次结构中，关联更容易识别。

以下规则决定了结果对象的构建：

- 所有应用器关键字（`*Of`、`$ref`、`if`/`then`/`else` 等）都需要一个节点。
- 没有子节点的节点会被删除。
- 只有一个子节点的节点将被子节点替换。

分支节点不需要错误消息或注解。

```json
{
  "valid": false,
  "keywordLocation": "",
  "instanceLocation": "",
  "errors": [
    {
      "valid": false,
      "keywordLocation": "/items/$ref",
      "absoluteKeywordLocation":
        "https://example.com/polygon#/$defs/point",
      "instanceLocation": "/1",
      "errors": [
        {
          "valid": false,
          "keywordLocation": "/items/$ref/required",
          "absoluteKeywordLocation":
            "https://example.com/polygon#/$defs/point/required",
          "instanceLocation": "/1",
          "error": "Required property 'y' not found."
        },
        {
          "valid": false,
          "keywordLocation": "/items/$ref/additionalProperties",
          "absoluteKeywordLocation":
            "https://example.com/polygon#/$defs/point/additionalProperties",
          "instanceLocation": "/1/z",
          "error": "Additional property 'z' found but was invalid."
        }
      ]
    },
    {
      "valid": false,
      "keywordLocation": "/minItems",
      "instanceLocation": "",
      "error": "Expected at least 3 items but found 2"
    }
  ]
}
```

#### 12.4.4. 详细（Verbose）

<details>
  <summary>Expand original text</summary>

The "Verbose" structure is a fully realized hierarchy that exactly matches that of the schema. This structure has applications in form generation and validation where the error's location is important.

The primary difference between this and the "Detailed" structure is that all results are returned. This includes sub-schema validation results that would otherwise be removed (e.g. annotations for failed validations, successful validations inside a `not` keyword, etc.). Because of this, it is RECOMMENDED that each node also carry a `valid` property to indicate the validation result for that node.

Because this output structure can be quite large, a smaller example is given here for brevity. The URI of the full output structure of the example above is: https://json-schema.org/draft/2020-12/output/verbose-example.

```json
// schema
{
  "$id": "https://example.com/polygon",
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "validProp": true,
  },
  "additionalProperties": false
}

// instance
{
  "validProp": 5,
  "disallowedProp": "value"
}

// result
{
  "valid": false,
  "keywordLocation": "",
  "instanceLocation": "",
  "errors": [
    {
      "valid": true,
      "keywordLocation": "/type",
      "instanceLocation": ""
    },
    {
      "valid": true,
      "keywordLocation": "/properties",
      "instanceLocation": ""
    },
    {
      "valid": false,
      "keywordLocation": "/additionalProperties",
      "instanceLocation": "",
      "errors": [
        {
          "valid": false,
          "keywordLocation": "/additionalProperties",
          "instanceLocation": "/disallowedProp",
          "error": "Additional property 'disallowedProp' found but was invalid."
        }
      ]
    }
  ]
}
```
</details>

"Verbose" 结构是一个完全实现的层次结构，与模式完全匹配。这种结构在表单生成和验证中有应用，其中错误的位置很重要。

它与 "Detailed" 结构的主要区别在于返回所有结果。这包括其它情况下将被删除的子模式验证结果（例如，失败验证的注释，`not` 关键字内的成功验证等）。因此，建议（RECOMMENDED）每个节点还携带一个 `valid` 属性，以指示该节点的验证结果。

因为这个输出结构可能相当大，所以在这里给出一个较小的例子，以简洁为主。上述示例的完整输出结构的URI是：https://json-schema.org/draft/2020-12/output/verbose-example。

```json
// schema
{
  "$id": "https://example.com/polygon",
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "validProp": true,
  },
  "additionalProperties": false
}

// instance
{
  "validProp": 5,
  "disallowedProp": "value"
}

// result
{
  "valid": false,
  "keywordLocation": "",
  "instanceLocation": "",
  "errors": [
    {
      "valid": true,
      "keywordLocation": "/type",
      "instanceLocation": ""
    },
    {
      "valid": true,
      "keywordLocation": "/properties",
      "instanceLocation": ""
    },
    {
      "valid": false,
      "keywordLocation": "/additionalProperties",
      "instanceLocation": "",
      "errors": [
        {
          "valid": false,
          "keywordLocation": "/additionalProperties",
          "instanceLocation": "/disallowedProp",
          "error": "Additional property 'disallowedProp' found but was invalid."
        }
      ]
    }
  ]
}
```

#### 12.4.5. 输出验证模式（Output validation schemas）

<details>
  <summary>Expand original text</summary>

For convenience, JSON Schema has been provided to validate output generated by implementations. Its URI is: https://json-schema.org/draft/2020-12/output/schema.
</details>

为方便起见，提供了 JSON Schema 来验证实现生成的输出。它的 URI 是：https://json-schema.org/draft/2020-12/output/schema。

## 13. 安全性考虑（Security Considerations）

<details>
  <summary>Expand original text</summary>

Both schemas and instances are JSON values. As such, all security considerations defined in RFC 8259 [RFC8259] apply.

Instances and schemas are both frequently written by untrusted third parties, to be deployed on public Internet servers. Validators should take care that the parsing and validating against schemas does not consume excessive system resources. Validators MUST NOT fall into an infinite loop.

A malicious party could cause an implementation to repeatedly collect a copy of a very large value as an annotation. Implementations SHOULD guard against excessive consumption of system resources in such a scenario.

Servers MUST ensure that malicious parties cannot change the functionality of existing schemas by uploading a schema with a pre-existing or very similar "$id".

Individual JSON Schema vocabularies are liable to also have their own security considerations. Consult the respective specifications for more information.

Schema authors should take care with "$comment" contents, as a malicious implementation can display them to end-users in violation of a spec, or fail to strip them if such behavior is expected.

A malicious schema author could place executable code or other dangerous material within a "$comment". Implementations MUST NOT parse or otherwise take action based on "$comment" contents.
</details>

模式和实例都是 JSON 值。因此，RFC 8259 [RFC8259] 中定义的所有安全性考虑都适用。

实例和模式通常由不受信任的第三方编写，部署在公共互联网服务器上。验证器应确保解析和根据模式验证不会消耗过多的系统资源。验证器必须不（MUST NOT）陷入无限循环。

恶意方可能导致实现反复收集一个非常大的值作为注释的副本。实现应该（SHOULD）防范这种情况下过度消耗系统资源。

服务器必须（MUST）确保恶意方无法通过上传具有预先存在或非常相似的 `$id` 的模式来更改现有模式的功能。

各个 JSON Schema 词汇表也可能有自己的安全性考虑。有关更多信息，请参阅各自的规范。

模式作者应注意 `$comment` 的内容，因为恶意实现可能会违反规范向最终用户显示它们，或者在预期此类行为时未能将它们剔除。

恶意模式作者可能在 `$comment` 中放置可执行代码或其它危险材料。实现必须不（MUST NOT）解析或根据 `$comment` 内容采取其它行动。

## 14. IANA Considerations（略，见原文）

## 15. References（略，见原文）
## Appendix（略，见原文）

## 原文：[JSON Schema: A Media Type for Describing JSON Documents](https://json-schema.org/draft/2020-12/json-schema-core.html)

[RFC2119]: https://datatracker.ietf.org/doc/html/rfc2119
[RFC3986]: https://datatracker.ietf.org/doc/html/rfc3986
[RFC6596]: https://datatracker.ietf.org/doc/html/rfc6596
[RFC6839]: https://datatracker.ietf.org/doc/html/rfc6839
[RFC6901]: https://datatracker.ietf.org/doc/html/rfc6901
[RFC8259]: https://datatracker.ietf.org/doc/html/rfc8259
[RFC8949]: https://datatracker.ietf.org/doc/html/rfc8949

[ecma262]: https://www.ecma-international.org/ecma-262/11.0/index.html
[json-schema-validation]: https://datatracker.ietf.org/doc/html/draft-bhutton-json-schema-validation-01
[json-hyper-schema]: https://datatracker.ietf.org/doc/html/draft-handrews-json-schema-hyperschema-02

[W3C.WD-fragid-best-practices-20121025]: https://www.w3.org/TR/2012/WD-fragid-best-practices-20121025
[W3C.REC-ldp-20150226]: https://www.w3.org/TR/2015/REC-ldp-20150226
