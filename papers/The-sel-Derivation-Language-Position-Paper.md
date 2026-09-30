# The sel: Derivation Language
## A Simpler Alternative to XSLT for Enterprise Knowledge Derivation

### Abstract

Enterprise knowledge extraction, lineage construction, governance normalization, and modernization initiatives frequently rely on XSLT or custom software. While XSLT 3.0 is powerful, its learning curve can be significant for architects, analysts, governance specialists, and modernization engineers.

This paper introduces **sel:**, a derivation language focused on selection, construction, and emission. Rather than replacing XSLT, sel provides a simpler abstraction for the common derivation tasks encountered when constructing enterprise knowledge graphs from architecture models, source code, databases, files, configuration, and metadata.

The language is namespace-agnostic and may be expressed using either XML syntax or XIR syntax. It serves as a common derivation mechanism for evidence extraction, ontology alignment, governance normalization, and provenance generation.

---

# 1. Introduction

Many enterprise transformation problems can be reduced to a common pattern:

```text
Find something
     ↓
Capture context
     ↓
Create an output artifact
```

Examples include:

- extracting observations from PL/SQL
- discovering architecture dependencies
- generating governance concepts
- creating provenance relationships
- normalizing enterprise metadata

Traditionally such problems are solved using:

```text
XSLT
XQuery
Custom Java
Custom Python
```

These technologies are extremely capable but often expose more complexity than required for simple derivation tasks.

sel was created to provide a smaller and more approachable abstraction.

---

# 2. Design Goals

The language was designed around five principles.

## Simplicity

Users should focus on derivation intent rather than transformation mechanics.

## Namespace Independence

The language must not be tied to a specific ontology.

## Multi-Stage Derivation

The same language should support:

```text
Evidence extraction
Ontology alignment
Governance normalization
Provenance generation
```

## XSLT Compatibility

The language should be compilable into XSLT or XQuery.

## Human Readability

Architects and analysts should be able to understand derivation logic without becoming XSLT experts.

---

# 3. Relationship to XSLT

sel is not intended to replace XSLT.

Instead:

```text
sel
    ↓
Compiler
    ↓
XSLT 3.0
    ↓
Result
```

The relationship is similar to SQL and query execution plans.

Most users write SQL.

Few users write execution plans.

Likewise most users should write sel.

Few users should need to write XSLT.

---

# 4. Core Concepts

The language focuses on three fundamental operations.

## Selection

```text
What should be selected?
```

Examples:

```text
ora:Procedure
archi:ApplicationComponent
dsa:Table
```

## Construction

```text
What should be created?
```

Examples:

```text
obs:Observation
map:Scenario
gov:BusinessCapability
```

## Emission

```text
What should be produced?
```

The output vocabulary is intentionally unconstrained.

---

# 5. Position in the Architecture

```text
Source XIR
      ↓
sel
      ↓
Target XIR
```

Possible outputs:

```text
obs:XIR
map:XIR
gov:XIR
prov:XIR
```

sel is therefore a derivation language rather than an ontology.

---

# 6. Separation of Language and Ontology

A key architectural principle is separation of concerns.

```text
sel:
    How something is derived

obs:
    What was observed

map:
    What it means

gov:
    What we know

prov:
    Why we believe it
```

This distinction keeps derivation logic independent from enterprise semantics.

---

# 7. XML Representation

A derivation can be expressed in XML.

```xml
<sel:rule match="ora:Procedure">
    <sel:emit target="obs:Observation"/>
</sel:rule>
```

The precise syntax may evolve, but the principle remains:

```text
Select
Construct
Emit
```

---

# 8. XIR Representation

The same derivation can be expressed using XIR.

```xir
(sel:Rule
   {match ora:Procedure}
   (sel:Emit obs:Observation))
```

Because sel operates above concrete syntax, both XML and XIR become possible authoring formats.

---

# 9. Evidence Extraction

Example:

```text
ora:
      ↓ sel:
obs:
```

```text
sp_enrich_customer
      ↓
Observation
```

The purpose is to capture evidence.

No enterprise interpretation is required at this stage.

---

# 10. Ontology Alignment

Example:

```text
obs:
      ↓ sel:
map:
```

```text
Procedure Observation
      ↓
Customer Enrichment
```

This creates alignment artifacts that connect observations to enterprise concepts.

---

# 11. Governance Normalization

Example:

```text
map:
      ↓ sel:
gov:
```

Result:

```text
gov:CustomerEnrichment
```

Governance normalization is therefore not a special mechanism.

It is another derivation.

---

# 12. Provenance Generation

Example:

```text
gov:
      ↓ sel:
prov:
```

The resulting provenance graph explains:

```text
Where a concept came from
Which rule created it
Which source was involved
```

---

# 13. Why sel Is Easier Than XSLT

The goal is not to match XSLT feature-for-feature.

The goal is to solve common derivation tasks with fewer concepts.

A typical sel author only needs to understand:

```text
Selection
Construction
Emission
Namespaces
```

By contrast, effective XSLT development often requires understanding:

```text
Templates
XPath
Modes
Variables
Functions
Sequences
Packages
```

sel intentionally limits scope to remain approachable.

---

# 14. Intended Users

sel is designed for:

- architects
- governance specialists
- business analysts
- lineage engineers
- metadata engineers

Advanced implementation specialists may still work directly with XSLT or XQuery when required.

---

# 15. Future Directions

Potential future capabilities include:

- reusable derivation libraries
- graph emission
- namespace-aware refactoring
- AI-assisted rule generation
- derivation testing frameworks

The language should remain focused on derivation rather than evolve into a general-purpose programming language.

---

# 16. Conclusion

sel was created to simplify enterprise derivation tasks.

By focusing on selection, construction, and emission, the language provides a more approachable abstraction than XSLT while preserving the ability to compile to powerful execution engines.

The language is intentionally independent of observation, governance, lineage, or provenance concerns.

Instead, it serves as a common derivation mechanism capable of producing evidence, alignment models, governance concepts, and provenance artifacts from a shared architectural foundation.
