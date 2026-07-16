# web-app-serve

See https://github.com/toggle-corp/web-app-serve

## Helm publish

Set `helm_publish: true` to also build, lint, smoke-render, package and push the
wrapper Helm chart to GHCR (`oci://ghcr.io/<owner>`), stamping provenance
annotations into `values.yaml` at `helm_annotations_path` (default
`.app.ingress.annotations`). Docker-only callers are unaffected — everything is
gated on `helm_publish == 'true'`.

### Required caller permissions

The provenance annotation writer resolves the PR for a commit via `gh api`, so
the calling workflow must grant `pull-requests: read` (in addition to the
`packages: write` needed to push). A composite action inherits the caller's
token; it cannot widen it.

```yaml
permissions:
  packages: write
  pull-requests: read
```

The caller must also `actions/checkout` the repo before invoking this action —
the annotation writer reads the commit subject via `git log`.

### Provenance annotation schema

The annotation set is described by a versioned JSON Schema under
[`schema/`](./schema). Keys are prefixed with `web-app-serve.togglecorp.com/`
and a `schema-version` annotation pins which schema file applies.

Bumping the schema is a 3-step rule: (1) edit the keys in `action.yml`, (2) bump
the `schema-version` literal in `action.yml`, (3) add `schema/annotations-v<n>.json`
with its `const` matching the new version (keep the old files). See
[`schema/README.md`](./schema/README.md).
