Purpose: Generate a human-readable CHANGELOG.md that follows the Common Changelog style (a stricter subset of Keep a Changelog), with deterministic formatting.

1) Principles & Preconditions
	•	Audience: Humans (consumers first).
	•	Goals: Communicate impact; skip noise; link for context.
	•	Repo rules: Git with tags; Semantic Versioning.

2) File & Global Ordering
	•	Filename: CHANGELOG.md (Markdown).
	•	First line must be exactly:

# Changelog

	•	Releases are listed latest-first by semantic version precedence (not by publish date).

3) Release Entry Format (with spacing)

Each release entry:

## [VERSION] - YYYY-MM-DD

_Optional single-paragraph notice goes here (emphasized)._

### Category

- Imperative change text (<references>) (<authors>)

Spacing rules:
	•	Blank line after ## [VERSION] - YYYY-MM-DD.
	•	If a notice exists, one blank line after it before the first ### Category.
	•	Blank line before every ### Category.
	•	No extra prose outside notices and change groups.

3.1 Versions & Dates
	•	VERSION: semver without v in the heading, links to the tag/release page via reference-style or inline link.
	•	DATE: ISO 8601 YYYY-MM-DD.

3.2 Notices (optional)
	•	At most one notice per release.
	•	Must be a single paragraph, emphasized with _..._.
	•	Typical use: first release, yanked status, “see UPGRADING.md”.

4) Change Groups

4.1 Categories (exact tokens; recommended order)
	1.	Changed
	2.	Added
	3.	Removed
	4.	Fixed

4.2 Group Structure & Sorting
	•	Each group begins with ### <Category> followed by an unordered list.
	•	Inside a group, sort: Breaking first → importance → newest.

4.3 Change Line Format
	•	One line, imperative mood (“Add”, “Fix”, “Remove”, “Refactor”, “Bump”, “Document”, “Deprecate”).
	•	Pattern:

- <Imperative change> (<references>) (<authors>)

	•	Each list item should be self-contained (readable without the category heading).

5) References (required when available)
	•	Placed on the same line as the change, wrapped in parentheses immediately after the change text.
	•	Prefer the best single starting point (often the PR) over redundant links.
	•	Allowed forms:
	•	Commit: ([53bd922](https://github.com/org/repo/commit/53bd922))
	•	PR/Issue: ([#194](https://github.com/org/repo/pull/194))
	•	External ticket: ([JIRA-837](https://jira.example.com/browse/JIRA-837))
	•	Multiple of the same type: (#1, #2) in a single parentheses block.

6) Authors (optional)
	•	After references, in parentheses: (Alice, Bob)
	•	You may separate references and authors with a semicolon: (#42; Alice, Bob)
	•	Omit if single-contributor project; for bot-authored changes, credit the human merger.

7) Prefixes
	•	Breaking changes: prefix the bullet with **Breaking:**
	•	Subsystem (optional): **Subsystem:** ...
	•	Both: **Subsystem (breaking):** ...
	•	Breaking changes must appear before non-breaking ones within a category.

8) Writing Guidance (LLM-focused)
	•	Exclude noise: toolchain/dotfiles/dev-only deps, trivial formatting.
	•	Include when impactful: refactors (possible side effects), runtime/support matrix changes, new docs for features.
	•	Normalize wording: e.g., “Bump `dep` from 2.x to 3.x”.
	•	Merge related work: represent multi-commit efforts as one change when they form a single user-visible outcome.
	•	Keep lines short: extended rationale belongs in the linked PR/commit or in a separate upgrade guide.

9) Antipatterns (avoid)
	•	Verbatim commit/PR dumps: too noisy, unclear impact.
	•	Conventional Commits syntax in the changelog: machine codes harm readability; use natural, imperative sentences.
	•	Non-ISO dates: always YYYY-MM-DD.

10) Minimal Examples (showing spacing)

10.1 First Release (notice, no groups)

## [1.0.0] - 2025-03-01

_First release._

10.2 Typical Release (notice + groups)

## [1.2.0] - 2025-04-15

_If you are upgrading from 1.1.x, see `UPGRADING.md`._

### Changed

- **Breaking:** emit `close` after `end` ([#312](https://github.com/org/repo/pull/312))

### Added

- Add `--verbose` CLI flag ([#298](https://github.com/org/repo/pull/298); Alice, Bob)

### Fixed

- Prevent segmentation fault on `close()` ([`53bd922`](https://github.com/org/repo/commit/53bd922))

10.3 Subsystem + breaking

## [2.0.0] - 2025-06-01

### Changed

- **API (breaking):** rename `write()` to `send()` ([#421](https://github.com/org/repo/pull/421))


⸻

11) Ruleset for LLM Enforcement

Rule ID	Level	Rule
CC-1	MUST	File is CHANGELOG.md with first line # Changelog.
CC-2	MUST	List releases latest-first by semantic version order.
CC-3	MUST	Release heading: ## [x.y.z] - YYYY-MM-DD with ISO date; version text links to tag/release.
CC-4a	MAY	Provide a notice for a release.
CC-4b	MUST	If a notice is present, it is a single emphasized paragraph placed immediately after the release heading, with a blank line before the first category.
CC-5	MUST	Only these categories: Changed, Added, Removed, Fixed. Recommend that order.
CC-6	MUST	Each change is a single unordered list item in imperative mood.
CC-7	MUST	References follow the change on the same line, wrapped in parentheses; prefer the most useful single reference; combine multiples of same type within one parentheses block.
CC-8	MAY	Authors may be listed in parentheses after references; semicolon may separate refs and authors.
CC-9	MUST	Prefix breaking changes with **Breaking:**; breaking items appear before non-breaking within a category.
CC-10	MAY	Prefix with **Subsystem:**; combine as **Subsystem (breaking):** when applicable.
CC-11	MUST NOT	Include unrelated prose outside notices and change groups.
CC-12	MUST	Use blank lines exactly as in §3 spacing rules.
CC-13	SHOULD	Exclude trivial/noise changes; merge related commits into a single user-visible change line.
CC-14	MUST NOT	Use Conventional Commit tokens in the changelog content.
CC-15	MUST	Use ISO 8601 dates.
