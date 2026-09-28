# XIR Implementation Specification

## For Developers and Architects

**Canonical XIR serialization via XDM structural model and S-expression syntax**

Version: 1.0 (merged MVP + reference spec)

> **See also:** [XIR Positioning Note for W3C Communities](XIR_W3C_Positioning_Note.md) for broader context on XIR's role in the standards ecosystem.

## 1. Purpose

Define one implementation spec for XIR (eXtensible Intermediate Representation) in `gradle-xml-plugin` ecosystem.

Goal:

- prove XIR serialization is complete, lossless structural representation for XDM-compatible content
- deliver minimal, shippable Gradle integration first
- preserve expansion path to richer XDM constructs without rewrite

Non-goal:

- replace XML stack
- add semantic domain rewriting (MathML/GraphML semantics, Neo4j export)
- add custom Saxon tree model
- add iXML or URI scheme extensions in first release

## 2. Scope and Profiles

### 2.1 MVP Profile (required first delivery)

Must implement:

- XML <-> XIR roundtrip through SAX/JAXP pipeline
- `.xir` ()  input and output support in Gradle tasks (XIR canonical serialization format)
- lossless support for XML document, elements, attributes, namespaces, text, comments, processing instructions
- deterministic serializer output

Out of scope in MVP:

- XDM maps and arrays in Gradle task pipeline unless explicitly enabled
- schema-aware typing beyond core atomic primitives
- vendor-specific tree model dependencies

### 2.2 Reference Profile (target after MVP)

Adds:

- typed atomic value serialization policy
- XDM maps and arrays via reserved heads
- optional Saxon-native bridges for tighter XDM alignment

## 3. Architecture

### 3.1 Logical flow

```text
Read path (.xml/.xir)
  input file
    -> source adapter (XMLReader or XIR expression reader)
    -> SAX events
    -> internal model/events
    -> Saxon/JAXP transform pipeline

Write path (.xml/.xir)
  transform result (events/model)
    -> serializer adapter (XML serializer or XIR expression serializer)
    -> output file
```

### 3.2 Layer responsibilities

- XML Import Layer: build events/model from standard XML reader
- XIR Reader: parse `.xir` (XIR canonical form) into SAX-compatible event stream
- Internal Model Layer: preserve ordering, namespaces, node kinds, lexical values
- XIR Writer: serialize events/model to canonical XIR expression syntax
- XML Export Layer: emit XML through JAXP/SAX serializer
- Gradle Integration Layer: choose adapters by extension and task config

## 4. XIR Canonical Syntax (S-Expression Serialization)

### 4.1 Node heads

- `(. ...)` document node
- `(qname ...)` element node
- `"text"` text node
- `(! "comment")` comment node
- `(?target { key value ... })` processing instruction

### 4.2 Containers

- `{ ... }` associative payload container (attributes, PI pseudo-attributes, map entries)
- `[ ... ]` sequence payload container (arrays)

### 4.3 Reserved heads for XDM structures beyond XML

Use delimiter-based types defined in section 4.4 (maps with `{}`, sequences with `[]`).

### 4.4 Structured Values: Maps and Sequences

XIR directly represents XDM structured values using native delimiters.

### 4.4.1 Maps

Maps are represented using `{}`.

Example:

```lisp
{
  name "John"
  age 42
  active true
}
```

Equivalent conceptual form:

```json
{
  "name": "John",
  "age": 42,
  "active": true
}
```

### 4.4.2 Arrays and Sequences

Arrays and sequences are represented using `[]`.

Example:

```lisp
[
  "red"
  "green"
  "blue"
]
```

Equivalent conceptual form:

```json
[
  "red",
  "green",
  "blue"
]
```

### 4.4.3 Nested Structures

Maps and arrays may be nested arbitrarily.

Example:

```lisp
{
  id "cust-1"

  emails [
    "john@example.com"
    "john.smith@example.com"
  ]

  tags [
    "preferred"
    "active"
  ]
}
```

### 4.4.4 Arrays of Maps

```lisp
[
  {
    id 1
    name "John"
  }

  {
    id 2
    name "Jane"
  }
]
```

These structures represent XDM maps and sequences directly and do not require special node heads.

### 4.4.5 Delimiter-Defined Types

Map and sequence identity are defined by delimiters, not reserved qualified names.

Canonical forms:

- `{ ... }` → XDM map
- `[ ... ]` → XDM sequence

Rationale:

- removes the need for an XDM namespace
- reduces syntactic overhead
- improves readability
- aligns with JSON and AST-oriented representations
- preserves structural fidelity

### 4.4.6 Element Nodes versus Maps

Element nodes remain distinct from maps.

Element node:

```lisp
(customer
  {
    id "cust-1"
  }
  (name "John")
)
```

Map:

```lisp
{
  id "cust-1"
  name "John"
}
```

The first form is an XML/XDM element node with attribute map and child element.

The second form is a map value.

Applications MUST preserve this distinction.

### 4.5 Representation versus Evaluation

XIR is a representation format.

XIR does not define:

- variable bindings
- execution contexts
- query semantics
- transformation semantics
- rule evaluation semantics

These concerns belong to processing environments that consume XIR.

Example:

```lisp
{
  customer {
    id "cust-1"
    name "John"
  }
}
```

XIR defines only structure.

Interpretation is external.

### 4.5.1 Evaluation Context Example

A processor MAY interpret the structure:

```lisp
{
  customer {
    id "cust-1"
    name "John"
  }
}
```

as an environment:

```text
customer ↦ {
  id "cust-1"
  name "John"
}
```

However, the binding relationship is external to the XIR representation.

XIR intentionally avoids introducing reserved forms such as:

- `(bind customer ...)`
- `(let customer ...)`
- `(var customer ...)`

because they represent evaluation semantics rather than structure.

### 4.5.2 Variable and Symbol Representation

Variable names, identifiers, and symbols are represented as **text values within XDM structures**.

They must appear in XDM-valid contexts:

**In maps (as keys or values):**

```lisp
{
  type "BinaryExpression"
  operator "+"
}
```

**In sequences:**

```lisp
[
  "customer"
  "ns:customer"
  "rdf:subject"
]
```

**As text node children of elements:**

```lisp
(predicate "rdf:subject")
```

**NOT valid as bare element children:**

```lisp
;; INVALID - bare symbols as children
(isa BinaryExpression Expression)

;; VALID - symbols as text content
(isa "BinaryExpression" "Expression")

;; VALID - symbols in map
{
  isa "BinaryExpression"
  extends "Expression"
}
```

**Rationale:**

XDM allows only these node types and values:
- Element nodes
- Attribute nodes (in maps)
- Text nodes (quoted strings)
- Comment nodes
- Processing instruction nodes
- Maps (XDM sequences of key-value pairs)
- Sequences (XDM arrays)
- Atomic values (strings, numbers, booleans, etc.)

Bare symbols are not XDM node types. They must be text-wrapped to conform to XDM.

No XIR-specific variable namespace is required.

Bindings, scopes, and symbol tables belong to higher-level languages that may be serialized in XIR but are not part of XIR itself.

This principle preserves the separation:

```text
XIR             = XDM representation

Hosted language = semantics
```

and ensures XIR conformance to XDM while remaining representation-focused, not execution-focused.

### 4.5.3 XDM Conformance Rules

**XIR MUST map to valid XDM. All XIR constructs MUST be expressible as XDM.**

**Valid XIR element children:**

- Text nodes: `"string"`
- Element nodes: `(qname ...)`
- Maps: `{ key value ... }`
- Sequences: `[ item ... ]`

**Valid XIR map keys and values:**

- Strings: `"value"`
- Numbers: `42`, `3.14`
- Booleans: `true`, `false`
- Element nodes: `(qname ...)`
- Maps: `{ ... }`
- Sequences: `[ ... ]`

**Invalid XIR constructs (non-XDM):**

```lisp
;; INVALID: bare symbol as element child
(isa BinaryExpression Expression)

;; INVALID: bare symbol in sequence
[ customer ns:customer rdf:subject ]

;; INVALID: unquoted identifiers
(name John age 42)
```

**Valid alternatives:**

```lisp
;; VALID: text children
(isa "BinaryExpression" "Expression")

;; VALID: text in sequence
[ "customer" "ns:customer" "rdf:subject" ]

;; VALID: map with text values
(person
  { name "John" age "42" })
```

This constraint ensures XIR is a transparent representation layer for XDM, not a separate language.

### 4.6 Attribute representation

Canonical form uses associative payload in element body:

```lisp
(book
  { id "b1" }
  (title "XML"))
```

Compatibility parse mode may accept legacy MVP attribute block syntax:

```lisp
(book
  [id "b1"]
  (title "XML"))
```

Serializer must emit canonical `{ ... }` form.

## 5. Supported Constructs Matrix

| Construct | MVP | Reference |
| --- | --- | --- |
| Document node | Required | Required |
| Element node | Required | Required |
| Text node | Required | Required |
| Attributes | Required | Required |
| Namespaces | Required | Required |
| Comments | Required | Required |
| Processing instructions | Required | Required |
| Typed atomic values | Basic lexical preservation | Typed policy |
| XDM map | Optional/off by default | Required |
| XDM array | Optional/off by default | Required |

## 6. Java Component Contracts

### 6.1 Parser core

```java
public final class XirParser {
    public void parse(Reader reader, ContentHandler handler) throws IOException, SAXException;
}
```

Contract:

- consume UTF-8 text stream
- preserve node order exactly
- report line/column on syntax errors

### 6.2 SAX adapter for input

```java
public final class XirReader implements XMLReader {
    // bridges XirParser to SAXSource
}
```

Usage:

```java
Source source = new SAXSource(new XirReader(), new InputSource(reader));
```

### 6.3 Serializer for output

```java
public final class XirSerializer implements ContentHandler {
    // consumes SAX events and writes canonical XIR expression
}
```

Contract:

- deterministic indentation/newline policy
- stable namespace declaration emission
- canonical attribute container emission (`{}`)

## 7. Gradle Integration Specification

### 7.1 Document type detection

```kotlin
enum class XmlDocumentType {
    XML,
    XIR
}
```

Detection rules:

- `.xir` -> `XIR` (XIR canonical serialization format)
- other configured XML extensions -> `XML`

### 7.2 Input adapter selection

If input extension is `.xir`, task must build source using `SAXSource(XirReader())` (XIR reader).

Otherwise use existing XML parser source path.

### 7.3 Output adapter selection

If output extension is `.xir`, task must attach `XirSerializer()` (XIR writer).

Otherwise use existing XML serializer path.

### 7.4 Gradle DSL examples

```kotlin
xslt {
    input.set(file("input.xir"))
    output.set(file("output.xml"))
}
```

```kotlin
xslt {
    input.set(file("input.xml"))
    output.set(file("output.xir"))
}
```

## 8. Fidelity and Losslessness Rules

Implementation must preserve:

- element/attribute names and namespace URIs
- namespace scoping behavior
- text node order and boundaries
- comments and processing instructions
- document child order

Roundtrip validation baseline:

```text
input.xml
  -> xml-to-xir
  -> a.xir (XIR canonical form)
  -> xir-to-xml
  -> b.xml

canonicalize(input.xml) == canonicalize(b.xml)
```

For `.xir` (XIR) origin:

```text
input.xir
  -> xir-to-xml
  -> x.xml
  -> xml-to-xir
  -> y.xir

normalize(input.xir) == normalize(y.xir)
```

`normalize` means parser + serializer canonical formatting pass.

## 9. Namespaces and QName Rules

- parser tracks namespace bindings per lexical scope
- serializer emits only required in-scope declarations
- prefix preservation is best effort; URI fidelity is mandatory
- equality checks must compare expanded names (URI + local name), not prefix text

## 10. Typed Atomic Values

MVP behavior:

- preserve lexical text values without coercion

Reference behavior:

- support explicit typed heads where configured:
  - `xs:string`
  - `xs:boolean`
  - `xs:integer`
  - `xs:decimal`
  - `xs:double`
- extension examples:

```lisp
(xs:date "2026-09-06")
(xs:dateTime "2026-09-06T12:00:00")
```

## 11. Error Model

Parser errors must include:

- error class (`LEXICAL`, `SYNTAX`, `NAMESPACE`, `SEMANTIC`)
- message
- line/column
- nearest form excerpt

Gradle task behavior:

- fail fast on parse error
- print compact diagnostic plus source file path
- provide optional verbose trace flag for debugging

## 12. Performance and Memory Constraints

Targets for MVP:

- linear parse/serialize time vs input size
- bounded memory growth for streaming SAX path
- no quadratic string concatenation hotspots

Guidance:

- prefer streaming writer APIs
- avoid building full DOM for XML <-> XIR conversion path
- benchmark with small, medium, large fixture sets

## 13. Test Strategy

### 13.1 Unit tests

- parser tokenization and AST forms
- serializer node emission and formatting
- namespace scope edge cases
- comment/PI handling

### 13.2 Integration tests

- XML -> XIR -> XML roundtrip
- XIR -> XML -> XIR roundtrip
- Gradle task extension-based adapter selection

### 13.3 Regression fixtures

Include fixtures for:

- mixed content
- namespace rebinding
- empty elements
- comments and PIs
- map/array reserved forms (Reference profile)

## 14. Deliverables

MVP deliverables:

1. `XirParser`
2. `XirReader`
3. `XirSerializer`
4. extension-based source selection in Gradle tasks
5. extension-based result selection in Gradle tasks
6. roundtrip integration tests with canonical equality assertions

Reference profile deliverables:

1. typed atomic policy hooks
2. optional Saxon profile bridge

## 15. Implementation Phasing

Phase 1 (MVP):

- core parser/serializer
- Gradle integration
- XML construct fidelity tests

Phase 2 (Reference):

- maps/arrays on by default
- richer typed atomics
- optional Saxon bridge

Phase 3 (Future):

- XIR-XSD (XSD representation for XIR)
- XIR-XSLT (XSLT transformation representation for XIR)
- XPath expression syntax representation in XIR
- function items
- schema-aware processing
- AI evaluation benchmark

## 16. Success Criteria

Release is successful when:

- XML -> XIR works on fixture corpus
- XIR -> XML works on fixture corpus
- lossless roundtrip proven by automated canonical comparisons
- Gradle tasks accept `.xir` input and output without custom user plumbing
- deterministic output proven across repeated runs
- test suite passes in CI

## 17. Source Basis (merged)

This specification merges and supersedes:

- `xml-sax-sexpr/gradle-xml-plugin-sexpr-mvp.md`
- `xml-sax-sexpr/XIR-Reference-Implementation-Spec.md`

