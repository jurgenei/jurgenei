# S-XDM Comprehensive Implementation Specification

## For Developers and Architects

Version: 1.0 (merged MVP + reference spec)

## 1. Purpose

Define one implementation spec for S-XDM in `gradle-xml-plugin` ecosystem.

Goal:

- prove S-XDM is complete, lossless structural serialization route for XDM-compatible content
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

- XML <-> S-XDM roundtrip through SAX/JAXP pipeline
- `.sexpr` input and output support in Gradle tasks
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
Read path (.xml/.sexpr)
  input file
    -> source adapter (XMLReader or SExpressionXmlReader)
    -> SAX events
    -> internal model/events
    -> Saxon/JAXP transform pipeline

Write path (.xml/.sexpr)
  transform result (events/model)
    -> serializer adapter (XML serializer or SExpressionSerializer)
    -> output file
```

### 3.2 Layer responsibilities

- XML Import Layer: build events/model from standard XML reader
- S-XDM Reader: parse `.sexpr` into SAX-compatible event stream
- Internal Model Layer: preserve ordering, namespaces, node kinds, lexical values
- S-XDM Writer: serialize events/model to canonical S-expression syntax
- XML Export Layer: emit XML through JAXP/SAX serializer
- Gradle Integration Layer: choose adapters by extension and task config

## 4. Canonical S-XDM Syntax

### 4.1 Node heads

- `(. ...)` document node
- `(qname ...)` element node
- `"text"` text node
- `(! "comment")` comment node
- `(?target { key value ... })` processing instruction

### 4.2 Containers

- `{ ... }` associative payload container (attributes, PI pseudo-attributes, map entries)
- `[ ... ]` sequence payload container (arrays)

### 4.3 Reserved heads for non-element XDM structures

- `(xdm:map { key value ... })`
- `(xdm:array [ item ... ])`

Disambiguation rule:

- `(map ...)` and `(array ...)` remain normal XML elements
- only `xdm:map` and `xdm:array` represent XDM map/array

### 4.4 Attribute representation

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
public final class SExpressionParser {
    public void parse(Reader reader, ContentHandler handler) throws IOException, SAXException;
}
```

Contract:

- consume UTF-8 text stream
- preserve node order exactly
- report line/column on syntax errors

### 6.2 SAX adapter for input

```java
public final class SExpressionXmlReader implements XMLReader {
    // bridges SExpressionParser to SAXSource
}
```

Usage:

```java
Source source = new SAXSource(new SExpressionXmlReader(), new InputSource(reader));
```

### 6.3 Serializer for output

```java
public final class SExpressionSerializer implements ContentHandler {
    // consumes SAX events and writes canonical S-XDM
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
    SEXPR
}
```

Detection rules:

- `.sexpr` -> `SEXPR`
- other configured XML extensions -> `XML`

### 7.2 Input adapter selection

If input extension is `.sexpr`, task must build source using `SAXSource(SExpressionXmlReader)`.

Otherwise use existing XML parser source path.

### 7.3 Output adapter selection

If output extension is `.sexpr`, task must attach `SExpressionSerializer`.

Otherwise use existing XML serializer path.

### 7.4 Gradle DSL examples

```kotlin
xslt {
    input.set(file("input.sexpr"))
    output.set(file("output.xml"))
}
```

```kotlin
xslt {
    input.set(file("input.xml"))
    output.set(file("output.sexpr"))
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
  -> xmlToSexpr
  -> a.sexpr
  -> sexprToXml
  -> b.xml

canonicalize(input.xml) == canonicalize(b.xml)
```

For `.sexpr` origin:

```text
input.sexpr
  -> sexprToXml
  -> x.xml
  -> xmlToSexpr
  -> y.sexpr

normalize(input.sexpr) == normalize(y.sexpr)
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
- avoid building full DOM for XML <-> S-XDM conversion path
- benchmark with small, medium, large fixture sets

## 13. Test Strategy

### 13.1 Unit tests

- parser tokenization and AST forms
- serializer node emission and formatting
- namespace scope edge cases
- comment/PI handling

### 13.2 Integration tests

- XML -> S-XDM -> XML roundtrip
- S-XDM -> XML -> S-XDM roundtrip
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

1. `SExpressionParser`
2. `SExpressionXmlReader`
3. `SExpressionSerializer`
4. extension-based source selection in Gradle tasks
5. extension-based result selection in Gradle tasks
6. roundtrip integration tests with canonical equality assertions

Reference profile deliverables:

1. `xdm:map` and `xdm:array` full support
2. typed atomic policy hooks
3. optional Saxon profile bridge

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

- S-XSD
- S-XSLT
- XPath expression syntax representation
- function items
- schema-aware processing
- AI evaluation benchmark

## 16. Success Criteria

Release is successful when:

- XML -> S-XDM works on fixture corpus
- S-XDM -> XML works on fixture corpus
- lossless roundtrip proven by automated canonical comparisons
- Gradle tasks accept `.sexpr` input and output without custom user plumbing
- deterministic output proven across repeated runs
- test suite passes in CI

## 17. Source Basis (merged)

This specification merges and supersedes:

- `xml-sax-sexpr/gradle-xml-plugin-sexpr-mvp.md`
- `xml-sax-sexpr/S-XDM-Reference-Implementation-Spec.md`

