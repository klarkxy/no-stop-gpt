# Less is more — frontend copy

Use when building an interface or reducing excessive interface text. A review
request stays read-only; an implementation request authorizes the scoped changes.
This concerns navigation, controls, and supporting copy, not shortening the user's
documents, articles, messages, or other primary content.

## Decide what earns screen space

- Start with the user's current task, meaningful state, and available actions.
  A heading or card does not automatically need a subtitle or description.
- Delete restatements, welcomes, generic benefits, obvious operating instructions,
  speculative warnings, and implementation details that do not affect a decision.
  Do not fill whitespace with prose or explain the design inside the product.
- Keep one clear statement of each fact in the current context. Use concrete
  labels such as "Export CSV" instead of a vague "Continue" plus an explanation.
- Add brief help beside a control only for a real ambiguity or prerequisite.
  Put optional background in existing, discoverable help or details. Delete useless
  copy rather than moving it wholesale into tooltips or new accordions.
- Show state-specific guidance when that state occurs: actionable errors after
  failure, relevant empty-state guidance when empty, and necessary consequences
  before commitment. Keep prerequisites visible before users need to act on them.
- Preserve text needed to distinguish choices, understand costs or data loss,
  enter valid input, and recover from failure. Keep persistent field labels and
  accessible names; placeholders, color, or hover-only hints are not substitutes.
- Preserve task-relevant data density and existing design conventions. Do not
  trade fewer words for cryptic icons, hidden actions, tiny text, or more navigation.

## Examples

| Overexplained | Prefer | Context |
| --- | --- | --- |
| Projects / "Here you can view and manage all your projects" | Projects | Delete the redundant subtitle. |
| "Click the button below to create your first project" + Create project | No projects yet + Create project | Empty state needs no operating tutorial. |
| "Your changes have been successfully saved" | Saved | Keep feedback without ceremony. |
| "Export the current data as a CSV file" + Export | Export CSV | Let the command name explain the action. |
| Delete / "Are you sure?" | Delete “Q3 report”? / This cannot be undone. | Keep the target and actual consequence when deletion is irreversible. |

## Check the result

Review the rendered screen when available, including narrow layouts and relevant
empty, loading, error, and destructive-action states. Check whether users can
identify the next action, distinguish choices, and understand the result without
reading explanatory paragraphs. Check keyboard and accessible labeling when
controls or help change. If only source or a mockup was reviewed, say so.

Compare removed duplication and unnecessary reading with any new ambiguity or
extra steps. Do not impose a universal word count or reduction percentage. Keep
copy whose removal makes the task harder, and preserve explicitly required content.

For design evidence and the limits of explanations for model verbosity, see
[Sources and further reading](sources.md#interface-copy-and-model-verbosity).
