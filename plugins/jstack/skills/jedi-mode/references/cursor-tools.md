# Cursor tool mapping for jstack

jstack skills are written in Claude Code tool language (the `Skill` tool, the `Agent` tool, `AskUserQuestion`, Claude model names). In Cursor, local or cloud agent, the skills are the same files, vendored into a repo's `.agents/skills/` or installed by the skills CLI. Read this when a jstack skill names a Claude tool, subagent type, or model. This file is Cursor-specific.

The `.cursor/rules/jstack-models.mdc` rule at the root of the jstack repository carries the subagent and model rules below in a form a repo can copy into its own `.cursor/rules/`. Cursor applies it to every chat, so a repo that vendors only some skills still gets the role models.

## Tool actions

| jstack / Claude action | Cursor equivalent |
|------------------------|-------------------|
| Invoke a skill (the `Skill` tool, `/command`) | Read the skill's `SKILL.md` and follow it. |
| Dispatch a subagent (the `Agent` tool) | `Task` |
| Dispatch N parallel subagents in one turn | N `Task` calls in one response |
| Run a subagent in the background (`run_in_background: true`) | `Task` with `run_in_background: true` |
| Track tasks (`TaskCreate` / `TaskUpdate`, or `TodoWrite`) | `TodoWrite` |
| Ask the human a fixed-choice question (`AskUserQuestion`) | `AskQuestion` where the session has it; otherwise ask in plain text. A cloud agent cannot wait for an answer, so it takes the documented default and says so. |

## Subagent policy

- Every jstack subagent type becomes `subagent_type: "generalPurpose"`: `general-purpose`, `jstack:jedi-agent`, and the `jstack:effort-<level>` and `jstack:jedi-agent-<level>` variants. Tell a stand-in for `jstack:jedi-agent` to read the `jedi-mode` skill in full first.
- There is no `jstack:comment-sicko` subagent type. The **no-comments** skill spawns a `generalPurpose` `Task` whose instructions tell it to read `jedi-mode/references/agents/comment-sicko.md` in full first, on the `no-comments reviewer` model.
- There are no `thermo-nuclear-review-subagent` or `thermo-nuclear-code-quality-review-subagent` types. The **thermos** skill spawns two `generalPurpose` `Task` calls whose instructions tell each to read the `thermo-nuclear-review` or `thermo-nuclear-code-quality-review` skill in full first, on the `thermos reviewers` model.
- A role value's `@<level>` suffix and the sheet's `default effort` line do not apply. A Cursor slug carries its reasoning effort, and its speed where Cursor offers a `-fast` variant, as in `grok-4.7-high` or `grok-4.7-xhigh`. jstack defaults to the regular, non-fast variants.
- Pass `model` on every `Task` call a skill makes for a role. A call without it runs on the parent chat's model, so every explorer and worker would run on whatever model the parent chat uses.

## Model names

Skills name Claude defaults in their Models sections. Those names do not pick a Cursor model, and a `Task` call without `model` runs on the parent chat's model. Pass the role's Cursor slug as the `Task` tool's `model`, from the `jstack-models.mdc` rule when it is loaded, or from this table, which the rule copies:

| Role | Cursor model |
| --- | --- |
| feature, refactoring | `grok-4.7-xhigh` |
| bug-fix | `grok-4.7-xhigh` |
| perf-issue | `grok-4.7-xhigh` |
| hillclimb | `grok-4.7-xhigh` |
| judgment and prose | `grok-4.7-xhigh` |
| strongest judgment | `grok-4.7-xhigh` |
| mechanical edits | `composer-2.5` |
| how explorer | `grok-4.7-xhigh` |
| how explainer | `grok-4.7-xhigh` |
| why investigators | `grok-4.7-xhigh` |
| why synthesizer | `grok-4.7-xhigh` |
| reflect tooling | `grok-4.7-xhigh` |
| reflect judgment, divergent, synthesizer | `grok-4.7-xhigh` |
| arena runners | `grok-4.7-xhigh`, `composer-2.5` |
| arena cross-judge pool | `grok-4.7-xhigh`, `composer-2.5` |
| swarm workers | `grok-4.7-xhigh` |
| architect runners | `grok-4.7-xhigh`, `composer-2.5` |
| interrogate reviewers | `grok-4.7-xhigh`, `composer-2.5` |
| thermos reviewers | `grok-4.7-xhigh` |
| no-comments reviewer | `grok-4.7-xhigh` |

Grok runs every role except `mechanical edits`, which runs on Composer, and each diverse panel pairs a Grok seat with a Composer seat. A panel role runs one subagent per listed slug.

## Per-skill notes

| Skill | On Cursor |
|-------|-----------|
| `architect` | The runners are `Task` calls on the `architect runners` slugs. |
| `arena` | The runners and the cross-judge are `Task` calls on the `arena runners` and `arena cross-judge pool` slugs. Families go by prefix: `grok-*` and `composer-*` are separate families. |
| `how` | Explorers and the explainer are `Task` calls on the `how explorer` and `how explainer` slugs. |
| `no-comments` | There is no `comment-sicko` subagent type; see Subagent policy above. |
| `reflect` | The reviewers and the synthesizer are `Task` calls on the `reflect tooling` and `reflect judgment, divergent, synthesizer` slugs. The transcript finder reads Claude Code's layout, so pass the session digest step 1 allows instead. |
| `setup-jstack` | In Cursor the sheet is the `jstack-models.mdc` rule; see the skill's Other runtimes table. |
| `swarm` | Each worker is a `Task` call on the `swarm workers` slug. Give each writing worker its own worktree or output directory. |
| `thermos` | There are no thermo-nuclear subagent types; see Subagent policy above. |
| `why` | Investigators and the synthesizer are `Task` calls on the `why investigators` and `why synthesizer` slugs. List MCP servers from the tools Cursor exposes to the session. |

## Instructions file

Where a jstack skill says "your instructions file", in Cursor that is `AGENTS.md` or a rule under `.cursor/rules/`.
