---
name: beads-common-changelog-release
description: Manage beads-backed Common Changelog entries. Use when closing beads that may need release-note metadata, preparing changelog text from release-pending beads, or updating bead changelog metadata.
version: 0.1.0
---

# Beads Common Changelog Release Workflow

## Purpose

This skill defines the agent side of a beads-backed release-note workflow.

Use it to decide which bead receives changelog metadata, whether completed work
needs a user-facing changelog entry, and how to generate Common Changelog text
from beads that are already marked `release:pending`.

Do not infer changelog entries from commit messages. Commit messages are for
developers; changelogs are for users of the software.

## Reference Files

When managing beads without generating a changelog, you must read/load the
focused implementation guide at `references/bead-changelog-metadata-guide.md`
before deciding whether to set or modify `changelog:*` state or changelog
metadata.

When generating a changelog, you must read the full Common Changelog
specification at `references/common-changelog-spec.md` before writing or
modifying changelog Markdown.

Do not use the focused implementation guide as a substitute for the full spec
when generating a changelog.

## Closing Beads Decision Tree

When closing a bead, first identify the bead that owns the changelog entry.

1. Inspect the bead being closed.
2. If the bead has a parent epic, use the parent epic bead as the changelog
   target. A child bead usually has a hierarchical ID such as `<epic-id>.1`, and
   children can be inspected with `bd show <epic-id> --children`,
   `bd children <epic-id>`, or `bd list --parent <epic-id> --status all`.
3. If the bead is not a child bead and is an implementation task or other work
   bead, use the bead itself as the changelog target.
4. If the bead is an ADR, spike, research note, planning-only bead, or other
   non-implementation work, do not add changelog metadata unless the user
   explicitly asks for it.

For child beads, close the child bead but make changelog classification and
metadata changes on the parent epic bead. The epic is the user-facing unit of
release communication.

For non-child implementation beads, close the bead and make changelog
classification and metadata changes on that same bead.

In this opinionated workflow, epics are the normal release unit. A single agent
is expected to implement the whole epic, close the child tasks, and produce one
PR for the epic. Therefore the epic should own the changelog state,
`metadata.changelog`, `metadata.git.author`, PR URL metadata, and
`release:pending` / `release:released` state.

## Changelog Need Decision Tree

After identifying the changelog target bead, decide whether the completed work
needs to be communicated to end users.

1. If the changelog target already has a `changelog:*` state or
   `metadata.changelog`, inspect both.
2. Decide whether the newly completed work changes the user-facing release note
   enough to require updating the target bead's `changelog:*` state or
   `metadata.changelog`.
3. If neither changelog state nor changelog metadata exists, decide whether users
   need to know about this work to understand the effect of updating from the
   previous version to the next version.
4. If users need to know, set exactly one `changelog:*` state and write or update
   `metadata.changelog`.
5. If users do not need to know, do not add a `changelog:*` state or
   `metadata.changelog`.

Include changes such as:

- New user-facing capabilities.
- User-visible behavior changes.
- Removed user-facing functionality.
- User-visible bug fixes.
- Configuration, API, CLI, deployment, output, or compatibility changes that
  affect consumers.

Do not include changes such as:

- Internal refactors with no user-facing behavior change.
- Test-only changes.
- Formatting-only changes.
- Build or dependency churn with no user-facing effect.
- Implementation details users do not need to know.

## Changelog Categories

Common Changelog supports exactly these change groups, in this order:

- `Changed`
- `Added`
- `Removed`
- `Fixed`

Use lowercase state values in beads:

- `changelog:changed`
- `changelog:added`
- `changelog:removed`
- `changelog:fixed`

Use `bd set-state <id> changelog=added` when setting state. Beads records that
as the label-style state `changelog:added` when reading, listing, or querying.
`bd set-state` removes any existing label for the same dimension before adding
the new `<dimension>:<value>` label, so a bead can only have one active value for
the `changelog` dimension.

Use `changelog:added` when users can do something new.

Use `changelog:fixed` when broken user-facing behavior now works correctly.

Use `changelog:changed` when existing user-facing behavior, configuration,
output, compatibility, defaults, or workflow changed.

Use `changelog:removed` when user-facing functionality, options, APIs,
configuration, or supported behavior was removed.

Do not use `Deprecated` or `Security` as Common Changelog categories. Represent
deprecations as `changelog:changed` and security fixes as `changelog:fixed`.

Represent breaking changes with metadata and render them as a `**Breaking:**`
prefix inside their normal category.

## SemVer Decision Tree

Determine SemVer impact from user impact and `metadata.changelog.breaking`, not
from the category alone.

1. If any pending changelog entry is breaking, the release is major.
2. Otherwise, if any pending entry adds backwards-compatible user-facing
   capability, the release is minor.
3. Otherwise, if all pending entries are non-breaking fixes, the release is
   patch.
4. If compatibility is unclear, ask the user one concise question before
   choosing the release bump.

`changelog:added` typically implies minor.

`changelog:fixed` typically implies patch.

`changelog:changed` may be major if it is not backwards compatible, or minor if
it is a backwards-compatible user-facing improvement.

`changelog:removed` is usually major when the removed item was user-facing.

Internal refactors should not be classified as `changelog:changed`; omit them
from the changelog.

## Metadata

Use labels for indexing and metadata for changelog content and references.

Preferred metadata shape:

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

When work needs a changelog entry, add the git user to the changelog target bead
metadata so the generated changelog can reference the author. Git author metadata
is not necessary for beads that will not appear in the changelog.

Get the git user from the repository config:

```bash
git config user.name
git config user.email
```

Before updating metadata, inspect the existing metadata and preserve unrelated
fields. `bd update --metadata` merges top-level metadata keys with existing
metadata, but nested objects are replaced as whole top-level values. If a bead
already has `metadata.changelog`, `metadata.git`, or `metadata.pr`, include the
full merged nested object for that key in the update.

Use `--metadata` for structured JSON:

```bash
bd update <id> --metadata '{"changelog":{"entry":"Add Garage storage backend","breaking":false},"git":{"author":{"name":"Example Author","email":"author@example.com"}}}'
```

Use `@file.json` when quoting is inconvenient:

```bash
bd update <id> --metadata @changelog-metadata.json
```

Avoid using `--set-metadata` for nested JSON objects. It preserves primitive
values like booleans and numbers, but object-shaped values are stored as strings.

Keep `metadata.changelog.entry` human-facing and impact-oriented. Describe what
changed for the user, not how the implementation changed.

## Bead Update Flow

When closing implementation work:

1. Read `references/bead-changelog-metadata-guide.md`.
2. Identify the changelog target bead using the closing decision tree.
3. Inspect existing `changelog:*` state and metadata on the target bead.
4. Decide whether the completed work needs a changelog entry.
5. If it does, set or update exactly one changelog state on the target bead.
6. If it does, add or update `metadata.changelog` on the target bead.
7. If the work needs a changelog entry, add or update `metadata.git.author` on
   the target bead from git config.
8. Close the work bead.
9. Commit and push bead database changes.

Example:

```bash
bd show <target-id> --long
bd set-state <target-id> changelog=added
bd update <target-id> --metadata '{"changelog":{"entry":"Add Garage storage backend","breaking":false},"git":{"author":{"name":"Example Author","email":"author@example.com"}}}'
bd close <work-id>
bd dolt commit && bd dolt push
```

If the work does not need a changelog entry, close the work bead and still commit
and push the bead database changes:

```bash
bd close <work-id>
bd dolt commit && bd dolt push
```

After any bead change, run:

```bash
bd dolt commit && bd dolt push
```

If commit or push fails because local bead state has diverged from the remote
Dolt database:

1. Run `bd doctor`.
2. Run `bd dolt pull` to merge remote Dolt changes into the local database.
3. If Dolt reports conflicts, inspect them with
   `bd sql "SELECT * FROM dolt_conflicts"`.
4. Resolve conflicts without discarding either side's intended issue changes.
5. Re-run the bead command that failed if needed.
6. Run `bd dolt commit && bd dolt push` again.

Do not use `bd dolt push --force` to resolve divergence unless the user
explicitly asks for it.

## Changelog Generation Flow

When generating a changelog:

1. Pull the latest git changes for the repository.
2. Pull the latest beads database changes.
3. Read `references/common-changelog-spec.md`.
4. Find all beads marked `release:pending` with a changelog state.
5. Inspect each bead's changelog metadata, PR URL metadata, and git author
   metadata.
6. Determine the SemVer bump from all pending entries.
7. Render the changelog according to the Common Changelog spec.
8. Review the generated changelog for spec compliance and user-facing wording.
9. Ask the user whether to commit, tag, and push unless that intent is already
   explicit.

Use these commands before reading pending beads:

```bash
git pull
bd dolt pull
```

Find release-pending changelog candidates:

```bash
bd list --label release:pending \
  --label-any changelog:changed,changelog:added,changelog:removed,changelog:fixed \
  --json
```

The `release:pending` state must be on the changelog target bead. For epic work,
that means the parent epic has `release:pending`; the child tasks do not own the
release changelog entry.

Group entries in this exact order:

- `Changed`
- `Added`
- `Removed`
- `Fixed`

Sort breaking changes first within each category.

Use `metadata.changelog.entry` as the basis for the rendered entry.

If `metadata.pr.url` exists, use it as the primary changelog reference.

In this opinionated workflow, the PR is expected to be at the epic level. For
epic work, read `metadata.pr.url` from the parent epic bead.

If `metadata.git.author` exists, use it with the PR URL or fallback reference to
construct the full changelog line according to the Common Changelog spec.

If no PR URL exists, use the best available durable reference from metadata,
commit data, or a linkable bead reference. A plain bead ID is useful locally but
is not strictly compliant unless it links to a browsable bead, issue export,
pull request, commit, or other durable location.

## Release Git Flow

After generating the changelog, do not assume you should commit, tag, or push.

If the user already asked you to perform the release git operations, commit the
changelog, create the SemVer tag, and push according to the repository's git
policy.

If the user did not explicitly ask for release git operations, ask whether they
want you to commit, tag, and push or stop for human review.

If the user tells you to commit, tag, and push the release, then after the repo
tag has been pushed, mark every included `release:pending` bead as
`release:released` and push the bead database changes:

```bash
bd set-state <id> release=released
bd dolt commit && bd dolt push
```

Because `bd set-state` permits only one active value per dimension, setting
`release=released` removes the previous `release:pending` state from that bead.

If the user wants a human review step, stop after generating the changelog and do
not commit, tag, push, or change `release:pending` beads to `release:released`.

## Invariants

Never add changelog metadata for internal-only changes.

Never use commit messages as the source of truth for user-facing release notes.

Never use Keep a Changelog-only categories like `Deprecated` or `Security` as
Common Changelog headings.

Always keep changelog entries human-facing and impact-oriented.

Always render Common Changelog groups in this order: `Changed`, `Added`,
`Removed`, `Fixed`.

Always mark breaking changes explicitly in metadata.

Never assume `changelog:changed` or `changelog:removed` is non-breaking without
checking user impact.

Always commit and push bead database changes after changing beads.

Never discard remote bead database changes to resolve a Dolt push conflict.
