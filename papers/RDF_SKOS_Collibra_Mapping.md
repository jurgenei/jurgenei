# RDF, SKOS and the Collibra Model

## Executive Summary

RDF/SKOS maps onto Collibra reasonably well at a conceptual level, but not at a native implementation level.

Collibra is fundamentally an asset-centric metadata graph. RDF is a graph of triples. SKOS is an RDF vocabulary for representing taxonomies, thesauri, and controlled vocabularies.

The concepts align, but the implementation models differ.

## Concept Mapping

| RDF / SKOS | Collibra |
|------------|----------|
| skos:ConceptScheme | Domain, Taxonomy, or Glossary Container |
| skos:Concept | Asset |
| URI | Asset ID or External Identifier |
| skos:prefLabel | Asset Name |
| skos:definition | Description |
| skos:altLabel | Synonyms Attribute |
| skos:broader | Parent-Child Relation |
| skos:narrower | Child-Parent Relation |
| skos:related | Asset Relation |
| SKOS Collection | Group of Assets |
| RDF Triple | Relation Between Assets |

## Structural Comparison

### RDF / SKOS

```turtle
customer skos:broader party .
```

Interpretation:

- Subject: customer
- Predicate: skos:broader
- Object: party

### Collibra

```text
Asset: Customer
Relation Type: is part of
Asset: Party
```

Semantically these represent the same relationship.

## Mental Model Mapping

```text
SKOS/RDF                  Collibra

Concept ----------------> Asset
Predicate --------------> Relation Type
Literal ----------------> Attribute
ConceptScheme ----------> Domain/Taxonomy
Triple -----------------> Graph Edge
```

## Where Cognitive Load Appears

The friction often comes from attempting to preserve the RDF worldview inside a governance platform.

Each layer introduces additional abstractions:

- RDF introduces graph semantics.
- SKOS introduces controlled-vocabulary semantics.
- OWL introduces formal ontology semantics.
- Enterprise ontologies introduce governance and modeling disciplines.
- Collibra introduces operational metadata governance.

Each abstraction has value, but also increases the amount of knowledge required to understand and maintain the solution.

## Reduction to Business Value

Many organizations ultimately need:

- Definitions
- Ownership
- Lineage
- Searchability
- Governance

This can be summarized as:

```text
Business wants:
- definitions
- ownership
- lineage
- searchability

RDF wants:
- formal graph semantics

SKOS wants:
- concept hierarchies

Collibra wants:
- governed metadata
```

These are related concerns, but they are not identical.

## Practical Conclusion

For most Collibra implementations:

- Assets
- Asset types
- Domains
- Attributes
- Relations

provide roughly 80-90% of the value obtained from a SKOS-aligned model while imposing substantially less cognitive overhead.

RDF and SKOS become compelling when there is a real requirement for:

- Knowledge graph interoperability
- Linked Data ecosystems
- Standard vocabularies
- Ontology-driven integration
- Semantic reasoning
- External graph exchange

Otherwise, the additional semantic precision often costs more effort than the business value it returns.
