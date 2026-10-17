# XIR: Structured Knowledge for Complex Systems

Most enterprise systems are operationally reliable but cognitively hard to understand.
XIR exists to make structure explicit so teams can trace, govern, modernize, and reason over complex information with less ambiguity.

## Narrative spine

```text
Structured Knowledge
↓
XIR
↓
Observation
↓
Derivation
↓
Knowledge
↓
Applications
```

## What XIR is

XIR (eXtensible Intermediate Representation) is a compact structural representation for documents, code structures, architecture models, and metadata.

- **XIR language**: concrete syntax and structural forms (`()`, `{}`, `[]`, document/comment/PI nodes).
- **XIR information model**: normalized structure used for transformation, validation, lineage, and reasoning.
- **XIR ecosystem**: tools and applications built on top of the language and model.

Canonical XIR entry point: [xir-starter-kit](xir-starter-kit/README.md)

## Why XIR exists

Without a shared representation layer, teams repeatedly remap semantics between extraction tools, transformation pipelines, lineage models, and governance platforms.
XIR gives one structural substrate across those stages.

## XIR vs XML, JSON, RDF

- **XML**: strong document syntax; XIR keeps structural rigor while reducing syntax overhead for derivation and transformation workflows.
- **JSON**: strong exchange format; XIR adds explicit document/element semantics, namespace handling, and mixed structural forms used in model-driven processing.
- **RDF**: strong graph semantics; XIR does not replace RDF, but provides a structural bridge that can host RDF-oriented models alongside executable transformation artifacts.

## 5-minute reading path

1. **Learn language quickly**: [xir-starter-kit/spec/XIR-QUICK-REFERENCE.md](xir-starter-kit/spec/XIR-QUICK-REFERENCE.md)
2. **See full definition**: [xir-starter-kit/spec/XIR-LANGUAGE.md](xir-starter-kit/spec/XIR-LANGUAGE.md)
3. **See observation→derivation mechanics**: [xir-g4](https://github.com/jurgenei/xir-g4)
4. **See application**: [example-lineage-plsql](https://github.com/jurgenei/example-lineage-plsql)

## Repository map

### Core XIR

- [xir-starter-kit](xir-starter-kit/README.md): language definition, quick reference, examples.
- [xir-sax](https://github.com/jurgenei/xir-sax): parser/serializer and XMLReader for XIR.
- [intellij-xir](https://github.com/jurgenei/intellij-xir): IntelliJ plugin support.

### Observation and derivation toolchain

- [xir-g4](https://github.com/jurgenei/xir-g4): grammar-to-model and AST derivation.
- [gradle-antlr-plugin](https://github.com/jurgenei/gradle-antlr-plugin): grammar-backed AST extraction tasks.
- [gradle-xml-plugin](https://github.com/jurgenei/gradle-xml-plugin): transformation/validation tasks with XML/XIR routing.
- [gradle-ooxml-plugin](https://github.com/jurgenei/gradle-ooxml-plugin): deterministic OOXML observation into canonical structures.
- [gradle-archi-plugin](https://github.com/jurgenei/gradle-archi-plugin): architecture model extraction tasks.

### Knowledge and applications

- [example-lineage-plsql](https://github.com/jurgenei/example-lineage-plsql): executable lineage pipeline from artifacts to graph verification.
- [react-svg-lineage-browser](https://github.com/jurgenei/react-svg-lineage-browser): lineage visualization.

## Papers by narrative level

### Foundation: why structured knowledge

1. [Extract, Derive, Resolve, Publish](papers/Extract-Derive-Resolve-Publish-Paper.md)
2. [Universal Semantic Lineage Architecture](papers/Universal_Semantic_Lineage_Architecture.md)
3. [Continuous Architecture Reconciliation](papers/continuous-architecture-reconcilliation.md)

### Core model and standards positioning

1. [XIR Structural Model and S-Expression Serialization](papers/XIR_Structural_Model_Paper.md)
2. [XIR Implementation Specification](papers/XIR-Implementation-Spec.md)
3. [XIR Positioning Note](papers/XIR_W3C_Positioning_Note.md)
4. [XIR W3C Merged Paper](papers/XIR_W3C_Merged_Paper.md)

### Observation and derivation methods

1. [ANTLR G4 to AST Classes Specification](papers/ANTLR_G4_to_AST_Classes_Spec.md)
2. [SEL Derivation Language](papers/SEL-Derivation-Language.md)
3. [Executable Architecture to Enterprise Behavior Graphs](papers/Executable-Architecture-Enterprise-Behavior-Graphs.md)

### Governance and knowledge applications

1. [Evidence-Oriented Enterprise Knowledge Graph Federation](papers/XIR-GOV-Evidence-Oriented-Enterprise-Knowledge-Graph-Federation.md)
2. [Observation-Driven Ontology Alignment](papers/Observation-Driven_Ontology_Alignment.md)
3. [RDF, SKOS and the Collibra Model](papers/RDF_SKOS_Collibra_Mapping.md)

## Why this matters

Governance, lineage, modernization, AI context engineering, enterprise architecture, and knowledge graphs are application layers on top of same representation foundation.
When observation and derivation share one substrate, knowledge becomes reproducible, testable, and auditable.
