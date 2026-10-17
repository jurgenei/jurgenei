# ShaclXsltSel Roadmap (v2)

## Vision

Build for SHACL what SchXslt built for Schematron.

```text
SHACL
   ↓
ShaclXslt
   ↓
XSLT 3
   ↓
Saxon
```

Extend the model with SEL observation semantics:

```text
SHACL + SEL
      ↓
 ShaclXsltSel
      ↓
      XIR
```

## Core Principles

- XIR owns semantics.
- SEL owns observations.
- Schematron owns tree constraints.
- SHACL owns graph constraints.
- XSLT/Saxon is the universal execution runtime.
- Cypher and Neo4j are persistence targets.
- No GIR intermediate artifact.
- Output namespaces are configurable.

## Architecture

```text
                   XIR
                    │
      ┌─────────────┼─────────────┐
      │                           │
 Schematron                    SHACL
      │                           │
      └───────┬─────────┬─────────┘
              │         │
            SEL       SEL
              │         │
              └────┬────┘
                   │
             XSLT Runtime
                 Saxon
                   │
                  XIR
                   │
      ┌────────────┼────────────┐
      │            │            │
    Cypher      Neo4j      Other Graphs
```

## Phase 0 - Foundation

### Deliverables

- Architecture decision record.
- XIR graph concepts.
- SEL observation model.
- Output namespace strategy.

## Phase 1 - RDF Integration

### Goals

Support:

- Turtle
- RDF/XML
- JSON-LD
- N-Triples

### Pipeline

```text
RDF Serialization
        ↓
      Jena
        ↓
       XIR
```

## Phase 2 - SHACL AST

### Initial Support

- NodeShape
- PropertyShape
- targetClass
- path
- class
- datatype
- minCount
- maxCount

### Deliverable

```text
SHACL
  ↓
 AST
```

## Phase 3 - XSLT Graph Runtime

### Runtime Functions

```text
node()
relation()
outgoing()
incoming()
reachable()
path()
```

### Goal

Provide reusable graph navigation primitives for generated stylesheets.

## Phase 4 - ShaclXslt MVP

### Compilation

```text
SHACL
  ↓
ShaclXslt
  ↓
XSLT
```

### Initial Constraints

- datatype
- class
- minCount
- maxCount
- targetClass

### Output

Validation result vocabulary.

## Phase 5 - SEL Binding

### Observation Concepts

- Observation
- Subject
- Aggregation
- Group
- Identity
- Namespace

### Graph Observation Types

- Path
- Lineage
- ImpactAnalysis
- RootCauseAnalysis
- OwnershipChain
- DependencyClosure

## Phase 6 - ShaclXsltSel

### Compilation

```text
SHACL + SEL
      ↓
 ShaclXsltSel
      ↓
      XIR
```

### Output Examples

```xml
<lineage:path/>
```

```xml
<impact:impact-set/>
```

```xml
<ownership:chain/>
```

Namespaces are configurable.

## Phase 7 - XIR to Cypher

### Purpose

Materialize graph observations.

```text
XIR
 ↓
Cypher
```

### Example

```cypher
MERGE (d:Dataset {id:$id})
```

## Phase 8 - Neo4j Driver

### Abstractions

```java
GraphStore
GraphTransaction
GraphQuery
```

### Goal

Keep persistence implementation replaceable.

## Phase 9 - Reference Ontology

Collibra-style subset:

- Community
- Domain
- Asset
- Dataset
- BusinessTerm
- Person
- Steward

Used as acceptance tests, not as architecture drivers.

## Phase 10 - Lineage and Impact

### First-Class Observations

- Lineage
- ImpactAnalysis
- RootCauseAnalysis
- DependencyClosure
- OwnershipChain

### Example Use Cases

- Root cause investigation
- Downstream impact analysis
- Ownership discovery
- Governance tracing

## Success Criteria

### V1

```text
SHACL
 ↓
ShaclXslt
 ↓
XSLT Validation
```

### V2

```text
SHACL + SEL
      ↓
 ShaclXsltSel
      ↓
      XIR
```

### V3

```text
Documents
   ↓
Schematron + SEL
   ↓
      XIR
   ↓
 SHACL + SEL
   ↓
      XIR
   ↓
Neo4j / Cypher
```

## End State

One observation language.

```text
SEL
```

Two bindings.

```text
SEL + Schematron
SEL + SHACL
```

One execution runtime.

```text
XSLT / Saxon
```

One semantic model.

```text
XIR
```
