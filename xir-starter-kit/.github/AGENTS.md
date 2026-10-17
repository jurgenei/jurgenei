# Agent Instructions for XIR

Use the terms in this file consistently. Do not invent XIR syntax.

## Read order

1. Read [spec/XIR-QUICK-REFERENCE.md](../spec/XIR-QUICK-REFERENCE.md).
2. Read [spec/XIR-LANGUAGE.md](../spec/XIR-LANGUAGE.md).
3. Inspect the closest relevant file in `examples/`.

## Fixed meanings

- `(. ...)` represents a document node.
- `(name ...)` represents an element node.
- `{...}` represents a map.
- `[...]` represents an array.
- `(! ...)` represents a comment node.
- `(? ...)` represents a processing-instruction node.
- The first map after an element name is the attribute map.
- A later map is element content.
- Map keys and values alternate.
- Whitespace separates map entries.
- Do not insert `=`, `:`, or `,` between map keys and values.
- Namespace declarations use `xmlns` names in the attribute map.

## Required behavior

- Preserve S-expression form. Write `(root ...)`, not `root(...)`.
- Preserve namespaces and qualified names.
- Preserve the distinction between an attribute map and map-valued content.
- Preserve arrays as arrays.
- Keep domain vocabulary separate from XIR core syntax.
- State uncertainty when the repository specification does not define a rule.
- Do not infer missing domain semantics from names alone.

## Minimal valid patterns

```xir
(root)
```

```xir
(root {id "1"})
```

```xir
(root {} {key "value"})
```

```xir
(root [1 2 3])
```

```xir
(.
  {xmlns:risk "urn:risk" xmlns:gov "urn:governance"}
  (risk:Exposure
    (gov:Owner "team-a")
  )
)
```

## Validation checklist

Before changing an XIR file:

1. Check balanced `()`, `{}`, and `[]` delimiters.
2. Check that each map has complete key-value pairs.
3. Check that the first element map is intended as attributes.
4. Check namespace declarations and qualified names.
5. Check that no domain construct was added to the XIR core definition.
