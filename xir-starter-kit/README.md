# XIR Starter Kit

XIR (eXtensible Intermediate Representation) is compact structural language for representing documents and models with minimal syntax overhead.

This package is canonical entry point for first-time readers and coding agents. Copy whole directory into repository, SharePoint library, or knowledge collection. Link to spec files instead of duplicating syntax explanations.

## Narrative spine position

This repository covers **XIR layer** in organization spine:

```text
Structured Knowledge -> XIR -> Observation -> Derivation -> Knowledge -> Applications
```

Observation, lineage, governance, modernization, and knowledge graphs are application layers above this kit.

## Start here

1. Read [spec/XIR-QUICK-REFERENCE.md](spec/XIR-QUICK-REFERENCE.md).
2. Read [spec/XIR-LANGUAGE.md](spec/XIR-LANGUAGE.md).
3. Inspect `examples/`.
4. Give coding agents [.github/AGENTS.md](.github/AGENTS.md).
5. Use [SKILLS.md](SKILLS.md) when an agent system supports repository skills.

Then continue to:
- [xir-g4](https://github.com/jurgenei/xir-g4) for observation/derivation pipeline.
- [example-lineage-plsql](https://github.com/jurgenei/example-lineage-plsql) for application example.

## Core notation

```xir
(.
  {xmlns "urn:example"}

  (customer
    {id "123"}
    (name "Jurgen")
    ["architect" "developer"]
  )
)
```

## Canonical-files rule

- [spec/XIR-LANGUAGE.md](spec/XIR-LANGUAGE.md) defines the language.
- [spec/XIR-QUICK-REFERENCE.md](spec/XIR-QUICK-REFERENCE.md) is the concise teaching aid.
- `examples/` contains normative teaching examples unless an example says that it is illustrative.
- Other locations should link to these files instead of copying their content.

## License

Prepared for use with the MIT-licensed XIR project. Add the canonical project `LICENSE` file when publishing this package.
