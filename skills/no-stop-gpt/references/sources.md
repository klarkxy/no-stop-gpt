# Sources and further reading

Canonical material behind the principles in this skill. These inform judgment;
the skill text owns the operative rules.

## Core design and simplification

- Kent Beck, Extreme Programming — YAGNI: implement only what current
  requirements demand; design for today's needs rather than imagined ones.
- John Ousterhout, *A Philosophy of Software Design*
  (<https://web.stanford.edu/~ouster/cgi-bin/book.php>) — complexity as
  dependencies plus obscurity; deep modules behind simple interfaces; tactical
  versus strategic programming; defining errors out of existence.
- Sandi Metz, *The Wrong Abstraction*
  (<https://sandimetz.com/blog/2016/1/20/the-wrong-abstraction>) — duplication
  is cheaper than the wrong abstraction; the rollback procedure of inlining
  back into callers and re-extracting from visible duplication.
- Artem Zakirullin, *Cognitive Load Is What Matters*
  (<https://github.com/zakirullin/cognitive-load>) — working-memory framing of
  complexity, the cost of many shallow modules, and why familiarity is not
  simplicity.
- Hyrum's Law (<https://www.hyrumslaw.com/>) — with enough consumers, every
  observable behavior of a system becomes depended on, documented or not.
- Dan McKinley, *Choose Boring Technology*
  (<https://mcfunley.com/choose-boring-technology>) — innovation tokens: spend
  a limited novelty budget where it pays and default to proven infrastructure.
- Chesterton's fence — history can explain a mechanism's purpose. Consult it
  when useful; this skill does not make complete historical understanding a
  prerequisite for a recoverable deletion attempt.
- Joe Armstrong and the Erlang/OTP tradition, "let it crash"
  (<https://www.erlang.org/doc/system/design_principles.html>) — workers carry
  only the happy path and fail visibly inside an isolated unit; supervisors
  restart from known-good state. Recovery replaces defensive code, not errors.
- Hunt and Thomas, *The Pragmatic Programmer*, "Crash, Don't Trash"
  (<https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/>)
  — crashing early beats continuing on corrupted state.
- Codersera, *Stop Claude Code From Over-Engineering Your Code*
  (<https://codersera.com/blog/how-to-stop-claude-code-over-engineering-2026/>)
  — practitioner catalog of agent elaboration tells; its explanations of model
  motives are anecdotal, not measured.
- Michael Feathers, *Working Effectively with Legacy Code* — legacy code is
  code without tests; pin current behavior with characterization tests at a
  seam, then change boldly.
- Martin Fowler, *Refactoring*, and the strangler fig pattern
  (<https://martinfowler.com/bliki/StranglerFigApplication.html>) — large
  refactorings as sequences of behavior-preserving steps; build the new path
  alongside, migrate consumers, then delete the old. See also his preparatory
  refactoring example
  (<https://martinfowler.com/articles/preparatory-refactoring-example.html>):
  make the change easy, then make the easy change.
- Ellnestam and Brolund, *The Mikado Method*
  (<https://www.mikadomethod.info/>) — attempt the change, record the
  prerequisites that block it, revert, and resolve prerequisites first.
- Ken Thompson — "One of my most productive days was throwing away 1000 lines
  of code." Code is a liability; deletion is progress.
- Anthropic's code-simplifier plugin
  (<https://github.com/anthropics/claude-plugins-official/tree/main/plugins/code-simplifier>)
  and Addy Osmani's code-simplification skill
  (<https://github.com/addyosmani/agent-skills/blob/main/skills/code-simplification/SKILL.md>)
  — behavior preservation, project conventions, scoped incremental passes, and
  separating cleanup commits from feature work.

## Task completion and self-contained delivery

Read on 2026-11-18 from [Using GPT-6.2](https://developers.openai.com/api/docs/guides/latest-model).
Task completion and self-contained delivery principles in [SKILL.md](../SKILL.md)
were adapted from that guide.

[SKILL.md](../SKILL.md) owns the operative rules. This note records the adoption
boundaries: vendor model names and reasoning-effort settings stay out. Frontend
copy remains [Less is more](less-is-more.md); imagery and typography notes on
the page were not added. Existing reuse, readable control flow, and abstraction
rules already covered the coding prompt. The guide's suggestion to skip new
tests is not a ban on tests the repository, the user, or a specific regression
requires. Examples on the page do not add authority. Continued work still ends at
the observable result; it does not forbid waiting, and it does not justify
repeating a failed check without a new reason.

## Legacy code deletion

Reviewed 2026-10-07. The operative workflow is
[legacy-cleanup.md](legacy-cleanup.md). These are source-reading references, not
installed dependencies or a measured ranking. The cut-and-repair default for
locally checkable paths is this skill's policy; the sources contribute specific
techniques.

- Knip, [Your First Cleanup](https://knip.dev/overview/first-cleanup) — distinguish
  dead files from missing entrypoint configuration, separate removing an export
  from deleting its implementation, and rescan as cuts expose more dead code.
- Uber, [PolyglotPiranha](https://github.com/uber/piranha) — start from a retired
  flag's final behavior and propagate simplifications through surrounding code.
- Meta, [Automating dead code cleanup](https://engineering.fb.com/2023/10/24/data-infrastructure/automating-dead-code-cleanup/)
  — remove unreachable groups, including cycles; account for dynamic and
  cross-language entrypoints. A small cleanup needs no SCARF-like infrastructure.
- [kanon-repo design-cleanup](https://github.com/gjkim42/kanon-repo/blob/main/skills/design-cleanup/SKILL.md)
  — follow abandoned designs into code, names, tests, and documentation. Preserve
  immutable history rather than adopting its blanket rewrite of design records.
- [agent-lsp lsp-dead-code](https://github.com/blackwell-systems/agent-lsp/blob/main/skills/lsp-dead-code/SKILL.md)
  — sanity-check empty reference results with a known active symbol and inspect
  registration paths. Neither one successful query nor a reference count proves
  complete indexing or usefulness.
- [oh-my-openagent remove-deadcode](https://github.com/code-yeongyu/oh-my-openagent/blob/dev/.agents/skills/remove-deadcode/SKILL.md)
  — batch cuts and verify the combined result. Do not adopt mandatory parallelism,
  zero-reference certainty, automatic commits, or arbitrary candidate-count stops.
- Hacker News discussions of [Sensenmann](https://news.ycombinator.com/item?id=35755841)
  and [ts-remove-unused](https://news.ycombinator.com/item?id=41554014) — practitioner
  reports highlight low-frequency business paths, dynamic imports, and test-only
  references. These motivate targeted checks, not a blanket veto on deletion.

## Interface copy and model verbosity

Reviewed 2026-09-16. The operative frontend rule is [Less is more](less-is-more.md).

- Nielsen Norman Group, [Aesthetic and Minimalist Design](https://www.nngroup.com/articles/aesthetic-minimalist-design/)
  — irrelevant information competes with useful information. Preserve everything
  necessary for the task; visual emptiness alone is not usability.
- GOV.UK, [Writing for user interfaces](https://www.gov.uk/service-manual/design/writing-for-user-interfaces)
  — people scan; start with less copy, add help for demonstrated needs, and fix
  interactions that require explanations of how to operate them.
- OpenAI, [Frontend prompt instructions](https://developers.openai.com/api/docs/guides/frontend-prompt)
  — audience and domain should determine on-screen text and layout; operational
  tools should support scanning and repeated action with restrained presentation.
  It also explicitly discourages visible in-app explanations of features and use.
- Park et al., ACL 2024, [Disentangling Length from Quality in Direct Preference Optimization](https://aclanthology.org/2024.findings-acl.297/)
  — experiments show preference optimization can exploit length rather than
  quality. This supports a possible mechanism, not a diagnosis of current GPT UI output.
- Zhang et al., ACL 2025, [From Lists to Emojis: How Format Bias Affects Model Alignment](https://aclanthology.org/2025.acl-long.1308/)
  — preference models can favor verbosity and formatting independently of quality.

A plausible frontend failure is carrying an assistant's explanatory answer style
into product copy, treating every requirement as something to announce on screen,
or compensating for unclear controls with more prose. These are design hypotheses,
not established facts about a specific GPT model's training or motives. General
verbosity research does not demonstrate why any particular screen became wordy.
Constrain the generated UI copy directly; a shorter chat response alone does not
establish a more concise interface.
