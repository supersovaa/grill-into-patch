# grill-into-patch

`grill-into-patch` is a ChatGPT-oriented skill for turning a repository-change conversation into a verified Git patch.
It is based on the core idea of the open-source `grilling` skill: resolve decisions through focused questioning instead of silently guessing.

## Contract

The user specifies a GitHub repository and target branch.

The skill then:

1. follows the latest state of that branch;
2. grills only unresolved user decisions;
3. treats a conclusion as settled once the conversation makes it unique;
4. keeps only the latest effective decisions when earlier statements are corrected or withdrawn;
5. includes all required supporting changes;
6. generates `changes.patch` against the latest target branch;
7. verifies that the patch is applicable with `git apply --check`;
8. redoes the patch if the target branch advances before verification completes;
9. packages `changes.patch` at the root of a zip file.

The patch must reflect every effective decision from the chat and no unrelated changes.

## Grill questions

Questions are numbered from `Q1` in every chat and continue sequentially.
Each question includes a recommended answer.
Facts that ChatGPT can determine from the repository or available tools are not user questions.

## Output

The final deliverable is a zip file of any suitable name:

```text
<name>.zip
└── changes.patch
```

`changes.patch` must apply to the latest verified state of the user-specified target branch.

## Origin

This skill is derived from the ideas and structure of Matt Pocock's `grilling` skill from the `mattpocock/skills` repository.
The upstream project is licensed under the MIT License.
