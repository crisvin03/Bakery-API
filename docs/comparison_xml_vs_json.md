# XML vs JSON in the Product API

## Overview

- Both formats are supported via `Content-Type`, `Accept`, or `?format=` query.
- Payloads are symmetrical: `name`, `price`, `stock`, `ingredients`, `is_active`, `is_archived`, `expiration_date`.

## Example Payloads (create product)

JSON:

```json
{
  "name": "Sample Bread",
  "price": 5.25,
  "stock": 10,
  "ingredients": "flour, sugar",
  "is_active": true
}
```

XML:

```xml
<product>
  <name>Sample Bread</name>
  <price>5.25</price>
  <stock>10</stock>
  <ingredients>flour, sugar</ingredients>
  <is_active>true</is_active>
</product>
```

## Structure & Readability

- JSON: concise, maps directly to objects/arrays; ideal for web/mobile engineers.
- XML: more verbose but explicit; supports attributes and mixed content; common in enterprise/SOAP ecosystems.

## Size & Performance

- JSON is smaller (no closing tags) and typically faster to parse in browsers/mobile.
- XML adds tag overhead and can be slightly slower; acceptable for modest payloads like this API.

## Validation & Schema

- XML: mature XSD tooling, strict schema enforcement where needed.
- JSON: JSON Schema is lighter and widely used; good editor/IDE support.

## Tooling & Ecosystem

- JSON: first-class in JS/TS, most HTTP clients, mobile SDKs, and modern APIs.
- XML: strong in legacy/enterprise stacks; good fit when partners require XML.

## Streaming & Partial Processing

- XML: well-supported streaming (SAX/DOM) for large docs.
- JSON: streaming (NDJSON/JSON lines) exists but less standardized for tree validation.

## Error Handling

- Both return structured errors; JSON is terser, XML is wordier but explicit.
- Status codes align (400 validation, 404 not found, 405 method, 409 conflict).

## Security Considerations

- Disable XXE in stacks that allow it; this implementation uses Python’s `xml.etree.ElementTree` safe parsing.
- Treat all input as untrusted; validation paths are the same for JSON and XML.

## Caching & CDN

- Similar behavior; vary cache by `Accept` header if you serve both formats from a CDN.

## When to Prefer

- Choose JSON for most web/mobile integrations, lighter payloads, and JS-friendly parsing.
- Choose XML for legacy/enterprise systems, strict schema/XSD needs, or partner-mandated XML.

## Testing Summary

- JSON list: `curl -H "Accept: application/json" http://<host>/api/products/`
- XML list: `curl -H "Accept: application/xml" http://<host>/api/products/`
- Create/update: switch `Content-Type` between `application/json` and `application/xml`; payload shapes match the examples above.

## Header Cheat Sheet

- Response format: `Accept: application/json` or `Accept: application/xml` (or `?format=json|xml`).
- Request body format: `Content-Type: application/json` or `Content-Type: application/xml`.
- Same shape for both: `name`, `price`, `stock`, `ingredients`, `is_active`, `is_archived`, `expiration_date`.

## Practical Trade-offs (Concise)

- JSON: smaller, faster to parse in browsers/mobile, native to JS/TS; best default for web/mobile APIs.
- XML: verbose but explicit; strong in legacy/enterprise and schema-driven integrations; good when partners require XML or document-style payloads.
- Validation: XML + XSD is mature; JSON Schema is common and lighter.
- Tooling: JSON dominates modern client stacks; XML tools are abundant in enterprise/older ecosystems.

## Common Pitfalls

- Missing headers: set both `Accept` (response) and `Content-Type` (request) to match the format you want.
- Dates: `expiration_date` must be ISO `YYYY-MM-DD`.
- Numbers: `price` must be decimal; `stock` must be integer ≥ 0.
- Booleans: JSON uses true/false; XML uses `true`/`false` text.
- Errors: 400 for validation, 404 for missing ID, 405 for wrong method, 409 if delete is blocked by references.

## Security Notes

- XML parsing uses Python’s `xml.etree.ElementTree` (safe by default here); avoid enabling external entities (XXE) in other stacks.
- Treat all inputs as untrusted; validation paths are identical for JSON and XML.

## Quick Validation Examples

- Missing required field (JSON): POST without `name` → 400 with error details.
- Invalid price (XML): `<price>abc</price>` → 400 with validation error.

## When to Prefer JSON vs XML (Reminder)

- Use JSON by default for web/mobile and most third-party integrations.
- Use XML when a partner mandates it, when you need strict XSD-driven contracts, or for document-centric exchanges.
