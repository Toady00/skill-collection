# Bead Changelog Metadata Guide

The bead should capture enough information for a later release job or changelog
generation step to produce a user-facing Common Changelog entry.

Do not write release prose for developers. Write for people deciding whether and
how to update their existing use of the software.

## Include Or Skip

Add changelog metadata only when the work changes something an external user,
operator, integrator, or consumer needs to understand when upgrading.

Include:

- New user-facing features or capabilities.
- Changed behavior, defaults, configuration, APIs, CLI, output, deployment, or
  compatibility.
- Removed user-facing functionality, options, APIs, configuration, or supported
  behavior.
- Bug fixes with user-visible impact.
- Security fixes that affect consumers.
- Deprecations that users need to plan around.

Skip:

- Internal refactors with no user-facing behavior change.
- Tests, formatting, linting, or documentation cleanup with no user impact.
- Build or dependency churn with no user-facing effect.
- Implementation details that do not change how users consume the software.

If the impact is unclear, ask one concise question before setting changelog
metadata.

## Choose One Dimension

Set exactly one `changelog` dimension value for a bead that should appear in a
release changelog:

- `changelog:changed` for changed user-facing behavior, defaults,
  compatibility, configuration, output, or workflow.
- `changelog:added` for a new user-facing capability.
- `changelog:removed` for removed user-facing functionality, options, APIs,
  configuration, or supported behavior.
- `changelog:fixed` for a user-visible bug fix.

Do not use `Deprecated` or `Security` as categories. Represent deprecations as
`changelog:changed` and security fixes as `changelog:fixed`.

Use command syntax like `bd set-state <id> changelog=added`. Beads records that
as `changelog:added` when reading or querying the bead. Setting a state removes
any previous value for the same dimension.

## Breaking Changes

Set `metadata.changelog.breaking` to `true` when existing users may need to
change code, configuration, data, automation, deployment, or expectations to keep
working after upgrading.

Typical breaking changes include:

- Removing user-facing functionality.
- Renaming or changing an API, CLI flag, config field, output field, or required
  workflow.
- Changing defaults in a way that can alter existing behavior.
- Dropping support for a runtime, platform, protocol, storage format, or
  integration users may rely on.

If the change is not breaking, set `metadata.changelog.breaking` to `false`.

## Entry Text

Set `metadata.changelog.entry` to one concise, imperative sentence fragment that
is readable without the category heading.

Good examples:

- `Add Garage storage backend`
- `Fix token refresh after session expiry`
- `Remove legacy S3 path-style configuration`
- `Change storage configuration format`

Avoid:

- Commit-message prefixes such as `feat:`, `fix:`, or `BREAKING CHANGE:`.
- Developer-only phrasing such as `Refactor storage adapter internals` when no
  user-facing behavior changed.
- Vague entries such as `Update dependencies` unless the dependency change has a
  direct user-facing effect.

## Optional Subsystem

Set `metadata.changelog.subsystem` when a short subsystem name helps users scan
the release notes, such as `Storage`, `API`, `CLI`, or `Kubernetes`.

Do not invent a subsystem if it does not add clarity.

## Author Metadata

When work needs a changelog entry, add the git user to the bead metadata so the
changelog can reference the author. Git author metadata is only necessary when
the bead needs a changelog entry.

Use this shape:

```json
{
  "git": {
    "author": {
      "name": "Example Author",
      "email": "author@example.com"
    }
  }
}
```

Read the values from:

```bash
git config user.name
git config user.email
```

## PR URL Metadata

Agents usually should not set PR URL metadata unless the URL is already known.
CI or release-stream automation should add it when work lands through a PR.

If present, use this shape:

```json
{
  "pr": {
    "url": "https://github.com/org/repo/pull/123"
  }
}
```

## Preferred Metadata Shape

Merge these fields into existing bead metadata without deleting unrelated
metadata. `bd update --metadata` preserves unrelated top-level metadata keys,
but nested objects are replaced as whole top-level values, so include the full
merged `changelog`, `git`, or `pr` object when updating any of those keys:

```json
{
  "changelog": {
    "entry": "Add Garage storage backend",
    "breaking": false,
    "subsystem": "Storage"
  },
  "git": {
    "author": {
      "name": "Example Author",
      "email": "author@example.com"
    }
  },
  "pr": {
    "url": "https://github.com/org/repo/pull/123"
  }
}
```

## SemVer Signal

The release job determines the final SemVer version from all pending entries,
but each bead should provide clear compatibility metadata.

- `breaking: true` signals a major release may be required.
- `changelog:added` commonly contributes to a minor release.
- `changelog:fixed` commonly contributes to a patch release.
- `changelog:changed` and `changelog:removed` require compatibility judgment;
  mark them breaking when the change breaks backwards compatibility from the
  users' point of view.
