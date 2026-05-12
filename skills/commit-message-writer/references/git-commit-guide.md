# How to Write a Git Commit Message

Source: https://cbea.ms/git-commit/

This reference contains the complete explanations and rationale for the seven rules of good commit messages. The SKILL.md file contains the workflow and checklist; this file contains the "why" and detailed examples.

---

## Introduction: Why Good Commit Messages Matter

A well-crafted Git commit message is the best way to communicate context about a change to fellow developers (and indeed to your future self). A diff will tell you what changed, but only the commit message can properly tell you why.

Re-establishing the context of a piece of code is wasteful. We can't avoid it completely, so our efforts should go to reducing it as much as possible. Commit messages can do exactly that and as a result, a commit message shows whether a developer is a good collaborator.

A well-cared for log is a beautiful and useful thing. `git blame`, `revert`, `rebase`, `log`, `shortlog` and other subcommands come to life. Reviewing others' commits and pull requests becomes something worth doing, and suddenly can be done independently. Understanding why something happened months or years ago becomes not only possible but efficient.

---

## The Seven Rules of a Great Git Commit Message

1. Separate subject from body with a blank line
2. Limit the subject line to 50 characters
3. Capitalize the subject line
4. Do not end the subject line with a period
5. Use the imperative mood in the subject line
6. Wrap the body at 72 characters
7. Use the body to explain what and why vs. how

---

## Rule 1: Separate subject from body with a blank line

From the `git commit` manpage:

> Though not required, it's a good idea to begin the commit message with a single short (less than 50 character) line summarizing the change, followed by a blank line and then a more thorough description. The text up to the first blank line in a commit message is treated as the commit title, and that title is used throughout Git.

Not every commit requires both a subject and a body. Sometimes a single line is fine, especially when the change is so simple that no further context is necessary. For example:

```
Fix typo in introduction to user guide
```

Nothing more need be said; if the reader wonders what the typo was, she can simply take a look at the change itself, i.e. use `git show` or `git diff` or `git log -p`.

However, when a commit merits a bit of explanation and context, you need to write a body. For example:

```
Derezz the master control program

MCP turned out to be evil and had become intent on world domination.
This commit throws Tron's disc into MCP (causing its deresolution)
and turns it back into a chess game.
```

The separation of subject from body pays off when browsing the log. Here's the full log entry:

```
$ git log
commit 42e769bdf4894310333942ffc5a15151222a87be
Author: Kevin Flynn <kevin@flynnsarcade.com>
Date:   Fri Jan 01 00:00:00 1982 -0200

 Derezz the master control program

 MCP turned out to be evil and had become intent on world domination.
 This commit throws Tron's disc into MCP (causing its deresolution)
 and turns it back into a chess game.
```

And now `git log --oneline`, which prints out just the subject line:

```
$ git log --oneline
42e769 Derezz the master control program
```

Or, `git shortlog`, which groups commits by user, again showing just the subject line for concision:

```
$ git shortlog
Kevin Flynn (1):
      Derezz the master control program

Alan Bradley (1):
      Introduce security program "Tron"

Ed Dillinger (3):
      Rename chess program to "MCP"
      Modify chess program
      Upgrade chess program

Walter Gibbs (1):
      Introduce protoype chess program
```

There are a number of other contexts in Git where the distinction between subject line and body kicks in—but none of them work properly without the blank line in between.

---

## Rule 2: Limit the subject line to 50 characters

50 characters is not a hard limit, just a rule of thumb. Keeping subject lines at this length ensures that they are readable, and forces the author to think for a moment about the most concise way to explain what's going on.

**Tip:** If you're having a hard time summarizing, you might be committing too many changes at once. Strive for atomic commits (a topic for a separate post).

GitHub's UI is fully aware of these conventions:
- It will warn you if you go past the 50 character limit
- It will truncate any subject line longer than 72 characters with an ellipsis

**So shoot for 50 characters, but consider 72 the hard limit.**

---

## Rule 3: Capitalize the subject line

This is as simple as it sounds. Begin all subject lines with a capital letter.

For example:

✅ **Good:**
```
Accelerate to 88 miles per hour
```

❌ **Bad:**
```
accelerate to 88 miles per hour
```

---

## Rule 4: Do not end the subject line with a period

Trailing punctuation is unnecessary in subject lines. Besides, space is precious when you're trying to keep them to 50 chars or less.

✅ **Good:**
```
Open the pod bay doors
```

❌ **Bad:**
```
Open the pod bay doors.
```

---

## Rule 5: Use the imperative mood in the subject line

**Imperative mood** just means "spoken or written as if giving a command or instruction". A few examples:

- Clean your room
- Close the door
- Take out the trash

The imperative can sound a little rude; that's why we don't often use it. But it's perfect for Git commit subject lines. One reason for this is that **Git itself uses the imperative whenever it creates a commit on your behalf.**

For example, the default message created when using `git merge` reads:

```
Merge branch 'myfeature'
```

And when using `git revert`:

```
Revert "Add the thing with the stuff"

This reverts commit cc87791524aedd593cff5a74532befe7ab69ce9d.
```

Or when clicking the "Merge" button on a GitHub pull request:

```
Merge pull request #123 from someuser/somebranch
```

So when you write your commit messages in the imperative, you're following Git's own built-in conventions.

### The Imperative Test

**A properly formed Git commit subject line should always be able to complete the following sentence:**

> **"If applied, this commit will _[your subject line here]_"**

✅ **Good examples:**

- If applied, this commit will **refactor subsystem X for readability**
- If applied, this commit will **update getting started documentation**
- If applied, this commit will **remove deprecated methods**
- If applied, this commit will **release version 1.0.0**
- If applied, this commit will **merge pull request #123 from user/branch**

❌ **Bad examples (notice how these don't work):**

- If applied, this commit will ~~fixed bug with Y~~ (wrong tense)
- If applied, this commit will ~~changing behavior of X~~ (wrong tense)
- If applied, this commit will ~~more fixes for broken stuff~~ (not imperative)
- If applied, this commit will ~~sweet new API methods~~ (not imperative)

### Additional Examples

✅ **Good (imperative mood):**
- Refactor subsystem X for readability
- Update getting started documentation
- Remove deprecated methods
- Release version 1.0.0

❌ **Bad (not imperative):**
- Fixed bug with Y
- Changing behavior of X
- More fixes for broken stuff
- Sweet new API methods

**Remember:** Use of the imperative is important only in the subject line. You can relax this restriction when you're writing the body.

---

## Rule 6: Wrap the body at 72 characters

Git never wraps text automatically. When you write the body of a commit message, you must mind its right margin, and wrap text manually.

The recommendation is to do this at **72 characters**, so that Git has plenty of room to indent text while still keeping everything under 80 characters overall.

A good text editor can help here. It's easy to configure Vim, for example, to wrap text at 72 characters when you're writing a Git commit. Traditionally, however, IDEs have been terrible at providing smart support for text wrapping in commit messages (although in recent versions, IntelliJ IDEA has finally gotten better about this).

**This is a manual process.** You must insert line breaks yourself. Git will not do it for you.

---

## Rule 7: Use the body to explain what and why vs. how

This commit from Bitcoin Core is a great example of explaining what changed and why:

```
commit eb0b56b19017ab5c16c745e6da39c53126924ed6
Author: Pieter Wuille <pieter.wuille@gmail.com>
Date:   Fri Aug 1 22:57:55 2014 +0200

 Simplify serialize.h's exception handling

 Remove the 'state' and 'exceptmask' from serialize.h's stream
 implementations, as well as related methods.

 As exceptmask always included 'failbit', and setstate was always
 called with bits = failbit, all it did was immediately raise an
 exception. Get rid of those variables, and replace the setstate
 with direct exception throwing (which also removes some dead
 code).

 As a result, good() is never reached after a failure (there are
 only 2 calls, one of which is in tests), and can just be replaced
 by !eof().

 fail(), clear(n) and exceptions() are just never called. Delete
 them.
```

Take a look at the full diff and just think how much time the author is saving fellow and future committers by taking the time to provide this context here and now. If he didn't, it would probably be lost forever.

**In most cases, you can leave out details about how a change has been made.** Code is generally self-explanatory in this regard (and if the code is so complex that it needs to be explained in prose, that's what source comments are for).

Just focus on making clear the reasons why you made the change in the first place:

- **Why** you made the change
- The way things worked **before** the change (and what was wrong with that)
- The way they work **now**
- Why you decided to solve it the way you did

The future maintainer that thanks you may be yourself!

---

## Complete Example Template

```
Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequences of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789
```

---

## Additional Examples

### Simple Commit (No Body Needed)

```
Fix typo in introduction to user guide
```

**Why this works:**
- Self-explanatory
- Follows all 7 rules
- No body needed - the diff tells the full story

### Feature Addition

```
Add caching to database queries

The application was making redundant database queries on every
request, causing performance issues under load. Implementing a
simple in-memory cache reduces database calls by ~80% for typical
usage patterns.

The cache expires after 5 minutes and is cleared on any write
operation to ensure data consistency.

Resolves: #234
```

**Why this works:**
- Subject: imperative, 32 chars, capitalized, no period
- Body explains the problem, solution, and trade-offs
- References the issue
- Focuses on "what and why" not implementation details

### Refactoring

```
Extract user validation into separate module

The authentication controller had grown to over 500 lines and
mixed concerns between request handling, validation, and session
management. This made it difficult to test and reason about.

This commit extracts validation logic into a dedicated module.
No functional changes, but the code is now more maintainable and
easier to extend for additional validation rules.

See also: #445
```

**Why this works:**
- Explains the problem (mixed concerns)
- Describes the solution at high level
- Notes "no functional changes" to help reviewers
- Explains benefits (maintainability, extensibility)

### Bug Fix

```
Prevent null pointer exception in user profile

When a user has no profile picture set, the profile page would
crash with a null pointer exception. This adds a null check and
displays a default avatar image instead.

The root cause was assuming all users would have a profile_picture
field, but legacy accounts migrated from the old system don't have
this field populated.

Fixes: #567
```

**Why this works:**
- Subject clearly states what's fixed
- Body explains the bug and the fix
- Includes important context (legacy data issue)
- References the bug ticket

---

## What NOT to Do

### ❌ Bad: No Commit Message

```
$ git commit -m "update"
```

This tells you nothing about what changed or why.

### ❌ Bad: Vague Subject

```
$ git commit -m "fix stuff"
```

What stuff? What was broken?

### ❌ Bad: Too Long Subject

```
$ git commit -m "Add support for OAuth2 authentication so users can log in with their Google or GitHub accounts which really improves the onboarding experience"
```

This is 131 characters. Way too long. The detail belongs in the body.

### ❌ Bad: Wrong Tense

```
$ git commit -m "Fixed bug with user login"
```

Should be "Fix bug with user login" (imperative mood).

### ❌ Bad: Not Capitalized

```
$ git commit -m "add oauth support"
```

Should start with capital letter.

### ❌ Bad: Has Period

```
$ git commit -m "Add OAuth support."
```

No period at the end of the subject line.

### ❌ Bad: Body Lines Too Long

```
Add OAuth support

This commit adds OAuth2 authentication so users can log in with their existing Google or GitHub accounts instead of creating new credentials which reduces friction in the onboarding process.
```

The body line is way over 72 characters and should be manually wrapped.

### ❌ Bad: No Blank Line

```
Add OAuth support
Users can now authenticate with Google or GitHub.
```

Missing blank line between subject and body. Tools will get confused.

---

## Summary

The seven rules are:

1. Separate subject from body with a blank line
2. Limit the subject line to 50 characters
3. Capitalize the subject line
4. Do not end the subject line with a period
5. Use the imperative mood in the subject line
6. Wrap the body at 72 characters
7. Use the body to explain what and why vs. how

Follow these rules consistently and your commit history will be clean, professional, and actually useful for understanding the evolution of your codebase.
