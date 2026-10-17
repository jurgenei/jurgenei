# XIR Starter Kit

XIR (eXtensible Intermediate Representation) is a compact representation syntax based on XDM concepts and S-expression form.

This package provides one canonical entry point for people and agents. Copy the complete directory into a repository, SharePoint library, or knowledge collection. Keep the specification canonical. Link to it instead of duplicating it.

## Start here

1. Read [spec/XIR-QUICK-REFERENCE.md](spec/XIR-QUICK-REFERENCE.md).
2. Read [spec/XIR-LANGUAGE.md](spec/XIR-LANGUAGE.md).
3. Inspect `examples/`.
4. Give coding agents [.github/AGENTS.md](.github/AGENTS.md).
5. Use [SKILLS.md](SKILLS.md) when an agent system supports repository skills.

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

## Design boundary

XIR is a representation foundation. Observation, lineage, governance, architecture, modernization, and knowledge-management models are applications or vocabularies built on top of XIR. They are not part of the XIR core.

## Canonical-files rule

- [spec/XIR-LANGUAGE.md](spec/XIR-LANGUAGE.md) defines the language.
- [spec/XIR-QUICK-REFERENCE.md](spec/XIR-QUICK-REFERENCE.md) is the concise teaching aid.
- `examples/` contains normative teaching examples unless an example says that it is illustrative.
- Other locations should link to these files instead of copying their content.

## License

Prepared for use with the MIT-licensed XIR project. Add the canonical project `LICENSE` file when publishing this package.
