# Credit Risk Architecture: SEL, XSLT, XIR

## Vision

- SEL = declarative rule language
- XSLT = universal execution runtime
- XIR = common interchange model
- SVRL = tree-oriented result vocabulary
- GIR = graph-oriented result vocabulary
- Quiplets = AI-oriented semantic representation

## Architecture

```mermaid
flowchart TD

    SHACL[SHACL Shapes]
    SCH[Schematron Rules]

    SHACL --> SHACLSEL[SHACL-SEL]
    SCH --> SCHSEL[Schematron-SEL]

    SHACLSEL --> SHACLXSLT[SHACL-XSLT Transpiler]
    SCHSEL --> SCHXSLT[SCHXSLT Transpiler]

    SHACLXSLT --> XSLT[XSLT Runtime]
    SCHXSLT --> XSLT

    XIR[XIR Data Model] --> XSLT

    XSLT --> SVRL[SVRL Reports]
    XSLT --> GIR[GIR Graph Results]
    XSLT --> XIR_OUT[XIR Output]
    XSLT --> CYPHER[Cypher]

    CYPHER --> GRAPH[(Graph Store)]
    GRAPH --> GIR
```

## Credit Risk Domain

```mermaid
graph TD

Customer --> Loan
Loan --> Exposure
Loan --> Collateral
Customer --> Guarantee

Exposure --> Rating
Exposure --> ProbabilityOfDefault
Exposure --> LossGivenDefault
Exposure --> ExpectedLoss
```

## SEL Example

```sel
for Exposure
assert ProbabilityOfDefault <= 1
assert LossGivenDefault <= 1
assert ExpectedLoss =
       ExposureAmount *
       ProbabilityOfDefault *
       LossGivenDefault
```

## Schematron Path

```mermaid
flowchart LR

XML --> XIR
XIR --> SEL
SEL --> XSLT
XSLT --> SVRL
```

Example result:

```text
SVRL
 ├─ failed-assert
 ├─ successful-report
 └─ diagnostics
```

## SHACL Path

```mermaid
flowchart LR

Graph --> XIR
XIR --> SEL
SEL --> XSLT
XSLT --> Cypher
Cypher --> GraphStore
GraphStore --> GIR
```

## Credit Concentration Rule

```sel
for Customer
report
sum(ExposureAmount)
group by Sector
where
sum(ExposureAmount) >
0.25 * TotalPortfolio
```

Generated Cypher:

```cypher
MATCH
 (c:Customer)-[:OWNS]->(e:Exposure)
RETURN
 c.sector,
 sum(e.amount)
```

## GIR Example

```text
GIR
 └─ Result
     ├─ Sector = RealEstate
     ├─ Exposure = 470M
     ├─ PortfolioShare = 31%
     └─ Status = HighConcentration
```

## AI Semantic Extraction

```text
(Customer, owns, Exposure)
(Exposure, hasPD, 0.03)
(Exposure, hasLGD, 0.42)
(ExpectedLoss, derivedFrom, ExposureAmount)
(RealEstate, exceedsConcentrationLimit, true)
```

## AI Pipeline

```mermaid
flowchart LR

XIR --> Quiplets
SVRL --> Quiplets
GIR --> Quiplets
Quiplets --> KnowledgeGraph
KnowledgeGraph --> AI
```

## Summary

```text
SEL       = one rule language
XSLT      = one execution runtime
XIR       = one interchange model
SVRL      = tree result vocabulary
GIR       = graph result vocabulary
Quiplets  = AI reasoning substrate
```
