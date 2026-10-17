# ShaclXslt + SEL + Cypher Roadmap

## Vision

Create for SHACL what SchXslt did for Schematron:

```text
SHACL
    ↓
ShaclXslt
    ↓
XSLT 3
    ↓
Saxon
```

and extend it with SEL:

```text
SHACL
    +
SEL
    ↓
ShaclXsltSel
    ↓
XIR/gir
    ↓
Cypher / Neo4j / Other Graph Stores
```

Guiding principle:

- XIR owns semantics.
- SHACL owns graph constraints.
- SEL owns observations.
- gir:* owns graph representation.
- XSLT 3 is the execution engine.
- Neo4j is a persistence target.

---

## Phase 0 - Architecture Baseline

### Decisions

- Keep SEL independent from Schematron and SHACL.
- Treat SEL as an observation language.
- Introduce `gir:*` namespace inside XIR.
- Make GIR the common result model.

### Deliverable

Architecture document:

```text
XIR
 ├─ schematron
 ├─ shacl
 ├─ sel
 └─ gir
```

---

## Phase 1 - GIR Namespace

Define graph vocabulary.

### Core Elements

```xml
<gir:graph/>
<gir:node/>
<gir:edge/>
<gir:path/>
<gir:subgraph/>
<gir:impactSet/>
<gir:lineage/>
```

### Node Model

```xml
<gir:node
    id="dataset:orders"
    type="Dataset"/>
```

### Edge Model

```xml
<gir:edge
    source="dataset:orders"
    target="report:sales"
    type="FEEDS"/>
```

### Deliverable

`xir-gir.xsd`

---

## Phase 2 - RDF to XIR/GIR

Create ingestion pipeline.

```text
Turtle
JSON-LD
RDF/XML
    ↓
Jena
    ↓
XIR/gir
```

### Deliverables

- RDF importer.
- RDF → XIR conversion.
- RDF → GIR conversion.

---

## Phase 3 - Minimal SHACL AST

Create internal model.

### Supported Shapes

- NodeShape
- PropertyShape
- TargetClass
- Path
- Datatype
- Class
- MinCount
- MaxCount

### Deliverable

```text
SHACL
   ↓
AST
```

---

## Phase 4 - Graph Runtime in XSLT

Build runtime before compiler.

### Runtime Functions

```xpath
gir:get-node()
gir:get-edges()
gir:outgoing()
gir:incoming()
gir:reachable()
gir:path()
```

### Representation

Use XDM maps or XML GIR.

### Deliverable

`gir-runtime.xsl`

---

## Phase 5 - ShaclXslt MVP

Compile SHACL to XSLT.

```text
SHACL
   ↓
ShaclXslt
   ↓
XSLT
```

### First Constraints

- sh:minCount
- sh:maxCount
- sh:datatype
- sh:class
- targetClass

### Output

Validation result model.

---

## Phase 6 - SHACL SEL Binding

Define graph observation semantics.

### Observation Types

```text
Observation
Path
Subgraph
Lineage
ImpactSet
DependencyClosure
```

### Example

```text
observation DownstreamImpact
```

Result:

```xml
<gir:subgraph>
    ...
</gir:subgraph>
```

---

## Phase 7 - ShaclXsltSel

Compiler pipeline.

```text
SHACL + SEL
      ↓
 ShaclXsltSel
      ↓
      GIR
```

### Deliverables

- Observation extraction.
- Graph materialization.
- Lineage extraction.
- Impact analysis extraction.

---

## Phase 8 - Cypher Generator

Generate persistence operations.

```text
GIR
  ↓
Cypher
```

### Examples

```cypher
MERGE (d:Dataset {id:$id})
```

```cypher
MERGE (d)-[:FEEDS]->(r)
```

### Deliverables

- GIR→Cypher generator.
- Batch loading support.

---

## Phase 9 - Neo4j Driver

Abstract persistence.

```text
GIR
  ↓
Graph Driver
  ↓
Neo4j
```

### Interfaces

```java
GraphStore
GraphTransaction
GraphQuery
```

### Goal

Keep Neo4j replaceable.

---

## Phase 10 - Collibra Subset

Implement reference ontology.

### Initial Concepts

- Community
- Domain
- Asset
- Dataset
- BusinessTerm
- Person
- Steward

### Purpose

Reference implementation and acceptance test.

---

## Phase 11 - Lineage & Impact

Model first-class graph observations.

### Observations

```text
Lineage
ImpactAnalysis
RootCauseAnalysis
OwnershipChain
DependencyClosure
```

### Outputs

```xml
<gir:lineage/>
<gir:impactSet/>
<gir:path/>
```

---

## Success Criteria

### V1

```text
SHACL
    ↓
ShaclXslt
    ↓
Validation XSLT
```

### V2

```text
SHACL + SEL
      ↓
ShaclXsltSel
      ↓
GIR
```

### V3

```text
Documents
      ↓
Schematron + SEL
      ↓
GIR
      ↓
SHACL + SEL
      ↓
Neo4j
```

### End State

A unified platform where:

```text
Schematron + SEL
       ↓
      GIR

SHACL + SEL
       ↓
      GIR

GIR
       ↓
Cypher / Neo4j / Graph Stores
```
