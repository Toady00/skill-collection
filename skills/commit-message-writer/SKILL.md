---
name: commit-message-writer
description: Write Git commit messages following the classic Chris Beams 7-rule standard from cbea.ms/git-commit. Use when creating commits, writing commit messages, or when git commit commands are being executed.
version: 0.1.0
---

# Commit Message Writer

## Overview

Write Git commit messages that follow the classic, well-established standard
from https://cbea.ms/git-commit/. This skill ensures consistent, professional
commit messages that focus on communicating changes clearly without unnecessary
metadata or attribution.

## Critical Prohibitions

**NEVER include any of the following in commit messages:**
- ❌ References to Claude, Claude Code, or AI assistance
- ❌ "Co-authored-by: Claude" or similar attribution
- ❌ "Generated with AI" or similar disclaimers
- ❌ Any mention of AI tools or assistance
- ❌ Meta-commentary about how the message was written

**Why:** Commit messages are about the code changes, not the tools used to write them.

## The Seven Rules

These are the rules from https://cbea.ms/git-commit/. For detailed explanations and examples, read `references/git-commit-guide.md`.

1. **Separate subject from body with a blank line**
2. **Limit the subject line to 50 characters** (72 hard limit)
3. **Capitalize the subject line**
4. **Do not end the subject line with a period**
5. **Use the imperative mood in the subject line**
6. **Wrap the body at 72 characters**
7. **Use the body to explain what and why vs. how**

**Quick test for imperative mood:** Does it complete this sentence?
> "If applied, this commit will [your subject line]"

## Commit Message Workflow

### 1. Review the Changes

```bash
# See what's staged
git diff --staged

# Or if not yet staged
git diff

# Check current status
git status
```

**Determine:**
- What changed?
- Why was it changed?
- What problem does it solve?

### 2. Write the Subject Line

**Format:** Imperative mood, capitalized, no period, 50 chars (72 max)

**Good examples:**
- "Add OAuth2 authentication support"
- "Fix memory leak in connection pool"
- "Remove deprecated user API endpoints"
- "Update documentation for rate limiting"

**Check character count:**
```bash
echo -n "Your subject line here" | wc -c
```

### 3. Decide if Body is Needed

**Skip the body for:**
- Simple, self-explanatory changes
- Example: "Fix typo in README"

**Write a body for:**
- Changes needing context or explanation
- Multiple related changes
- Side effects or consequences
- Decision-making rationale

### 4. Write the Body (if needed)

**Structure:**
```
Subject line (50 chars max)
<blank line>
Body paragraph explaining what and why. Wrap lines at 72
characters manually. Git does not wrap automatically so you must
insert line breaks yourself.

Additional paragraphs separated by blank lines.

- Bullet points are okay
- Use hyphens with single space
- Blank lines between bullets

Issue references at bottom:
Resolves: #123
See also: #456
```

**Focus on what/why, not how.** The code shows how.

### 5. Pre-Commit Checklist

Before committing, verify:

**Subject line:**
- [ ] 50 characters or less (72 max)
- [ ] Capitalized
- [ ] No period at end
- [ ] Imperative mood
- [ ] Completes "If applied, this commit will..."

**Body (if present):**
- [ ] Blank line after subject
- [ ] Lines wrapped at 72 characters
- [ ] Explains what and why, not how

**Prohibited content:**
- [ ] No mention of Claude or AI
- [ ] No co-authorship attribution

### 6. Create the Commit

**Simple (subject only):**
```bash
git commit -m "Add OAuth2 authentication support"
```

**With body (use temp file):**
```bash
cat > /tmp/commit-msg << 'EOF'
Add OAuth2 authentication support

Users requested the ability to authenticate using existing Google
and GitHub accounts instead of creating new credentials. This
reduces friction in onboarding and improves user adoption.

The implementation uses the standard OAuth2 flow with PKCE for
security. Session tokens expire after 24 hours.

Resolves: #234
EOF

git commit -F /tmp/commit-msg
```

## Quick Examples

**Simple commit:**
```
Fix typo in user documentation
```

**Commit with body:**
```
Refactor authentication middleware for clarity

The previous implementation mixed concerns between token
validation, session management, and error handling. This made
it difficult to test and reason about.

This refactor separates token validation into its own module,
centralizes session management logic, and improves error
messages for debugging.

No functional changes, but the code is now more maintainable.

See also: #445
```

## Common Mistakes to Avoid

❌ **Wrong tense:** "Added OAuth2 support" → ✅ "Add OAuth2 support"  
❌ **Too long:** "Added a new OAuth2 authentication system..." (77 chars)  
❌ **Has period:** "Add OAuth2 support." → ✅ "Add OAuth2 support"  
❌ **Not capitalized:** "add oauth2 support" → ✅ "Add OAuth2 support"  
❌ **AI attribution:** "Co-authored-by: Claude" → ✅ (never include)  
❌ **Long body lines:** Don't let body lines exceed 72 characters  

## Integration with Other Skills

- **workflow-manager**: Write good commit messages throughout development
- **gitlab-mr-creator**: Commits in MR should all have proper messages
- **issue-creator**: Reference issues with `Resolves: #123` format

## When This Skill Triggers

Activate automatically when:
- Executing `git commit` commands
- Asked to write a commit message
- About to commit changes
- Any commit-related activity

**Apply these rules automatically** without asking permission or mentioning that you're following them.

## Reference Documentation

For complete explanations, examples, and rationale for each rule, read:

```bash
cat ~/.config/opencode/skills/commit-message-writer/references/git-commit-guide.md
```

This contains the full text from https://cbea.ms/git-commit/ with detailed
explanations of why each rule matters and extensive examples of good and bad
commit messages.
