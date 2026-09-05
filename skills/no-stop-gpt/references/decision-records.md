# Decision record cleanup

Use when requested documentation consolidation or an authorized cut affects a
design record. Preserve useful rationale and follow the repository's conventions;
[SKILL.md](../SKILL.md) governs scope and authorization.

## Find the current owner

Trace the relevant implementation, compatibility policy, newer decisions, and
inbound links. Consult configuration, schemas, or release evidence where needed.
Dates and titles help discovery but do not establish ownership.

Classify a record as current, partly displaced, or fully displaced according to
which contracts and useful rationale survive. Historical records may intentionally
be immutable: update current owners and links instead of rewriting frozen history.
A document's claim is evidence to reconcile, not automatic proof of a live consumer.

## Consolidate useful information

Before retiring a displaced record, carry forward unique rationale, alternatives,
consequences, known gaps, and conditions that could change the decision. Preserve
supported compatibility and independently useful warnings. Inventories dedicated
to retired implementation details need not become permanent design obligations.

Repair affected links, indexes, paired translations, and repository metadata as
part of the change. Keep separate records when they still own distinct behavior
or when merging would hide a meaningful historical decision.

## Match the artifact to the need

Use the existing decision mechanism. A durable proposal should explain the current
contract, evidence, proposed change, strongest reason to retain the design, material
consequences, and remaining uncertainty. A small actionable item may need only a
local TODO or issue under existing conventions, not a new architectural record.
Do not scatter speculative cleanup annotations through unrelated code.

For a small edit, describe the updated owner and material rationale in the response.
For broader consolidation, also identify retired or retained records and evidence
limits. Read-only Survey reports findings without creating files unless repository
edits were requested; authorized documentation work needs no additional approval
of its intermediate classification.
