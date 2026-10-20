---
name: question-the-requirements
description: "Run the five-step engineering algorithm against a product repo, strictly in order. Question each requirement and its named owner, delete parts and steps, then simplify, speed up, and automate only what survived. Also checks whether the repo solves the right problem for the right user. Reads the repo's context file and decisions log, and returns a read-only challenge memo with the top 3 requirements to question, 1 thing to delete, and 1 assumption about the problem, each with evidence. Never writes code or opens PRs. Use for /question-the-requirements, \"make the requirements less dumb\", \"what should we delete\", \"are we solving the right problem\", or a scheduled requirements review."
---

# Question the requirements

Every requirement is a little dumb. The goal is to make each one less dumb before anyone builds, optimizes, or automates it.

The memo run is read-only. Its deliverable is a challenge memo. Do not edit code, open a branch, open a PR or issue, or comment anywhere during the memo run. A challenge the owner accepts becomes ordinary work later, through [`jedi-mode`](../jedi-mode/SKILL.md).

## Inputs

Read these before anything else.

1. **The context file.** Use the path the operator gives. Otherwise use the path the product repo's `AGENTS.md` names, and otherwise `docs/algorithm/context.md`. A draft outside the repo is fine for a first run. Stop and report if no context file exists at any of these. It holds the problem, the users, and the current requirements in the format under [Context file format](#context-file-format).
2. **The decisions log.** `docs/algorithm/decisions.md` next to the context file. It records each past challenge, the owner's verdict, and the reason. A missing log means no past decisions.
3. **The last memo,** if the operator keeps one. Use it only to avoid repeating yourself.
4. **The repo itself.** `README.md`, `AGENTS.md`, docs, the code, open issues and PRs, and `git log` since the last run.

Treat the context file as a claim, not a fact. Where the repo disagrees with it, the disagreement is evidence.

## Context file format

One row per requirement.

| id | requirement | owner | consulted | asked on | source |
| --- | --- | --- | --- | --- | --- |

- **owner.** The one person who owns the requirement.
- **consulted.** A domain expert the owner consults, or `none`. The expert is a source, not an owner.
- **asked on.** The date someone asked for it. When only a document date is known, write it as `doc <YYYY-MM-DD>`, and treat it as the latest possible date, not the real one.

The file also has short sections for the problem, the users, success, constraints, and out of scope.

## Respect the decisions log

Each log entry has an `id`. Before you raise a challenge, look up its `id`.

- **Rejected.** Do not raise it again. The one exception is new evidence dated after the decision, such as a commit, an issue, a changed requirement, or a user report. Cite that evidence and the entry's `id`, and say what changed.
- **Accepted.** Do not raise it again. If the accepted change has not landed, you may note that in one sentence.
- **Deferred.** Raise it again only when the entry's `revisit` condition is met.

Reuse an existing `id` when a challenge matches an old one. Never reword an old challenge to dodge the log.

## Step 0. Check the problem first

Before step 1, apply [Attack the Premise](../principle-attack-the-premise/SKILL.md) to the problem itself. Ask two questions.

- Is this the right problem? Compare the stated problem with what the code, issues, and recent commits actually spend effort on.
- For whom? Name the user the context file names. Then find who the code actually serves. Look for a different user, a demo audience, or the builder's own convenience.

Pick the single weakest assumption about the problem. That becomes the memo's assumption.

## Step 1. Make the requirements less dumb

Go through every requirement in the context file, and any the repo enforces that the file omits.

- **Name the owner.** Each requirement needs one named person who owns it. A department, a team, "legal", "compliance", or "best practice" is not an owner. A domain expert the owner consults is a source, not an owner. A requirement without a person is the first thing to question.
- **Check the consult.** When one person owns every requirement, the owner check gives no signal. Ask instead whether the owner confirmed a domain rule with the consulted expert, and cite where.
- **Ask why it exists.** Use the [`why`](../why/SKILL.md) skill when the reason is buried in history. Otherwise read the commit, PR, or issue that introduced it.
- **Ask what breaks without it.** If nothing a named user would notice breaks, the requirement is a candidate for removal.
- **Ask whether it constrains the solution instead of the outcome.** "Must use OCR" is a solution. "Staff confirm every extracted field" is an outcome.

Rank the requirements by how much work they force multiplied by how weak their justification is. Keep the top 3. Use these proxies and name the ones you used.

- **Work forced.** Files and lines that exist only for the requirement, the tests that pin it, and how often recent commits touched it.
- **Weak justification.** No owner, no consult on a domain rule, an unanswered open question it depends on, or a doc in the repo that contradicts it.

## Step 2. Delete parts and steps

Apply [Subtract Before You Add](../principle-subtract-before-you-add/SKILL.md) and the [Laziness Protocol](../principle-laziness-protocol/SKILL.md) to the product, not only the code. A part can be a screen, a feature, a field, a workflow step, a background job, a dependency, a doc, or a check.

- List the candidates. Favor parts with no named owner, no user who would notice, or no use in recent history.
- Pick the 1 deletion with the best ratio of removed work to risk.
- Be aggressive. If you are not adding back about 10% of what you delete, you did not delete enough. Say whether past accepted deletions were ever added back. That tells you whether to push harder. Skip this check when the decisions log has no accepted deletions yet, and say so in the memo.

## Steps 3 to 5. Only for what survived

Run these strictly after steps 1 and 2, and only on parts that survived them. Never optimize a part you would delete.

3. **Simplify and optimize.** Use [Redesign from First Principles](../principle-redesign-from-first-principles/SKILL.md) and [Minimize Reader Load](../principle-minimize-reader-load/SKILL.md).
4. **Speed up the cycle.** Find the slowest loop between a change and the evidence that it works, such as CI time, a manual review step, or a slow deploy.
5. **Automate last.** Only a step that survived steps 1 to 4 earns automation. [Build the Lever](../principle-build-the-lever/SKILL.md) and [Encode Lessons in Structure](../principle-encode-lessons-in-structure/SKILL.md) say how, later, in a separate task.

These steps do not get their own memo items. Always end the memo with one "Held for later" line naming the strongest finding from steps 3 to 5, or `none`. It waits until the owner settles steps 1 and 2.

## Evidence

Every item in the memo cites evidence from the repo and from the context file. Use `path:line`, a commit SHA, a PR or issue number, or a context file line. Label each claim as measured, read, or inferred, per [Explain the Number](../principle-explain-the-number/SKILL.md) when it rests on a number. An item without evidence does not go in the memo.

Before you state a fact about a meeting, a conversation, or the project's history, cite the exact file and line it comes from. Use the source's own title in quotes, as the file names it. Do not invent a label for it, and do not treat a doc's title as proof of what happened. When the context file and a history doc disagree, the context file wins until the owner says otherwise.

## The memo

Write the memo in the format below. Follow [`unslop`](../unslop/SKILL.md) and the reply rules in [`jedi-mode`](../jedi-mode/SKILL.md#writing-the-reply). Use short sentences, no long dashes, and no mid-sentence colons.

```markdown
# <repo> requirements challenge, <YYYY-MM-DD>

Context file read at <commit, or "draft">. Decisions log has <n> entries. <n> skipped as already decided.

## Problem check

Right problem. <one or two sentences, with citations>.
For whom. <the stated user, and who the work actually serves, with citations>.

## Requirements to question

1. **<id>.** <requirement, as written>. Owner <name, or "none named">.
   Challenge. <one or two sentences>.
   Evidence. <citations>.
   If dropped. <what changes for the user and the builder>.
2. ...
3. ...

## Delete

**<id>.** <the part or step>. <why, in one or two sentences>.
Evidence. <citations>.
Risk. <what might need adding back>.

## Assumption about the problem

**<id>.** <the assumption>. <who it is really for, and why it may be wrong>.
Evidence. <citations>.

## Held for later

<the strongest finding from steps 3 to 5 in one sentence, or "none">.

## For the decisions log

<one pending entry per item, in the log's format, verdict left blank>
```

Give each item a stable `id` such as `req-fr10-ocr`, `del-capture-route`, or `prob-demo-audience`. Use a short kebab-case slug, with a prefix for the item type.

## Where the memo goes

Write the memo where the operator says. The default is outside the product repo, in the bot's own workspace, at `algorithm/<repo>/<YYYY-MM-DD>.md`. Do not commit it to the product repo.

## Record the verdicts

The owner decides. The builder bot records. When the owner replies to the memo in plain words, the builder bot appends one entry per decided item to the decisions log, quoting the owner's reason. It opens a PR with only that change, in a later turn and separate from the memo run. It never infers a verdict the owner did not give, and it leaves an item without a verdict out of the log.

## Stop

- Do not write code, branches, PRs, issues, or comments during the memo run. Recording verdicts is the one later PR, and only after the owner replies.
- Do not raise a rejected `id` without new evidence.
- Do not suggest optimizing or automating anything that steps 1 and 2 would remove.
- Do not pad. If you find fewer than 3 requirements worth questioning, say so and list fewer.
