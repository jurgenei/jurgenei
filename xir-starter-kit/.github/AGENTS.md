# Agent Instructions for XIR

Use XIR terms consistently. Do not invent syntax or domain semantics.

## Read order

1. Read [spec/XIR-QUICK-REFERENCE.md](../spec/XIR-QUICK-REFERENCE.md).
2. Read [spec/XIR-LANGUAGE.md](../spec/XIR-LANGUAGE.md).
3. Inspect the closest relevant file in `examples/`.

XIR sits at **XIR layer** in spine:

```text
Structured Knowledge -> XIR -> Observation -> Derivation -> Knowledge -> Applications
```

For fixed syntax meanings (`(. ...)`, maps, arrays, attribute-map position, namespace rules), treat [spec/XIR-QUICK-REFERENCE.md](../spec/XIR-QUICK-REFERENCE.md) and [spec/XIR-LANGUAGE.md](../spec/XIR-LANGUAGE.md) as canonical source.

## Required behavior

- Preserve S-expression form. Write `(root ...)`, not `root(...)`.
- Preserve namespaces and qualified names.
- Preserve the distinction between an attribute map and map-valued content.
- Preserve arrays as arrays.
- Keep domain vocabulary separate from XIR core syntax.
- State uncertainty when the repository specification does not define a rule.
- Do not infer missing domain semantics from names alone.

## Validation checklist

Before changing an XIR file:

1. Check balanced `()`, `{}`, and `[]` delimiters.
2. Check that each map has complete key-value pairs.
3. Check that the first element map is intended as attributes.
4. Check namespace declarations and qualified names.
5. Check that no domain construct was added to the XIR core definition.
