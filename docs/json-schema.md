---
sidebar_position: 2
---

# JSON Schema 中文文档

本文翻译自：[JSON Schema: A Media Type for Describing JSON Documents](https://json-schema.org/draft/2020-12/json-schema-core.html).

## 摘要（Abstract）

<details>
  <summary>Expand original text</summary>

JSON Schema defines the media type "application/schema+json", a JSON-based format for describing the structure of JSON data.
JSON Schema asserts what a JSON document must look like, ways to extract information from it, and how to interact with it. The "application/schema-instance+json" media type provides additional feature-rich integration with "application/schema+json" beyond what can be offered for "application/json" documents.
</details>

JSON Schema 定义了媒体类型 `application/schema+json`，这是一种基于 JSON 的格式，用于描述 JSON 数据的结构。
JSON Schema 定义了 JSON 文档的内容结构，从中提取信息的方法以及如何与其交互。`application/schema-instance+json` 媒体类型为 `application/schema+json` 提供了额外的功能丰富的集成，超出了 `application/json` 文档所能提供的内容。

## 1. 引言（Introduction）

<details>
  <summary>Expand original text</summary>

JSON Schema is a JSON media type for defining the structure of JSON data. JSON Schema is intended to define validation, documentation, hyperlink navigation, and interaction control of JSON data.

This specification defines JSON Schema core terminology and mechanisms, including pointing to another JSON Schema by reference, dereferencing a JSON Schema reference, specifying the dialect being used, specifying a dialect's vocabulary requirements, and defining the expected output.

Other specifications define the vocabularies that perform assertions about validation, linking, annotation, navigation, and interaction.
</details>

JSON Schema 是一种用于定义 JSON 数据结构的 JSON 媒体类型。JSON Schema 旨在定义 JSON 数据的验证、文档、超链接导航和交互控制。

本规范定义了 JSON Schema 核心术语和机制，包括通过引用指向另一个 JSON Schema、取消引用 JSON Schema 引用、指定正在使用的方言（dialect）、指定方言的词汇表（vocabulary）要求以及定义预期输出。

其他规范定义了用于执行关于验证（validation）、链接（linking）、注释（annotation）、导航（navigation）和交互（interaction）的断言的词汇表。

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

本文档提议一种新的媒体类型 `application/schema+json`，用于标识描述 JSON 数据的 JSON 模式。它还提议了另一种可选的媒体类型 `application/schema-instance+json`，以提供其他集成功能。JSON 模式本身是 JSON 文档。此类及相关规范定义了允许作者以多种方式描述 JSON 数据的关键字。

JSON Schema 使用关键字对 JSON 实例进行约束或用附加信息对这些实例进行注释。其他关键字用于对更复杂的 JSON 数据结构应用断言和注释，或基于某种条件。

为了便于重用，关键字可以组织成词汇表。一个词汇表由一组关键字及其语法和语义组成。一种方言被定义为一组词汇表及其在元模式中确定的所需支持。

JSON Schema 可以通过定义额外的词汇表或者在词汇表之外非正式地定义额外的关键字来扩展。无法识别的单个关键字会将其值作为注释收集，而在声明正在使用的词汇表时，可以控制对无法识别的词汇表的行为。

本文档定义了一个核心词汇表，必须由任何实现支持，并且不能被禁用。其关键字都以 "$" 字符为前缀，以强调其必需性。这个词汇表对于 "application/schema+json" 媒体类型的功能至关重要，并用于引导其他词汇表的加载。

此外，本文档定义了一个建议的关键字词汇表，用于有条件地应用子模式，以及将子模式应用于对象和数组的内容。为了编写用于断言验证、注释或两者兼而有之的非平凡 JSON 实例的模式，需要使用这个词汇表或类似的词汇表。虽然不是必需的核心词汇表的一部分，但为了最大的互操作性，本文档包含了这个附加词汇表，并强烈推荐使用。

其他文档中定义了用于结构验证或超媒体注释等目的的更多词汇表。这些其他文档中的每一个都定义了一个方言，收集编写用于该文档目的的模式所需的标准词汇表集合。

## 4. 定义（Definitions）


### 4.1 JSON 文档

<details>
  <summary>Expand original text</summary>

A JSON document is an information resource (series of octets) described by the application/json media type.

In JSON Schema, the terms "JSON document", "JSON text", and "JSON value" are interchangeable because of the data model it defines.

JSON Schema is only defined over JSON documents. However, any document or memory structure that can be parsed into or processed according to the JSON Schema data model can be interpreted against a JSON Schema, including media types like [CBOR][RFC8949].
</details>

JSON 文档是一个由 application/json 媒体类型描述的信息资源（一系列八位字节组）。
在 JSON Schema 中，术语 "JSON 文档"、"JSON 文本" 和 "JSON 值" 是可以互换的，因为它定义了数据模型。

JSON Schema 只针对 JSON 文档进行定义。然而，任何可以解析为 JSON Schema 数据模型的文档或内存结构都可以根据 JSON Schema 进行解释，包括像 [CBOR][RFC8949] 这样的媒体类型。

### 4.2 实例（Instance）

<details>
  <summary>Expand original text</summary>

A JSON document to which a schema is applied is known as an "instance".

JSON Schema is defined over "application/json" or compatible documents, including media types with the "+json" structured syntax suffix.

Among these, this specification defines the "application/schema-instance+json" media type which defines handling for fragments in the URI.
</details>

应用了一个模式的 JSON 文档被称为 "实例"。

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

实例有六种基本类型，取决于类型，可以有一系列可能的值：
- `null`：JSON "null" 值
- `boolean`：JSON "true" 或 "false" 值的 "true" 或 "false" 值
- `object`：一个将字符串映射到实例的无序属性集，来自 JSON "object" 值
- `array`：实例的有序列表，来自 JSON "array" 值
- `number`：任意精度、基数为10的十进制数字值，来自 JSON "number" 值
- `string`：Unicode 代码点的字符串，来自 JSON "string" 值

因此，空白和格式问题（例如，数据模型中相等的数字的不同词法表示）不在 JSON Schema 的范围内。希望处理词法表示差异的 JSON Schema 词汇表（第 8.1 节）应该定义关键字，以便在数据模型中精确解释格式化字符串，而不是依赖于原始 JSON 表示的 Unicode 字符。

由于对象不能有两个具有相同键的属性，所以在单个对象中尝试定义两个具有相同键的属性的 JSON 文档的行为是未定义的。

请注意，JSON Schema 词汇表可以自由地定义自己的扩展类型系统。这不应与此处定义的核心数据模型类型混淆。例如，"integer" 是一个合理的类型，词汇表可以将其定义为关键字的值，但数据模型不区分整数和其他数字。

#### 4.2.2 实例相等（Instance Equality）

<details>
  <summary>Expand original text</summary>

Two JSON instances are said to be equal if and only if they are of the same type and have the same value according to the data model. Specifically, this means:

```text
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

```text
两者都是 null；或
两者都是 true；或
两者都是 false；或
两者都是字符串，并且在每个代码点上相同；或
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

可以在 JSON Schema 数据模型的超集中使用 JSON Schema，其中实例可能超出 JSON 的六种数据类型之外。

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

```text
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

应用于实例的对象属性称为关键字或模式关键字。大致上，关键字可分为五类：

```text
标识符（identifiers）：通过为模式设置 URI 和/或更改基本 URI 的确定方式来控制模式识别
断言（assertions）：在应用于实例时产生布尔结果
注解（annotations）：为应用程序使用附加信息到实例
修改器（applicators）：将一个或多个子模式应用于实例中的特定位置，并组合或修改它们的结果
保留位置（reserved locations）：不直接影响结果，但为确保互操作性保留一个特定目的的位置
```

关键字可能属于多个类别，尽管应用程序应仅根据其子模式的结果产生断言结果。它们不应定义与其子模式无关的其他约束。

在同一模式对象中的属性的关键字被称为相邻关键字。

扩展关键字，即在本文档及其附件之外定义的关键字，也可以自由定义其他行为。

JSON Schema 可能包含非模式关键字的属性。未知关键字应被视为注解，其中关键字的值是注解的值。

空模式是一个没有属性或只有未知属性的 JSON Schema。

#### 4.3.2. 布尔 JSON Schemas

<details>
  <summary>Expand original text</summary>

The boolean schema values "true" and "false" are trivial schemas that always produce themselves as assertion results, regardless of the instance value. They never produce annotation results.

These boolean schemas exist to clarify schema author intent and facilitate schema processing optimizations. They behave identically to the following schema objects (where "not" is part of the subschema application vocabulary defined in this document).

```text
true: Always passes validation, as if the empty schema {}
false: Always fails validation, as if the schema { "not": {} }
```

While the empty schema object is unambiguous, there are many possible equivalents to the "false" schema. Using the boolean values ensures that the intent is clear to both human readers and implementations.
</details>

布尔模式值 "true" 和 "false" 是始终产生它们自身作为断言结果的简单模式，无论实例值如何。它们从不产生注解结果。

这些布尔模式存在是为了澄清模式作者的意图并促进模式处理优化。它们的行为与以下模式对象完全相同（其中 "not" 是本文档中定义的子模式应用词汇的一部分）。

```text
`true`：始终通过验证，就像空模式 `{}` 一样
`false`：始终无法通过验证，就像模式 `{ "not": {} }` 一样
```

虽然空模式对象是明确的，但与 "false" 模式等价的可能有很多。使用布尔值确保意图对人类阅读者和实现都是清晰的。

#### 4.3.3. 模式词汇表（Schema Vocabularies）

<details>
  <summary>Expand original text</summary>
A schema vocabulary, or simply a vocabulary, is a set of keywords, their syntax, and their semantics. A vocabulary is generally organized around a particular purpose. Different uses of JSON Schema, such as validation, hypermedia, or user interface generation, will involve different sets of vocabularies.

Vocabularies are the primary unit of re-use in JSON Schema, as schema authors can indicate what vocabularies are required or optional in order to process the schema. Since vocabularies are identified by URIs in the meta-schema, generic implementations can load extensions to support previously unknown vocabularies. While keywords can be supported outside of any vocabulary, there is no analogous mechanism to indicate individual keyword usage.

A schema vocabulary can be defined by anything from an informal description to a standards proposal, depending on the audience and interoperability expectations. In particular, in order to facilitate vocabulary use within non-public organizations, a vocabulary specification need not be published outside of its scope of use.
</details>

模式词汇表，或简称为词汇表，是一组关键字、它们的语法和语义。词汇表通常围绕特定目的组织。JSON Schema 的不同用途，如验证、超媒体或用户界面生成，将涉及不同的词汇表集合。

词汇表是 JSON Schema 中主要的可重用单元，因为模式作者可以指示处理模式所需或可选的词汇表。由于元模式中的词汇表由 URI 标识，通用实现可以加载扩展以支持以前未知的词汇表。虽然关键字可以在任何词汇表之外得到支持，但没有类似的机制来指示单个关键字的使用。

模式词汇表可以由从非正式描述到标准提案的任何内容定义，具体取决于受众和互操作性期望。特别是为了促进非公共组织内的词汇表使用，词汇表规范不需要在其使用范围之外发布。

#### 4.3.4. 元模式（Meta-Schemas）

<details>
  <summary>Expand original text</summary>

4.3.4. Meta-Schemas
A schema that itself describes a schema is called a meta-schema. Meta-schemas are used to validate JSON Schemas and specify which vocabularies they are using.

Typically, a meta-schema will specify a set of vocabularies, and validate schemas that conform to the syntax of those vocabularies. However, meta-schemas and vocabularies are separate in order to allow meta-schemas to validate schema conformance more strictly or more loosely than the vocabularies' specifications call for. Meta-schemas may also describe and validate additional keywords that are not part of a formal vocabulary.
</details>

用来描述模式的模式被称为元模式。元模式用于验证 JSON 模式并指定它们正在使用的词汇表。

通常，元模式会指定一组词汇表，并验证符合这些词汇表语法的模式。然而，元模式和词汇表是分开的，以便允许元模式比词汇表的规范要求更严格或更宽松地验证模式一致性。元模式还可以描述和验证不属于正式词汇表的额外关键字。

#### 4.3.5. 根模式、子模式和资源（Root Schema and Subschemas and Resources）

<details>
  <summary>Expand original text</summary>
A JSON Schema resource is a schema which is canonically [RFC6596] identified by an absolute URI [RFC3986]. Schema resources MAY also be identified by URIs, including URIs with fragments, if the resulting secondary resource (as defined by section 3.5 of RFC 3986 [RFC3986]) is identical to the primary resource. This can occur with the empty fragment, or when one schema resource is embedded in another. Any such URIs with fragments are considered to be non-canonical.

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

根模式是组成整个 JSON 文档的模式。根模式始终是一个模式资源，URI 是按照第 9.1.1 节的描述确定的。请注意，将模式嵌入到其他格式的文档在这个意义上不会有一个根模式资源。这种用法与 JSON Schema 文档和资源概念如何适应将在未来的草案中得到澄清。

某些关键字本身采用模式，允许 JSON 模式嵌套：

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

如第 8.2.1 节所讨论的，一个 JSON Schema 文档可以包含多个 JSON Schema 资源。在未经限定的情况下使用术语 "根模式" 是指文档的根模式。在某些情况下，讨论了资源的根模式。资源的根模式是其顶级模式对象，如果资源被提取到一个独立的 JSON Schema 文档中，它也将成为一个文档根模式。

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

根据 [RFC 6839][RFC6839] 第 3.1 节的规定，任何 `+json` 媒体类型的片段标识符的语法和语义应与 `application/json` 的规定相同。（在本文档发布时，尚未为 "application/json" 定义片段标识符语法。）
此外，`application/schema+json` 媒体类型支持两种片段标识符结构：纯名称和 JSON 指针。`application/schema-instance+json` 媒体类型支持一种片段标识符结构：JSON 指针。

在 [RFC 6901][RFC6901] 中描述了将 JSON 指针用作 URI 片段标识符。对于 `application/schema+json`，它支持两种片段标识符语法，匹配 JSON 指针语法的片段标识符（包括空字符串）必须被解释为 JSON 指针片段标识符。

根据 W3C 关于片段标识符的最佳实践 [W3C.WD-fragid-best-practices-20121025][W3C.WD-fragid-best-practices-20121025]，`application/schema+json` 中的纯名称片段标识符保留用于引用本地命名的模式。所有不符合 JSON 指针语法的片段标识符必须被解释为纯名称片段标识符。

在 `application/schema+json` 文档中定义和引用纯名称片段标识符的规定在 `$anchor` 关键字（第 8.2.2 节）部分。




[RFC2119]: https://www.rfc-editor.org/info/rfc2119
[RFC3986]: https://www.rfc-editor.org/info/rfc3986
[RFC6596]: https://www.rfc-editor.org/info/rfc6596
[RFC6839]: https://www.rfc-editor.org/info/rfc6839
[RFC6901]: https://www.rfc-editor.org/info/rfc6901
[RFC8259]: https://www.rfc-editor.org/info/rfc8259
[RFC8949]: https://www.rfc-editor.org/info/rfc8949

[W3C.WD-fragid-best-practices-20121025]: https://www.w3.org/TR/2012/WD-fragid-best-practices-20121025