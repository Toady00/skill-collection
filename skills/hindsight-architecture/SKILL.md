---
name: hindsight-architecture
description: Use when designing Hindsight memory bank architecture, tags, entity labels, observation scopes, mental models, or bank templates for agent memory systems.
version: 0.1.1
---

# Hindsight Architecture

Use this skill to help a user design an effective Hindsight memory
architecture. The goal is to convert a messy real-world operating model into a
small number of memory banks, a disciplined tag taxonomy, and reusable bank
templates that let agents recall useful context without unnecessary
orchestration.

This skill has two phases:

- Phase 1: Help the user design the memory architecture.
- Phase 2: Generate a correct, minimal Hindsight bank template that supports the architecture.

Use `templates/bank-template-defaults.reference.json` as the reference for the
bank template shape and default values when generating templates. It is a
reference file, not something to copy wholesale.

## Core Principle

Treat a Hindsight memory bank as an isolated brain for a durable world, not as
a folder, topic, or agent profile.

Use memory banks for hard boundaries. Use tags for views inside a bank.

Good bank boundaries usually come from:

- Privacy or access-control isolation
- Different memory subjects that should not influence each other
- Different bank-level missions, directives, disposition, or extraction behavior
- Very high-volume or noisy memory streams that would degrade retrieval
- Separate corpora where cross-domain reasoning is rarely useful

Good tag boundaries usually come from:

- Domain, topic, source, session, repo, service, feature, or workflow
- Agent harness or agent profile that wrote the memory
- Memory shape such as ADR, spec, runbook, decision, risk, or open question
- Visibility tiers inside an otherwise shared context
- Filters needed for recall, reflect, observations, or mental models

## Vocabulary

Avoid treating the word "agent" as precise. Ask what the user means.

- Harness: The runtime that calls the LLM and tools, such as opencode, Claude Code, OpenClaw, CI reviewers, or monitoring automations.
- Harness agent/profile: A named instruction set or persona inside a harness, such as `spec-writer`, `testing`, `architect`, or `reviewer`.
- Memory consumer: Anything that reads memory.
- Memory writer: Anything that retains memory.
- Memory subject: What the memory is about, such as a person, company, platform, repo, service, customer, incident, or product.
- Memory bank: An isolated Hindsight brain for one memory subject or hard boundary.
- Tags: Structured dimensions and visibility filters inside a bank.

A different harness agent/profile is not by itself a reason to create a
separate bank. If `spec-writer` and `testing` both work on the same codebase,
they usually belong in the same project/platform bank with `agent_profile:*`
tags.

## First Answer To Give

When the user asks whether all of this is configurable via a memory bank
template, answer:

Hindsight bank templates are the right place to capture bank-level
configuration such as missions, entity labels, directives, observation
behavior, mental models, and reusable defaults. Templates should not be treated
as the only place to enforce architecture. Some architecture lives in client or
integration configuration, especially default retain tags, recall filters,
dynamic bank IDs, source tags, and per-harness behavior.

In practice:

- Put stable bank behavior in the Hindsight bank template.
- Put writer-specific tags and source metadata in opencode, OpenClaw, CI, ingestion jobs, and other integrations.
- Put operating conventions in documentation or a skill so future agents apply them consistently.

When generating a bank template, keep it minimal. Do not explicitly set fields
that match Hindsight defaults. Only set defaults when the user explicitly asks
for a fully expanded reference template, and label it as expanded/noisy.

## Reference Template

Before generating a Hindsight bank template, read
`templates/bank-template-defaults.reference.json` from this skill directory.

Use it to understand:

- The top-level template shape: `version`, `bank`, `mental_models`, and `directives`.
- Which bank fields have defaults.
- Which bank fields default to `null` because they are intentionally unset.
- The object shape for mental models and directives.
- The trigger shape for mental models.

Do not treat placeholder mental models or directives in the reference file as
defaults. They show the valid shape for optional user-defined sections. They
should not appear in generated output unless the architecture calls for real
mental models or directives.

Never copy every field from the reference file into a generated template. That
creates noise and hides the user's intentional configuration.

## Generation Rules

Generate minimal templates.

- Omit bank fields that would be set to their default value.
- Omit `null` fields unless the template format specifically requires them.
- Omit empty `mental_models` and `directives` arrays unless the user's target format requires arrays to be present.
- Omit example placeholders from the reference file.
- Include missions, entity labels, directives, and mental models when they are part of the designed architecture.
- Include `retain_extraction_mode` only when changing away from the default or when paired with `retain_custom_instructions`.
- Include `retain_custom_instructions` only when `retain_extraction_mode` is `custom` and custom extraction rules are actually needed.
- Include disposition traits only when the reflect behavior needs a defensible non-default posture.
- Include recall budget or consolidation tuning only when the user has a concrete latency, quality, scale, or cost reason.
- Include `mcp_enabled_tools` only when the architecture needs tool restrictions.

Do not change a default without explaining why. The explanation must be tied to
the architecture, such as privacy, extraction quality, domain-specific behavior,
latency, cost, retrieval quality, or operational safety. Prefer leaving defaults
alone when the reason is weak.

When a value is intentionally omitted because it matches the default, do not
apologize or add it for completeness. Omission is the correct minimal template.

## Two-Phase Workflow

Phase 1 is architecture design. Do not jump to JSON too early.

- Interview the user using the workflow below.
- Produce an architecture recommendation.
- Confirm the bank layout, tag taxonomy, entity labels, observation scopes, mental models, directives, and integration defaults.
- Identify which parts belong in the Hindsight bank template and which parts belong in clients or ingestion code.

Phase 2 is template generation.

- Read the reference template.
- Generate one template per bank that needs custom bank-level behavior.
- Keep each template minimal and intentional.
- Explain every non-default bank field included.
- Separately list integration settings that cannot be encoded in the bank template.
- Provide a validation checklist so the user can review the template before applying it.

## Interview Workflow

Ask only the questions needed to disambiguate the architecture. Prefer a
concise interview over a long form.

1. Identify memory subjects.

Ask what durable worlds need memory. Examples: personal memory, company memory,
platform memory, customer memory, research corpus, observability stream.

2. Identify hard boundaries.

Ask what must not leak or cross-influence. Examples: personal vs company,
customer A vs customer B, restricted legal/finance data, generated market
research vs operating knowledge.

3. Identify cross-domain reasoning needs.

Ask which topics must be reasoned about together. If the user needs one
conversation to move between business, product, engineering, and operations,
prefer one shared bank with tags.

4. Identify writers and consumers.

List harnesses, agent profiles, CI jobs, ingestion pipelines, monitoring
systems, chat interfaces, and humans. Treat these as source/profile tags unless
they need hard isolation.

5. Identify retrieval modes.

Ask whether default recall should be broad, scoped, or strict. Broad default
recall favors fewer banks. Strict per-user/customer recall favors either
separate banks or mandatory strict tags.

6. Identify high-volume/noisy streams.

Separate, defer, sample, or configure dedicated ingestion for high-volume
observability, public research corpora, market data, logs, and generated
analysis artifacts if they might overwhelm durable operating memory. Do not
recommend pre-summarizing normal conversations, docs, transcripts, specs, or
agent sessions before retain.

7. Define mental models.

Use mental models as curated cross-sections of one bank, not as a substitute
for bank design. Prefer narrow mental models over one giant summary.

## Decision Heuristics

Use one shared bank when:

- The user wants easy transitions between roles or domains.
- Cross-domain reasoning is a major feature.
- Multiple harnesses work on the same company, product, platform, or repo.
- The cost of multi-bank orchestration would be high.
- Privacy can be handled by strict tags and operating discipline.

Use multiple banks when:

- Data isolation is more important than cross-domain reasoning.
- Recall should never mix two subjects by accident.
- Bank-level config differs materially.
- One corpus is large/noisy enough to harm retrieval in the main bank.
- Different subjects need different directives or dispositions during reflect.

Use tags when:

- You want to categorize related memories.
- You want to know where a memory came from.
- You want per-session, per-agent-profile, per-service, or per-feature views.
- You want to filter by memory shape.
- You want scoped observations or mental models within a shared world.

## Retain Input Fidelity

Hindsight is designed to ingest rich source material and extract durable facts,
entities, relationships, observations, and mental models from it. Do not tell
users to pre-summarize normal source content before retain.

Prefer retaining the richest practical representation available:

- Full conversations or transcripts with clear roles, timestamps, and structure
- Raw docs, specs, ADRs, runbooks, tickets, and notes
- Structured JSON conversations when available
- Tool calls and relevant tool results when they explain decisions or outcomes
- Stable `document_id` values so updated source material is reprocessed idempotently

Pre-summarizing before retain usually causes fidelity loss. Hindsight would be
extracting memories from an already-compressed version of the source, which can
drop temporal details, entity relationships, causal context, uncertainty,
contradictions, and exact phrasing that Hindsight needs for better extraction.

Use Hindsight's retain strategy, chunking, extraction mode, missions, document
upserts, async retain, tagging, and bank separation to manage ingestion quality
and cost before reaching for pre-summarization.

Exceptions are narrow and should be explicit:

- Raw logs, metrics, traces, or event streams that are too large or repetitive to retain directly
- Third-party corpora where only curated excerpts are legally or operationally appropriate
- Generated artifacts that duplicate already-retained source material
- Cases where the user knowingly accepts lower fidelity in exchange for cost, volume, or privacy constraints

Even in exception cases, prefer retaining curated excerpts, structured incident
records, sampled events, or human/agent-authored postmortems rather than vague
summaries. Make the tradeoff explicit.

## Tag Rules

Tags are visibility and retrieval filters. Metadata is for provenance and UI
linking, not filtering.

For shared banks, always establish a small required tag set for writers:

- `source:<system>` for where the memory came from
- `domain:<domain>` for broad area
- `memory_type:<type>` for shape
- `scope:<scope>` or `visibility:<tier>` when access matters
- A subject tag such as `user:<id>`, `company:<id>`, `repo:<name>`, `service:<name>`, or `customer:<id>` when applicable

Prefer strict tag matching for partitioned data:

- `any_strict` means at least one requested tag must be present and untagged memories are excluded.
- `all_strict` means all requested tags must be present and untagged memories are excluded.
- Avoid `tags_match="any"` in multi-tenant or privacy-sensitive banks unless untagged memories are intentionally global.
- Use `tag_groups` for complex boolean filters.

## Entity Labels

Recommend entity labels with `tag: true` for stable classifications that future
recalls should filter on.

Entity labels are extracted by Hindsight from retained content. Use them for
classifications the model can infer from the content. Do not use entity labels
for fields the writer already knows and should stamp deterministically, such as
`source:<system>`, `agent_profile:<name>`, `repo:<name>`, `document_id`, file
paths, commit hashes, or ingestion job IDs. Those belong in integration tags or
metadata.

Good entity label dimensions:

- `domain`: business, product, engineering, infrastructure, operations, finance, legal, research
- `memory_type`: mission, vision, adr, design-doc, spec, user-journey, feature, runbook, decision, risk, open-question, incident, research-note
- `status`: proposed, accepted, deprecated, active, blocked, resolved
- `system`: platform, web-app, analysis-engine, market-data, infra
- `infra`: aws, terraform, kubernetes, flux, postgres, redis

Use controlled enum values when possible. Free-text labels are less reliable
because wording may drift.

## Observation Scopes

Use observation scopes when a shared bank needs durable patterns at specific
tag levels.

Observation scopes are retain-time behavior for memories/items, not a general
bank-template field. Capture the observation-scope strategy in the architecture
and integration/ingestion guidance unless the target template format explicitly
supports encoding it. Do not imply that named observation scopes can be set once
globally in the bank template unless the current Hindsight template schema
supports that field.

- `combined`: best for simple single-subject banks.
- `per_tag`: best when individual tags represent independent subjects, such as user, team, service, or project.
- `all_combinations`: powerful but expensive; avoid as a default.
- Custom scopes: best for precise multi-dimensional memory such as user-level, team-level, and combined observations.

For most serious shared-bank architectures, recommend explicit custom scopes
for the few combinations that matter.

## Disposition Traits

Disposition traits affect `reflect` only. They do not affect `retain` or
`recall`. Treat them as reasoning-style controls, not as a substitute for clear
missions, tags, directives, or retrieval filters.

The effective default posture is balanced. In the reference template the
disposition fields are `null`, which means the bank uses the server/default
behavior. Do not explicitly set balanced defaults just for completeness.

Set disposition traits only when the bank itself needs a defensible non-default
reflect posture. Do not tune dispositions for narrow tasks like "code review" or
"security review" unless those tasks have their own dedicated bank. For a broad
company/platform bank, task-specific posture should usually come from the
reflect query, directives, tool instructions, or the calling harness.

Think in bank archetypes:

- Personal assistant bank: Usually benefits from higher empathy, balanced skepticism, and balanced literalism. The bank is about helping a person across life/work context, so warmth and personalization are useful, but it should still distinguish fact from inference.
- Company/platform operating bank: Usually balanced or slightly skeptical, balanced-to-higher literalism, and low-to-moderate empathy. The bank spans business, product, engineering, infrastructure, and operations, so the default posture should be grounded and careful without becoming adversarial.
- Investment research or institutional analysis bank: Usually high skepticism, high literalism, and low-to-moderate empathy. The bank should challenge claims, separate evidence from inference, avoid overstatement, and reason conservatively.
- Customer-specific support bank: Usually moderate skepticism, moderate literalism, and higher empathy. The bank should understand the customer's history and tone while still checking facts before making claims.
- Product documentation or product facts bank: Usually higher literalism, moderate-to-high skepticism, and lower empathy. The bank should answer from documented behavior and avoid inventing unsupported product capabilities.
- Observability or incident knowledge bank: Usually high skepticism, high literalism, and low-to-moderate empathy. The bank should prefer evidence, timestamps, concrete symptoms, and verified remediations.
- Legal, compliance, policy, or finance operations bank: Usually high skepticism, high literalism, and low empathy unless it is directly user-facing. The bank should be conservative and avoid inference drift.

When one broad bank must serve multiple task postures, leave dispositions closer
to balanced and encode task posture outside the bank:

- Query framing: "Reflect as a skeptical code reviewer..." or "Analyze this from a customer-success perspective..."
- Directives: hard rules that should always apply to the bank, such as "Do not invent unsupported product behavior."
- Mental models: curated scoped views, such as "Product Facts" or "Engineering Principles".
- Tags: retrieval scoping, not disposition scoping.

If the desired postures would conflict in routine use, prefer separate banks.
Example: a main company/platform bank may remain balanced, while an investment
research bank uses high skepticism and literalism. In customer support, a
customer-specific memory bank can use a more empathetic support posture, while a
separate product-facts bank can use a stricter documentation posture.

Prefer omitting disposition fields unless the architecture clearly benefits from
one of these non-default postures. If included in a generated template, explain
why each trait is set and how it supports the bank's purpose.

## Mental Models

Use mental models to make broad banks usable. They provide curated, fast,
reusable views of the memory bank.

Prefer narrow mental models tailored to the use case.

Personal assistant examples:

- User Profile
- Current Commitments
- Communication Preferences
- Important Relationships
- Health, Travel, and Scheduling Preferences
- Current Projects and Goals

Customer support examples:

- Customer Account Summary
- Product Entitlements and Configuration
- Open Support Risks
- Prior Resolutions
- Escalation History
- Common Issue Patterns

Company or platform examples:

- Company Mission and Strategy
- Go-Live Plan
- Product Vision
- Target Customer and User Journeys
- Platform Architecture
- Bounded Context Map
- Infrastructure Architecture
- Engineering Principles and ADR Summary
- Current Risks and Open Questions
- Operating Principles

Research or analyst examples:

- Research Thesis
- Evidence Summary
- Contradictions and Open Questions
- Source Quality Notes
- Current Recommendations

Avoid a single mental model called "Everything" or "All Context". It will be
low quality and hard to refresh.

Tags on a mental model both filter which memories build it and control which
recall/reflect calls can see it. Use this to create scoped models inside a
shared bank.

Important: do not use mental model `tags` as merely descriptive labels. In
Hindsight, mental model tags affect both visibility and refresh/source-memory
selection. Tagged mental models commonly refresh with strict matching semantics,
so a model tagged with many tags may only read memories that contain every one
of those tags. That can accidentally produce empty or overly narrow mental
models.

Mental model tag guidance:

- Use the fewest tags needed to scope source memories and visibility.
- Prefer one broad subject tag such as `domain:product` over many `memory_type:*` tags when the model should synthesize several memory types.
- Do not tag a model with multiple alternative memory types such as `memory_type:mission`, `memory_type:vision`, and `memory_type:decision` unless source memories are expected to carry all of them together.
- If the model should read an OR set of tags, use `trigger.tag_groups` or another supported filter mechanism from the current template schema instead of stacking tags as if they were descriptive metadata.
- If tag filters are too restrictive, leave `tags` empty and make the `source_query` precise, or create multiple narrower mental models.
- Use the model `name` and `source_query` for human description. Use tags only for real visibility/source filtering.

## Architecture Archetypes

Use archetypes as starting points, not rules. Always adjust for privacy,
cross-domain reasoning, retrieval quality, and operational complexity.

### Personal Assistant Or Second Brain

Default to one bank for the person.

- Bank: `user:<id>` or a stable personal name.
- Use tags for life domains, projects, source systems, people, topics, and sensitivity.
- Use broad default recall because the assistant's value comes from connecting personal context across domains.
- Add a separate bank only for hard boundaries such as work vs personal, sensitive legal/medical notes, or high-volume document/research corpora.

Common tags: `domain:personal`, `domain:work`, `domain:health`,
`domain:travel`, `project:<name>`, `person:<name>`, `source:<system>`,
`sensitivity:private`, `memory_type:preference`, `memory_type:commitment`,
`memory_type:goal`.

### Customer Support Or Customer Success

Choose between one shared customer-support bank with strict tags and one bank
per customer/user based on isolation requirements.

- Use one shared bank when cross-customer aggregate analysis is important and tag discipline is enforceable.
- Use one bank per customer or user when data isolation is a hard requirement or accidental leakage would be unacceptable.
- If using one shared bank, every retained customer memory must carry `customer:<id>` or `user:<id>` and recalls must use `any_strict` or `all_strict`.
- Keep public product documentation either untagged global only if safe, tagged as `scope:global`, or in a separate product-docs bank if it is large.

Common tags: `customer:<id>`, `user:<id>`, `account:<id>`,
`product:<name>`, `plan:<tier>`, `case:<id>`, `severity:<level>`,
`status:open`, `status:resolved`, `memory_type:ticket`,
`memory_type:resolution`, `memory_type:escalation`, `scope:global`.

### Company, Product, Or Platform Builder

Default to one company/platform bank when the user needs to move fluidly across
business, product, engineering, infrastructure, and operations.

- Bank: company, product, or platform name.
- Use tags for business domains, product areas, repos, services, infrastructure, source systems, and agent profiles.
- Add a personal bank for private preferences and individual operating style when needed.
- Add separate research or observability banks later if those streams become large or noisy.
- Do not split business, product, engineering, and infrastructure into separate banks unless the user rarely needs to reason across them or has hard access boundaries.

Common tags: `domain:business`, `domain:product`, `domain:engineering`,
`domain:infrastructure`, `repo:<name>`, `service:<name>`,
`source:<system>`, `agent_profile:<name>`, `memory_type:adr`,
`memory_type:spec`, `memory_type:user-journey`, `memory_type:runbook`,
`memory_type:risk`.

### Multi-Service Engineering Platform

Prefer one platform bank when cross-service reasoning matters.

- Use tags for services, repos, bounded contexts, teams, environments, ADRs, specs, incidents, and runbooks.
- Use separate service banks only when service-level isolation or retrieval quality matters more than cross-service reasoning.
- Use mental models for platform architecture, service ownership, ADR summaries, and active risks.

Common tags: `service:<name>`, `repo:<name>`, `team:<name>`,
`bounded-context:<name>`, `env:prod`, `env:staging`, `memory_type:adr`,
`memory_type:incident`, `memory_type:runbook`, `memory_type:decision`.

### Research, Analysis, Or Knowledge Corpus

Separate external or generated corpora from operating memory when they are large,
noisy, or citation-sensitive.

- Use a dedicated research bank for large public documents, filings, papers, generated reports, or source-heavy analysis.
- Use tags for issuer, topic, source, document type, time period, confidence, and analyst workflow stage.
- Keep business/product/engineering operating memory in a separate bank if the research corpus would dominate retrieval.

Common tags: `issuer:<ticker>`, `sector:<name>`, `source:<provider>`,
`document_type:10-k`, `document_type:earnings-call`, `period:<yyyy-qn>`,
`memory_type:thesis`, `memory_type:risk`, `memory_type:evidence`,
`confidence:<level>`.

### Observability, Monitoring, Or Incident Automation

Keep high-volume operational streams out of broad human operating memory unless
they are curated into durable records that preserve the important evidence.

- Use a separate observability bank when retaining frequent alerts, metrics summaries, incident traces, or log-derived observations.
- Use the main company/platform bank only for durable incident learnings, accepted remediations, and architectural decisions.
- Prefer structured incident records, postmortems, sampled events, and curated log excerpts over raw log firehoses.
- Avoid pre-summarizing normal incident conversations or investigation transcripts; retain the rich transcript or postmortem when practical so Hindsight can extract facts with less fidelity loss.

Common tags: `env:<name>`, `service:<name>`, `incident:<id>`,
`severity:<level>`, `signal:latency`, `signal:error-rate`,
`memory_type:incident`, `memory_type:remediation`, `memory_type:runbook`.

## Output Format

When producing an architecture recommendation, use this structure:

### Recommendation

State the recommended bank layout in one short paragraph.

### Banks

List each bank with purpose, what belongs there, what does not belong there,
and why it is or is not separate.

### Tags

Define required tags, recommended tags, and examples.

### Entity Labels

List proposed entity label groups, enum values, and which labels should set
`tag: true`.

### Observation Scopes

Specify whether to use `combined`, `per_tag`, `all_combinations`, or custom
scopes.

### Mental Models

List the mental models to create, including suggested tags and refresh
strategy. Verify that model tags are real source/visibility filters, not just
descriptive labels. If the model needs OR filtering across domains or memory
types, specify supported `trigger.tag_groups` rather than stacking tags that
will be interpreted as an AND-style source filter.

### Integration Defaults

Describe how opencode, OpenClaw, CI, ingestion jobs, and monitoring systems
should set bank IDs, retain tags, source metadata, and recall behavior.

### Template Boundary

Separate what belongs in a Hindsight bank template from what belongs in
client/integration configuration.

### Template Plan

List each template to generate, the non-default fields it needs, and why those
fields are justified. Explicitly say which defaults will be omitted.

### Risks

Call out leakage, noisy corpora, tag discipline, over-filtering, and
over-fragmentation risks.

## Template Output Format

When the user asks for the actual config file, use this structure:

### Template

Provide valid JSON for the minimal Hindsight bank template.

### Non-Default Fields

Explain each non-default field included and why it supports the architecture.

### Defaults Intentionally Omitted

Briefly summarize categories of defaults omitted, such as retain chunk sizing,
consolidation tuning, recall budget mapping, observation enablement, and default
entity behavior. Do not list every omitted field unless the user asks.

### Integration Configuration

List required opencode, OpenClaw, CI, ingestion, or monitoring settings that are
outside the bank template.

### Validation Checklist

Include a short checklist:

- No copied placeholder mental models or directives remain.
- No field is set to a default value just for completeness.
- Every non-default bank field has a defensible reason.
- Entity label values match the proposed tag taxonomy.
- Mental model tags align with intended visibility and source memory filters.
- Mental model tags are not being used as descriptive metadata; long tag lists are checked for accidental over-filtering.
- OR-style mental model source filters use supported `trigger.tag_groups` or separate models instead of many tags on one model.
- Directives are true hard rules, not general preferences.
- Integration defaults cover bank ID, retain tags, document IDs, source metadata, and recall filters.

## Template Boundary Checklist

Put this in a bank template:

- `retain_mission`
- `observations_mission`
- `reflect_mission`
- Disposition traits
- Entity labels
- Directives
- Default recall budget configuration when supported
- Mental model definitions when supported by the template workflow
- Bank-level MCP tool allowlists when needed

Only include these fields when they are intentionally configured. The existence
of a field in the reference template does not mean it should appear in generated
output.

Put this in integrations or ingestion code:

- Default `bank_id`
- Dynamic bank ID strategy
- Default retain tags
- Default recall tags and `tags_match`
- Retain-time `observation_scopes` strategy when scoped observations are needed
- Source-specific metadata
- Document ID conventions
- Retain cadence
- Which roles/tool calls to retain
- Whether a writer should read broadly or with strict filters
- Whether any high-volume source needs sampling, excerpting, async retain, or a separate bank instead of pre-summarization

Put this in human/agent operating docs:

- Tag naming conventions
- When to create a new bank
- When to create a new mental model
- What should never be retained
- What should be retained as rich/raw structured source material rather than pre-summarized text
- How to handle sensitive data

## Minimal Template Example

Use examples like this to demonstrate minimal output. Do not include all default
bank fields.

```json
{
  "version": "1",
  "bank": {
    "retain_mission": "Extract durable facts relevant to the user's long-term goals, preferences, commitments, relationships, and active projects. Ignore greetings, filler, and short-lived logistics unless they create future obligations.",
    "observations_mission": "Synthesize stable preferences, recurring patterns, commitments, and contradictions. Ignore one-off transient state.",
    "reflect_mission": "You are a personal assistant with long-term memory. Ground answers in retained facts, distinguish known facts from inference, and personalize responses to the user's preferences.",
    "entity_labels": [
      {
        "key": "domain",
        "description": "The life or work domain this memory belongs to.",
        "type": "multi-values",
        "tag": true,
        "values": [
          { "value": "personal", "description": "Personal life, preferences, relationships, and routines." },
          { "value": "work", "description": "Work, projects, professional commitments, and career context." }
        ]
      }
    ]
  },
  "mental_models": [
    {
      "id": "user-profile",
      "name": "User Profile",
      "source_query": "Summarize the user's stable preferences, goals, recurring commitments, and important context for personal assistance.",
      "tags": ["domain:personal"],
      "max_tokens": 2048
    }
  ]
}
```

In this example, defaults such as `retain_extraction_mode: concise`,
`retain_chunk_size: 3000`, `enable_observations: true`, recall budget mapping,
and consolidation tuning are intentionally omitted.

## Example Reasoning

Personal assistant: If the user wants a second brain that remembers personal
preferences, commitments, goals, relationships, travel, health, and work context,
prefer one personal bank. The assistant's value comes from cross-domain recall.
Use tags for domains, projects, people, source systems, and sensitivity. Split
only for hard work/personal/privacy boundaries or large external corpora.

Customer support: If the system serves many customers, decide whether aggregate
cross-customer learning is worth the operational risk. If yes, use one support
bank with mandatory `customer:<id>` or `user:<id>` tags and strict recall. If
leakage would be unacceptable, use one bank per customer or user and optionally a
separate global product-docs bank.

Company/platform builder: If the user has business operations, product strategy,
engineering architecture, infrastructure, coding harnesses, and conversational
interfaces, prefer one company/platform bank. The user's main need is fluid
cross-domain reasoning. Use tags for business, product, engineering,
infrastructure, repo, service, source, and agent profile. Add separate banks
later only for personal private memory, large research corpora, or high-volume
observability streams.

Research corpus: If the user plans to ingest large volumes of public documents,
market data, filings, or generated analysis, prefer a dedicated research bank so
the corpus does not drown out operating memory. Link it conceptually with tags
and naming, but do not force operational agents to search it by default.

Observability: If monitoring agents retain frequent alerts or metric summaries,
prefer a separate observability bank, sampling, curated excerpts, or structured
incident records instead of pushing raw firehoses into the platform bank. Retain
rich incident conversations and postmortems when practical. Durable incident
learnings and remediation decisions can be promoted into the main platform bank.
