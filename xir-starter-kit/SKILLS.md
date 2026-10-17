# XIR Skills

Use these skills with canonical syntax rules from:
- [spec/XIR-QUICK-REFERENCE.md](spec/XIR-QUICK-REFERENCE.md)
- [spec/XIR-LANGUAGE.md](spec/XIR-LANGUAGE.md)

Narrative spine position:

```text
Structured Knowledge -> XIR -> Observation -> Derivation -> Knowledge -> Applications
```

## Skill: Read XIR

Purpose: interpret XIR structure without converting it mentally to JSON or function-call syntax.

Procedure:

1. Identify the outer delimiter.
2. For `(`, inspect the first token.
3. Interpret `.` as a document node, `!` as a comment, `?` as a processing instruction, and any other name as an element.
4. In an element, interpret the first map as attributes.
5. Interpret later maps, arrays, elements, and atomic values as content.
6. Resolve qualified names from `xmlns` declarations in scope.

## Skill: Write XIR

1. Select the correct structural form.
2. Put the element name immediately after `(`.
3. Put attributes in the first map.
4. Insert an empty first map when map-valued content must not become attributes.
5. Separate every item with whitespace.
6. Do not add comma, colon, or equals separators.
7. Preserve explicit namespace qualification when vocabularies are mixed.

## Skill: Review XIR

Check structural balance, map pairing, attribute-map position, namespace scope, and vocabulary boundaries. Report errors directly. Do not silently reinterpret malformed input.
