# Beads Common Changelog Release Skill

Opinionated skill for creating releases and managing a changelog file, informed
through beads. This assumes you are using:

- [SemVer](https://semver.org/)
- [Common Changelog](https://common-changelog.org/)
- [beads](https://github.com/gastownhall/beads)
  - and that the beads database is stored in the same git remote as the underlying code base

If you are using conventional commits, this isn't for you. Commit messages are
for developers, not for consumers of your software. "Changelogs are _for
humans_, not machines."

## How It Works

This is opinionated toward epic-level work. The expected flow is that another
tool creates an epic with child task beads, then one agent implements the whole
epic in one pass. The epic bead is the release-note unit: child beads are useful
for implementation tracking, but the epic owns changelog state and metadata.

Dimensions on beads track the state of the underlying code. A `changelog`
dimension allows the implementing agent to mark code with the category of the
changelog entry per Common Changelog Spec (`Changed|Added|Removed|Fixed`). The
implementing agent also stores the human-facing changelog entry details in
`metadata.changelog` and records the git user in metadata so the changelog can
reference the author. For epic work, this metadata belongs on the epic bead.

The CI/CD pipeline job that introduces the bead into the release stream (whatever
that looks like for you), adds a dimension `release:pending`. If the work landed
through a PR, that job should also add the PR URL to the bead metadata so the
generated changelog can link to the reviewed change. In the opinionated epic
workflow, the PR is at the epic level, and both `release:pending` and PR URL
metadata belong on the epic bead.

The CI/CD pipeline job that cuts a new release looks for all beads that are
`release:pending`, then grabs all the metadata and the `changelog` dimension
value to build a new changelog release. The release job determines the correct
SemVer version based on the changelog dimension values and the changelog
metadata.

Once the entry is built, it should be committed by the job and it should be
tagged with the new SemVer version determined by the release job. The tagging
process ideally would be the trigger for a production release.

After the repo is tagged, the release job should mark all included
`release:pending` beads as `release:released` so future releases do not include
already released changelog entries.
