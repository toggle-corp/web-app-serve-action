# Provenance annotation schemas

`web-app-serve-action` writes a set of provenance annotations into the wrapper
chart's `values.yaml` (at `.app.ingress.annotations` by default). Those
annotations end up on the live Kubernetes `Ingress` object. This directory holds
a versioned JSON Schema describing that annotation set.

## Domain prefix

Every annotation key is prefixed with the domain

```
web-app-serve.togglecorp.com/
```

The `properties` keys in each schema file include the **full** prefixed name
(e.g. `web-app-serve.togglecorp.com/commit`), because that is exactly what
appears on a live object — matching the schema against a rendered Ingress
requires no key rewriting.

## `$id`

Each schema declares a stable `$id`, e.g.

```
https://raw.githubusercontent.com/toggle-corp/web-app-serve-action/main/schema/annotations-v1.json
```

The `$id` is the canonical identity of a schema version. It never changes for a
given version — a new version gets a new file and a new `$id`. Tooling can `$ref`
a specific version by its `$id`.

## `schema-version` and the sync rule

The annotation `web-app-serve.togglecorp.com/schema-version` carries a literal
(currently `"1"`) that the action writes and that pins which schema file
describes a given object. It is **not** auto-derived — it is kept in sync by
hand.

To bump the schema (3-step rule):

1. Edit the annotation keys emitted in `action.yml`.
2. Bump the `schema-version` literal in `action.yml` (and the matching `const`
   in the new schema).
3. Add `annotations-v<n>.json` with the new `$id`. **Keep the old files** so
   objects stamped with older `schema-version` values remain describable.

## Files

- `annotations-v1.json` — schema-version `1`. Draft 2020-12. `pr-*` keys are
  optional (a build may have no associated PR); all other keys are required.
