---
name: grill-into-patch
description: Grill the user until a change to a GitHub repository is fully determined, then produce a verified changes.patch against the latest state of the user-specified target branch and package it in a zip file.
---

# Grill into Patch

Use this skill for a ChatGPT conversation whose purpose is to decide and deliver changes to the canonical source in a GitHub repository.

## Target

The user specifies the target repository and target branch.

If the repository is already uniquely identified by the conversation, do not ask for it again.
Never infer the target branch from a local or checked-out branch.
Use only the branch specified by the user.

Treat the latest state of the target branch as the canonical source.
Keep tracking that latest state throughout the session.

## Grill

Resolve every user decision needed to make the intended repository change unambiguous; after each user response, reconcile currently effective decisions and continue with the next eligible unresolved question until the grill is complete.

Map decisions by dependency.
Ask only questions whose prerequisites are already settled.
Do not ask the user for facts that can be determined from the repository, GitHub, available tools, or other accessible sources.

Number grill questions sequentially as `Q1`, `Q2`, and so on.
Restart numbering at `Q1` for each chat.
Give a recommended answer with every question.

Present answer choices for every grill question.
If the decision can be fully answered as yes or no, label the choices `Y` and `N`.
Otherwise, label the choices `A`, `B`, `C`, and so on as needed.
Do not use any other choice-label scheme.

Write every choice so its meaning is clear from the choice text alone.
For `Y` and `N` choices in particular, avoid abstract wording such as "do so" or "do not do so".
State the concrete action or outcome in each choice, such as "Update the README to document the behavior" or "Leave the README unchanged".
The recommended answer must identify one of the presented choice labels.

A decision is settled when the conversation leaves only one valid conclusion.
Do not require an explicit confirmation phrase when the conclusion is already unique.

Later corrections, withdrawals, and replacements override earlier statements.
Only the currently effective decisions count.

If a newer target-branch state can be followed without changing the meaning of a settled decision, adapt to it autonomously.
If following the newer state could change the meaning of a settled decision, continue grilling.

Include ancillary changes that are required to make settled decisions correct and internally consistent.
If an ancillary change requires a new user decision, continue grilling instead of choosing silently.

The grill is complete when no unresolved user decisions remain.
At that point, proceed directly to artifact generation unless the user explicitly asked not to generate the artifact yet.

## Build the patch

Refresh the target branch before generating the final patch.

Construct the change from the latest target-branch state, not from an older conversational snapshot.

The resulting patch must:

- contain every currently effective decision from the chat;
- contain all required ancillary changes;
- exclude withdrawn, superseded, or rejected decisions;
- exclude unrelated changes;
- be applicable with `git apply`;
- be written as `changes.patch`.

Before packaging, compare the complete set of effective chat decisions with the patch result and ensure none are missing.

## Verify

Verification against the latest target branch is mandatory.

Run the equivalent of:

```bash
git apply --check changes.patch
```

against a clean copy of the current latest target branch.

Run repository-provided tests, lint, or build checks when the environment and documented project workflow make them available.
If those checks reveal a problem caused by the proposed change, continue correcting the change.

After verification, confirm that the target branch still points to the same latest revision used for verification.
If it advanced, rebuild and reverify the patch against the new latest revision.

Do not treat the artifact as complete until the mandatory patch applicability verification has actually succeeded.

## Package

Create a zip file with any suitable filename.

The zip root must contain:

```text
changes.patch
```

Return the zip file as the final artifact.
