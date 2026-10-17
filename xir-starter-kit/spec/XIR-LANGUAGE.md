# XIR Language Definition

Status: concise working definition derived from the established XIR notation. Keep the implementation specification authoritative where it defines additional lexical or processing rules.

## 1. Purpose

XIR is a compact concrete syntax for structured representation. It exposes document nodes, elements, maps, arrays, comments, processing instructions, and atomic values with minimal punctuation.

XIR does not define domain vocabularies. A schema or vocabulary defines domain meaning on top of XIR.

## 2. Structural forms

### 2.1 Document node

A document node starts with `(.` and ends with `)`.

```xir
(.
  (root)
)
```

### 2.2 Element node

An element starts with `(`, followed by its name, optional attributes, and zero or more content items.

```xir
(root
  {id "r1"}
  (child "text")
)
```

The form is S-expression notation: `(name content...)`, not function-call notation `name(content...)`.

### 2.3 Map

A map uses `{}`. It contains alternating key and value items separated by whitespace.

```xir
{key1 "value1" key2 2}
```

XIR does not require commas, colons, or equals signs between map entries.

### 2.4 Attribute-map position

The first map directly after an element name is the element attribute map.

```xir
(el {id "1"})
```

A subsequent map is content.

```xir
(el {} {key "value"})
```

An empty first map explicitly reserves the attribute position when map-valued content follows.

### 2.5 Array

An array uses `[]` and contains zero or more items.

```xir
[1 2 3]
```

An element can contain an array without an attribute map.

```xir
(el [1 2 3])
```

### 2.6 Comment

A comment uses the `(! ...)` form.

```xir
(! "Comment text")
```

### 2.7 Processing instruction

A processing instruction uses the `(? ...)` form.

```xir
(? xml-stylesheet {href "style.xsl"})
```

## 3. Namespaces

Namespace declarations use XML-compatible `xmlns` names in an element's attribute map.

```xir
(.
  {xmlns "urn:default" xmlns:x "urn:x"}
  (x:item)
)
```

Namespace prefixes remain explicit when vocabularies are mixed. A default namespace can remove repetition for the dominant vocabulary.

## 4. Mixed content kinds

Elements can contain elements, maps, arrays, atomic values, comments, and processing instructions, subject to the XIR processing model.

```xir
(container
  {id "c1"}
  "text"
  (child)
  {key "value"}
  [1 2 3]
  (! "note")
)
```

## 5. Design principles

- The delimiter identifies the construct kind.
- Whitespace separates items.
- Syntax does not duplicate information without need.
- XIR separates representation from domain vocabularies.
- XML/XDM compatibility is a foundation, not the public value proposition.
- Domain models must not expand the XIR core.

## 6. Non-goals

XIR is not itself an observation model, lineage model, governance model, architecture model, or knowledge-management model. These can use XIR as their representation syntax.

## 7. Items that require the canonical implementation specification

This concise definition intentionally does not invent rules for escaping, numeric lexical forms, QName validation, duplicate map keys, namespace scope errors, document-node constraints, or XML round-trip edge cases. Use the canonical implementation specification for those rules.
