# XIR Positioning Note for W3C Communities

## Abstract

XIR (eXtensible Intermediate Representation) is a compact, expression-oriented serialization of XDM designed for semantic, governance, lineage, and interoperability use cases. It provides an alternative representation for structured information that preserves the capabilities of the XML ecosystem while reducing syntactic overhead and aligning more closely with abstract syntax trees, semantic expressions, and derivation models.

XIR is not intended to replace XML, RDF, SKOS, SHACL, PROV-O, or related standards. Any information represented in XIR can, in principle, also be represented using existing standards and formats. Instead, XIR provides a compact representation layer that can host these standards while simplifying transformation, analysis, lineage capture, validation and AI-assisted processing.

---

## Problem Statement

Organizations repeatedly describe the same business intent using multiple technical representations:

- XML schemas
- RDF vocabularies
- SKOS taxonomies
- SHACL constraints
- Cypher queries
- SQL statements
- APIs
- Documentation
- Lineage models
- Governance artifacts

Each solves a local problem, but collectively they introduce semantic fragmentation.

The challenge is not a lack of standards.

The challenge is the repeated re-expression of the same meaning in different technical forms.

---

## What XIR Is

XIR should be understood as:

> A compact serialization of XDM optimized for expressing structure, derivation, validation and semantic intent.

XIR is not a replacement for XML.

Everything expressible in XIR can be represented in XML.

The motivation is to provide a representation that exhibits:

- higher semantic density
- lower syntactic noise
- direct mapping to ASTs
- straightforward schema derivation
- efficient processing by humans and AI systems

XML:

```xml
<book id="b1">
    <title>XML</title>
</book>
```

XIR:

```lisp
(book
  { id "b1" }
  (title "XML"))
```

The underlying structure remains the same. The representation becomes closer to the tree itself.

---

## XDM as Canonical Structural Model

The architectural foundation of XIR is XDM.

```text
Sources
   ↓
 XDM
   ↓
 XIR
```

XIR should therefore be viewed as a serialization of XDM rather than a competing data model.

### Reference Architecture

```text
XML
   ↓
SAX Events
   ↓
Internal Model
   ↓
Transforms

XIR
   ↓
SAX Events
   ↓
Internal Model
   ↓
Transforms
```

The current implementation processes both XML and XIR through equivalent SAX/JAXP pipelines.

---

## Canonical XIR Serialization

### Node Types

```lisp
(. ...)
```
Document node.

```lisp
(book ...)
```
Element node.

```lisp
"text"
```
Text node.

```lisp
(! "comment")
```
Comment node.

```lisp
(?target { key value })
```
Processing instruction.

### Structured Types: Maps and Sequences

XIR represents XDM maps and sequences using delimiter syntax.

Maps (`{}`):

```lisp
{
  name "John"
  age 42
  active true
}
```

Sequences/Arrays (`[]`):

```lisp
[
  "red"
  "green"
  "blue"
]
```

Nested structures:

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

Arrays of maps:

```lisp
[
  { id 1 name "John" }
  { id 2 name "Jane" }
]
```

These structures represent XDM maps and sequences directly without requiring special node heads.

**Canonical Forms:**

- `{}` → XDM map
- `[]` → XDM sequence

**Rationale:**

- removes need for XDM namespace
- reduces syntactic overhead
- improves readability
- aligns with JSON and AST-oriented representations
- preserves structural fidelity

---

## XIR as Representation, Not Evaluation

XIR is a representation format for structure.

XIR does not define:

- variable bindings
- execution contexts
- query semantics
- transformation semantics
- rule evaluation semantics

These concerns belong to processing environments consuming XIR.

### Element Nodes versus Maps

Element nodes (XML/XDM):

```lisp
(customer
  { id "cust-1" }
  (name "John")
)
```

Map (value):

```lisp
{
  id "cust-1"
  name "John"
}
```

Applications MUST preserve this distinction.

### Variable and Symbol Representation

Variable names and symbols use ordinary values:

```lisp
customer
ns:customer
rdf:subject
skos:concept
```

No XIR-specific variable namespace is required.

Bindings, scopes, and symbol tables belong to hosted languages, not XIR itself.

This preserves separation:

```text
XIR = representation
Hosted language = semantics
```

and prevents XIR from becoming an execution language.

---

## Grammar-Driven Interoperability

A distinguishing characteristic of XIR is grammar-driven onboarding.

```text
ANTLR Grammar
        ↓
     Schema
        ↓
      XDM
        ↓
      XIR
```

Programming languages, DSLs, XML vocabularies, RDF vocabularies and governance models can all enter the same representation ecosystem through grammar- or schema-driven projections.

---

## Relationship to Existing Standards

### XML

XML provides document structure.

XIR provides a more compact representation of equivalent structure while remaining compatible with XML processing pipelines.

### RDF

RDF provides graph representation.

RDF/XML and other RDF serializations can be represented within XIR without redefining RDF semantics.

### SKOS

SKOS concept schemes, labels, hierarchies and mappings can be represented within XIR while preserving SKOS semantics.

### SHACL

SHACL constraints can be represented as semantic assertions and validation artifacts within XIR.

### PROV-O

PROV-O focuses on provenance.

XIR complements provenance by also representing derivation expressions, transformations and validation artifacts.

### OpenLineage

OpenLineage focuses on operational lineage events.

XIR can host these events while connecting them to broader semantic and governance models.

---

## Implementation Characteristics

Current implementation goals include:

- XML ↔ XIR lossless round-tripping
- deterministic serialization
- namespace preservation
- SAX/JAXP compatibility
- streaming processing
- Gradle integration through .sexpr support

Example:

```text
input.xml
   → xml-to-xir
   → document.sexpr
   → xir-to-xml
   → output.xml
```

Success is defined by canonical equivalence after round-trip conversion.

---

## Vision

XIR does not replace existing standards.

Instead, it provides a common representation format in which:

- XML vocabularies
- RDF models
- SKOS taxonomies
- SHACL constraints
- provenance models
- lineage artifacts
- programming language ASTs
- domain-specific languages

can coexist while remaining accessible to:

- transformation engines
- governance platforms
- lineage systems
- documentation systems
- AI systems

The long-term objective is semantic interoperability through a compact, XDM-based representation layer rather than semantic replacement.
