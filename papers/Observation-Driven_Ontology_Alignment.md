# Observation-Driven Ontology Alignment

## Abstract

Observation-Driven Ontology Alignment treats ontology alignment as evidence-first engineering, not taxonomy-first modeling.  
Source namespaces stay intact, observations capture evidence, interpretation maps evidence to governance meaning, and projections publish result to downstream platforms.

## Document Relationship

This paper owns semantic method and terminology for alignment.

System-level architecture, derivation scenarios, modernization outcomes, and regulatory projection examples are defined in companion paper:

- [From Executable Architecture to Enterprise Behavior Graphs](./Executable-Architecture-Enterprise-Behavior-Graphs.md)
- [Observation Model](./Executable-Architecture-Enterprise-Behavior-Graphs.md#obs-model)
- [Evidence and Interpretation](./Executable-Architecture-Enterprise-Behavior-Graphs.md#evidence-interpretation)
- [Regulatory Lineage as Projection](./Executable-Architecture-Enterprise-Behavior-Graphs.md#regulatory-projection)

Use this paper as canonical source for term boundaries and alignment flow.  
Use companion paper for full enterprise behavior graph narrative.

## Core Thesis

Traditional integration approaches map systems directly to target platforms.

```text
Source A -> Collibra
Source B -> Collibra
Source C -> Collibra
```

Observation-driven alignment inserts explicit semantic stages between sources and tools.

```text
Source namespaces
        ↓
Observation extraction (evidence)
        ↓
Interpretation + ontology alignment (meaning)
        ↓
Governance normalization (gov namespace)
        ↓
Enterprise graph federation
        ↓
Consumer projections (Neo4j / Collibra-compatible API / RDF / GraphQL)
```

## Architectural Principles

### Namespace Ownership

Every source owns its vocabulary.

```text
obs:
ora:
dsa:
sdp:
file:
archi:
```

No source vocabulary is treated as canonical.

### Governance Namespace
<a id="governance-namespace"></a>

Governance namespace provides semantic convergence point.

```text
gov:ApplicationLandscape
gov:Application
gov:ApplicationComponent
gov:Dataset
gov:BusinessTerm
gov:Consumes
gov:FlowsTo
```

Application hierarchy semantics:

- `gov:ApplicationLandscape`: enterprise application estate boundary
- `gov:Application`: business application capability boundary
- `gov:ApplicationComponent`: deployable/logical component implementing part of application behavior

### Observation Before Interpretation
<a id="observation-before-interpretation"></a>

Observation extracts evidence from corpus.  
Observation does **not** assign governance meaning.

Evidence categories include:

- structure
- naming
- references
- lineage-relevant relationships
- contextual fragments

### Interpretation Before Projection
<a id="interpretation-before-projection"></a>

Interpretation maps evidence to governance concepts and relations.  
Projection serializes already-interpreted governance model to target consumers.

### Alignment Is Explicit, Versioned, Auditable

Type mappings, relation mappings, and attribute mappings are governed assets.  
Alignment decision must be traceable to evidence and rule.

## End-to-End Alignment Flow
<a id="end-to-end-alignment-flow"></a>

```text
Source XIR
      ↓
Observation extraction rules (e.g., Schematron obs:* annotations)
      ↓
Observation XML (evidence artifacts)
      ↓
Ontology alignment + interpretation rules
      ↓
Governance normalization
      ↓
gov:XIR
      ↓
Enterprise graph
      ↓
Consumer projections
```

## Canonical Terms (Glossary)
<a id="glossary-canonical-terms"></a>

| Term | Definition | Boundary |
|---|---|---|
| Observation | Evidence-bearing artifact extracted from source corpus. | Not governance meaning. |
| Evidence | Source-backed payload and context used to justify governance fact. | Must be reproducible from source + rule. |
| Interpretation | Semantic step mapping observations to governance concepts/relations. | Must be explicit and rule-driven. |
| Ontology alignment | Cross-namespace semantic equivalence/translation into governance ontology. | Not platform mapping. |
| Governance normalization | Materialization into canonical `gov:*` vocabulary. | Independent from consumer tool. |
| Projection | Serialization/exposure of normalized governance model to specific platforms/APIs. | Must not redefine semantics. |

## Minimal Worked Example

```text
archi:ApplicationComponent
```

Observed as:

```text
Executable application boundary
```

Interpreted as:

```text
gov:ApplicationComponent
```

Interpretation rule (conceptual):

```text
if observation indicates executable application boundary
then map source type to gov:ApplicationComponent
```

Contextual roll-up rule (conceptual):

```text
if multiple gov:ApplicationComponent nodes share bounded context and ownership
then derive parent gov:Application

if multiple gov:Application nodes belong to same enterprise estate scope
then derive gov:ApplicationLandscape
```

## Collibra Compatibility (Projection Rule)

Canonical `gov` semantics remain independent from Collibra object model.  
Projection may reshape types for consumer compatibility without changing canonical meaning.

| Source semantics | Canonical governance | Collibra projection (preferred) |
|---|---|---|
| ArchiMate landscape scope | `gov:ApplicationLandscape` | Domain / community-scoped grouping construct |
| `archi:ApplicationComponent` (behavior unit) | `gov:ApplicationComponent` | Technical asset mapped under Business Application context |
| Grouped business capability boundary | `gov:Application` | Business Application |

Projection adapter responsibility:

```text
gov:* is source of truth
Collibra type mapping is downstream representation
```

## Enterprise Graph

Enterprise graph stores both governance facts and evidence trail:

- normalized governance concepts (`gov:*`)
- observations and evidence pointers
- interpretation/alignment rule references
- provenance/lineage chain
- business term links

Graph becomes canonical semantic system of record.  
Each projected consumer view is derivative.

## Cross-Document Alignment Status (First Pass, Read-Only)

| Anchor concept | XIR-GOV paper | schematron-based-extraction | gradle-xml-plugin README | example-lineage-plsql README |
|---|---|---|---|---|
| Observation as evidence | Exact | Exact | Partial | Partial |
| Observation ≠ interpretation | Partial | Exact | Partial | Missing |
| Governance namespace normalization | Exact | Partial | Partial | Missing |
| Projection after interpretation | Exact | Partial | Missing | Missing |
| Evidence-first lineage/provenance | Exact | Exact | Partial | Exact |

## Deferred Harmonization Notes

First pass scope edits only this anchor paper.  
Follow-up pass should align wording in supporting docs where status is Partial or Missing.

## Conclusion

Observation-Driven Ontology Alignment separates evidence from meaning and meaning from distribution.  
Governance emerges from reproducible observations, explicit alignment rules, and canonical normalization, then projects cleanly to multiple consumer platforms without semantic lock-in.
