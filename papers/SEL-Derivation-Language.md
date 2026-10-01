# SEL Derivation Language
## Introduction and Normative Specification

> Supersedes: `SEL-Derivation-Language-Short-Paper.md`

## Abstract

SEL (Schematron Expression Language) is derivation language for turning source evidence into structured target artifacts using Schematron-style rule declarations and XPath semantics.  
Unlike generic transformation languages, SEL focuses on semantic derivation: selecting evidence, preserving context, and emitting grouped outputs that feed downstream interpretation and governance pipelines.

This document has three goals:

1. Introduce SEL for practitioners.
2. Define normative language specification (syntax, conformance, execution semantics, error model).
3. Show extension path from SEL output into graph ingestion, including concrete SEL-to-Cypher mapping.

---

## Part I — Introduction

## 1. Why SEL exists

Many enterprise workflows need derivation, not presentation:

- extract evidence from canonical corpora
- map implementation artifacts to semantic facts
- preserve provenance for audit and modernization
- emit machine-processable intermediate artifacts

XSLT is powerful and general, but many derivation tasks are easier to govern when rule intent is explicit:

```text
if context matches and predicate holds
then emit typed fact into logical group
```

SEL provides this model directly.

## 2. Position in architecture

Consistent with executable-architecture model:

- `sel:` is derivation language (not ontology)
- `obs:`, `map:`, `gov:`, `prov:` are target vocabularies
- one derivation mechanism can produce multiple semantic layers

Typical flow:

```text
Source XIR/XML
   ↓
SEL rules
   ↓
Derived XIR/XML artifacts
   ↓
Interpretation / alignment / graph projection
```

### 2.1 Language and ontology separation

SEL intentionally separates derivation mechanics from enterprise meaning:

- `sel:` defines how facts are derived
- `obs:` represents observed evidence
- `map:` represents interpretation/alignment
- `gov:` represents normalized governance knowledge
- `prov:` represents provenance and justification

This separation keeps rule authoring stable even when ontology models evolve.

## 3. Relationship to existing XML stack

| Language | Primary role |
|---|---|
| XSD / Relax NG | Structural constraints |
| Schematron | Semantic constraints |
| SEL | Semantic derivation |
| XSLT | General transformation/rendering |

SEL is complementary to Schematron and XSLT, not replacement.

### 3.1 Why SEL is often simpler than direct XSLT authoring

SEL narrows problem shape to common derivation tasks:

```text
select -> construct -> emit
```

For this class of work, authors usually reason in rule statements, not template orchestration.  
XSLT remains execution-grade foundation; SEL reduces author-facing complexity by compiling into that foundation.

### 3.2 Intended users

SEL targets people who own semantics and architecture outcomes, including:

- enterprise architects
- governance specialists
- lineage and metadata engineers
- business analysts defining derivation intent

Implementation specialists can still drop to XSLT/XQuery where required.

## 4. Scope and non-goals

### In scope

- rule-based derivation from context + predicates
- grouped output emission
- evidence/context/provenance preservation
- deterministic compilation/execution semantics

### Non-goals

- full procedural control language
- generic publishing and rendering
- arbitrary side effects as language primitive

---

## Part II — Normative Specification

## 5. Conformance model

Terms **MUST**, **SHOULD**, **MAY** are normative.

### 5.1 Conformance classes

An implementation MAY conform to one or more classes:

1. **SEL-Parser**  
   Parses SEL-annotated Schematron and validates structural constraints.
2. **SEL-Compiler**  
   Produces executable derivation form (for example XSLT).
3. **SEL-Processor**  
   Executes derivation over source documents and emits grouped outputs.
4. **SEL-Cypher-Emitter** (optional extension)  
   Produces Cypher ingestion artifacts from SEL output model.

### 5.2 Minimum conformance

`SEL-Processor` conformance **MUST** include parser + compiler behavior, either explicitly or embedded.

## 6. Core data model

A derivation unit has:

- **context**: XPath context selector
- **predicate**: boolean expression (`test`)
- **emit flag**: enable/disable derivation
- **type**: semantic classification
- **group**: logical output partition
- **copy expression**: evidence payload selector
- **context expression** (optional): contextual payload selector

## 7. Surface syntax

SEL annotations are carried on Schematron rule assertions/reports.

### 7.1 Namespace

Implementations **MUST** use SEL namespace:

```xml
xmlns:sel="http://jurgenei.name/sel"
```

### 7.2 Supported SEL attributes

| Attribute | Required | Type | Meaning |
|---|---|---|---|
| `sel:emit` | yes | boolean | Enables derivation when `true` |
| `sel:type` | no | string | Emitted fact type (default implementation-defined, typically `sel`) |
| `sel:group` | no | string | Logical output group (default `default`) |
| `sel:copy` | no | XPath | Evidence payload selector (default `.`) |
| `sel:context` | no | XPath | Optional context payload selector |

### 7.3 Host elements

SEL annotations **MUST** be read from Schematron `sch:report` and `sch:assert`.

- For `sch:report`, emission condition is `test = true`.
- For `sch:assert`, emission condition is `not(test)`.

## 8. Grammar (normative profile)

EBNF-like notation for supported profile:

```ebnf
SelSchema          ::= SchSchema
SchSchema          ::= "<sch:schema ...>" Pattern* "</sch:schema>"
Pattern            ::= "<sch:pattern ...>" Rule+ "</sch:pattern>"
Rule               ::= "<sch:rule context=XPath>" (Report | Assert)+ "</sch:rule>"
Report             ::= "<sch:report test=XPathBoolean SelAttrs ...>...</sch:report>"
Assert             ::= "<sch:assert test=XPathBoolean SelAttrs ...>...</sch:assert>"
SelAttrs           ::= SelEmit SelType? SelGroup? SelCopy? SelContext?
SelEmit            ::= 'sel:emit="true|false"'
SelType            ::= 'sel:type=String'
SelGroup           ::= 'sel:group=String'
SelCopy            ::= 'sel:copy=XPath'
SelContext         ::= 'sel:context=XPath'
```

`sel:emit="true"` entries are derivation-active. Others are ignored.

## 9. Processing model (normative)

For each source document:

1. Evaluate rule contexts.
2. Evaluate assertion/report predicate semantics.
3. For each active match:
   1. evaluate `sel:copy`
   2. evaluate `sel:context` (if present)
   3. construct emitted fact record
   4. append to target group stream
4. Materialize grouped outputs.

### 9.1 Output grouping

Logical group names are mapped to output paths by configuration.

- If group path mapping missing, processor **MUST** apply deterministic default.
- Default profile path **SHOULD** be `sel/<group>.xml`.

### 9.2 Determinism

Given identical inputs (schema, params, sources), processor **MUST** produce equivalent grouped outputs.

## 10. Output record shape

Canonical output shape for this profile:

```xml
<sel:Observations group="knowledge">
  <sel:Observation type="paragraph" group="knowledge" source="report" ruleContext="c:Paragraph">
    <sel:Evidence>...</sel:Evidence>
    <sel:Context>...</sel:Context> <!-- optional -->
    <sel:Source document="canonical-order.xml" path="..."/>
  </sel:Observation>
</sel:Observations>
```

Processors MAY include additional attributes, but **MUST NOT** omit required semantic fields (`type`, `group`, evidence payload, source metadata) in this profile.

## 11. Error model

### 11.1 Static errors (compile-time)

Processor **MUST** report error for:

- invalid XPath in `context`, `test`, `sel:copy`, or `sel:context`
- missing/invalid namespace binding for `sel`
- structurally invalid host schema

### 11.2 Dynamic errors (run-time)

Processor **MUST** report error for:

- source parsing failure
- non-recoverable evaluation failure
- output materialization failure

Processor MAY offer `failOnError=false` mode; in that mode errors **MUST** be logged explicitly and not silently swallowed.

### 11.3 Unknown attributes

Unknown `sel:*` attributes MAY be ignored, but implementation **SHOULD** expose warning channel for forward compatibility diagnostics.

---

## Part III — Practical Examples

## 12. Example A: Basic mapping-style derivation

Source:

```xml
<src:Customer xmlns:src="urn:src">
  <src:Name>John Smith</src:Name>
</src:Customer>
```

Rule:

```xml
<sch:rule context="src:Customer"
          xmlns:sch="http://purl.oclc.org/dsdl/schematron"
          xmlns:sel="http://jurgenei.name/sel"
          xmlns:src="urn:src">
  <sch:report test="src:Name"
              sel:emit="true"
              sel:type="customer-name"
              sel:group="mapping"
              sel:copy="src:Name"/>
</sch:rule>
```

Output excerpt:

```xml
<sel:Observation type="customer-name" group="mapping">
  <sel:Evidence><src:Name>John Smith</src:Name></sel:Evidence>
</sel:Observation>
```

## 13. Example B: Evidence + context preservation

Rule:

```xml
<sch:report test="normalize-space(.)"
            sel:emit="true"
            sel:type="paragraph"
            sel:group="knowledge"
            sel:copy="."
            sel:context="ancestor::c:Section[1]/c:Title"/>
```

Output excerpt:

```xml
<sel:Observation type="paragraph" group="knowledge">
  <sel:Evidence><c:Paragraph>...</c:Paragraph></sel:Evidence>
  <sel:Context><c:Title>Interfaces</c:Title></sel:Context>
</sel:Observation>
```

## 14. Example C: Multi-profile grouped emission

Profiles (same source corpus):

- `knowledge`
- `terminology`
- `architecture`

Configured outputs:

```text
knowledge   -> sel/knowledge.xml
terminology -> sel/terminology.xml
architecture-> sel/architecture.xml
```

This supports independent downstream pipelines per concern.

---

## 15. SEL to Cypher extension

Cypher translation is compelling because SEL already produces:

- typed facts
- grouping
- source provenance
- stable structural context

These map naturally to graph nodes + edges.

## 16. Mapping rules (SEL output -> graph model)

### 16.1 Node mapping

| SEL field | Graph target |
|---|---|
| `sel:Observation` | `(:Observation)` |
| `@type` | `Observation.type` property |
| `@group` | `Observation.group` property |
| `sel:Source/@document` | `(:SourceDocument {name})` |
| `sel:Source/@path` | `Observation.path` property |

### 16.2 Relationship mapping

- `(:Observation)-[:OBSERVED_IN]->(:SourceDocument)`
- optional semantic label expansion by group/type (implementation policy)

## 17. End-to-end SEL -> Cypher example

Input SEL artifact (excerpt):

```xml
<sel:Observation type="relationship-candidate" group="architecture">
  <sel:Evidence>
    <c:Connector source="sap" target="crm"/>
  </sel:Evidence>
  <sel:Source document="canonical-integration.xml" path="/.../Connector[1]"/>
</sel:Observation>
```

Derived Cypher payload (parameterized):

```cypher
MERGE (d:SourceDocument {name: $document})
MERGE (o:Observation {id: $observationId})
SET o.type = $type,
    o.group = $group,
    o.path = $path,
    o.evidence = $evidenceXml
MERGE (o)-[:OBSERVED_IN]->(d)
```

Optional semantic projection:

```cypher
WITH $type AS t, $group AS g, $evidenceMap AS e
CALL {
  WITH t, g, e
  WHERE t = 'relationship-candidate' AND g = 'architecture'
  MERGE (s:System {name: e.source})
  MERGE (tgt:System {name: e.target})
  MERGE (s)-[:POTENTIAL_FLOW]->(tgt)
  RETURN 1
}
RETURN true
```

This keeps SEL output canonical, while Cypher projection remains downstream and replaceable.

## 18. Why this extension matters

SEL-to-Cypher enables:

- rapid graph bootstrapping from evidence
- reproducible lineage from fact to source
- graph-native querying for modernization and drift analysis
- decoupling between derivation and storage technology

---

## 19. Security and governance considerations

- Provenance fields (`document`, `path`) **SHOULD** be retained for traceability.
- Sensitive payloads in `sel:Evidence` **SHOULD** be redacted or policy-filtered before graph ingestion.
- Group policies **MAY** enforce separate data domains and retention windows.

## 20. Implementation notes

- SEL can be compiled to XSLT for runtime portability.
- Existing Schematron toolchains remain usable as host infrastructure.
- Processor implementations can evolve (direct interpreter, compiled runtime, hybrid) without changing SEL source semantics.

## 20.1 Future directions

Likely expansion areas (non-normative):

- reusable SEL rule libraries
- rule testing/conformance fixtures
- graph-oriented emitters beyond Cypher
- AI-assisted rule synthesis with deterministic validation
- namespace-aware refactoring assistance

## 21. Conclusion

SEL provides focused derivation language that sits between validation and transformation:

- precise enough for normative execution
- simple enough for practitioners
- extensible enough for graph-oriented projections such as Cypher

As architecture recovery and modernization workflows mature, SEL offers stable semantic bridge from source evidence to enterprise knowledge systems.
