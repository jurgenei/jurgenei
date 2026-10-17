# XIR Quick Reference

XIR uses delimiters to identify value or node kinds.

```text
(. ...)       document node
(name ...)    element node
{...}         map; in first position in an element, the attribute map
[...]         array
(! ...)       comment node
(? ...)       processing-instruction node
```

## Maps

A map contains alternating key and value items. Whitespace separates items. Do not use `=`, `:`, or `,` as key-value separators.

```xir
{id "E1" amount 1000 active true}
```

## Elements and attributes

The first map after an element name is its attribute map.

```xir
(customer {id "123"})
```

A later map is element content. Use two maps when an element has attributes and map-valued content.

```xir
(customer
  {id "123"}
  {city "Alphen aan den Rijn" country "NL"}
)
```

An element can contain an array directly.

```xir
(roles ["architect" "developer"])
```

## Namespaces

Namespace declarations are entries in the attribute map and use XML-style `xmlns` names.

```xir
(.
  {xmlns "urn:example" xmlns:risk "urn:risk" xmlns:gov "urn:governance"}
  (risk:Exposure
    (gov:Owner "team-a")
  )
)
```

## Comments and processing instructions

```xir
(! "This is a comment")
(? xml-stylesheet {href "style.xsl" type "text/xsl"})
```
