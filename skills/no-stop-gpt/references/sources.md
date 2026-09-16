# Sources and further reading

Canonical material behind the principles in this skill. These inform judgment;
the skill text owns the operative rules.

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
- Chesterton's fence — do not remove a mechanism until you understand why it
  exists; blame and commit history are the first evidence to consult.
- Joe Armstrong and the Erlang/OTP tradition, "let it crash"
  (<https://www.erlang.org/doc/system/design_principles.html>) — workers carry
  only the happy path and fail visibly inside an isolated unit; supervisors
  restart from known-good state. Recovery replaces defensive code, not errors.
- Hunt and Thomas, *The Pragmatic Programmer*, "Crash, Don't Trash"
  (<https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/>)
  — crashing early beats continuing on corrupted state.
- Codersera, *Stop Claude Code From Over-Engineering Your Code*
  (<https://codersera.com/blog/how-to-stop-claude-code-over-engineering-2026/>)
  — catalog of agent elaboration tells and why models hedge with unrequested
  code.
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
