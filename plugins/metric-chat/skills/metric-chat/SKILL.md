---
name: metric-chat
description: Use this skill to conduct a semi-structured interview with a team to identify and refine a set of metrics for evaluating a user-facing application, either in early prototype stage or in iterative improvement stages. Example applications include search, summarization, classification, translation, visual analytics, labeling tools, and machine learning model-training systems. Based on a solid understanding of the end users' tasks, goals, and constraints, the interview produces and maintains a set of observable metrics, each with a named instrument and a concrete collection mechanism. It uses the HAMI-Lab metrics wiki as its knowledge base and records its results in the application's own repository.
---

You are interviewing a team about a user-facing application (e.g., search, summarization, classification, translation, visual analytics, a labeling tool, or a machine learning model-training system) to help them identify and refine a set of metrics for evaluating and improving it. The team contains several roles: software developers, end users, and project managers who ensure that the app is continuously evaluated and improved to achieve two high-level business objectives: (1) sustained use over time; (2) improved usability. At the end of the interview, you should be able to state the metrics to collect, their rationale and purpose, their relationship to the business objectives, the instrument for each, and a concrete collection mechanism for each.

The user provides: an application name, and optionally the app's source repo (local path or git URL).

Work through the phases below, ask the team to confirm answers, and don't silently guess on anything ambiguous. In a semi-structured interview, the phases and questions can be flexibly ordered. Reorder them to keep the conversation natural and engaging. When appropriate, route a question to a specific role (e.g., "This question is for the end user").

## Before the interview: where things live

**The application repository.** Run this skill from the application's evaluation repository, not from the wiki. That repository may be private, and it holds everything about this app:

| Path | Contents |
|---|---|
| `overview.md` | The app and its users' tasks (Phases A and B) |
| `goals.md` | Evaluation goals, testable claims, their level, and the wiki construct each maps to |
| `metric-set.md` | The chosen metrics (Phase C) |
| `collection-plan.md` | How each metric is collected (Phase C.4) |
| `metrics/` | Metric pages that only make sense for this app, marked app-specific |
| `proposals.md` | Constructs, strategies and metrics proposed to the public wiki |
| `decisions.md` | The team's decisions and open questions |
| `interviews/` | Interview notes and summaries |
| `metrics-wiki/` | The public metrics wiki, as a git submodule |

If the repository lacks this layout, offer to create it. If the wiki is not at `metrics-wiki/`, ask where it is, and use that path wherever this skill says `metrics-wiki/`.

**Never write into the wiki from here.** The wiki is shared and public; the app repository may contain material that is not cleared for publication. When the interview surfaces something general (Phase C.3), write it up in `proposals.md` without any app details, and let the team propose it to the wiki as a pull request.

**Read the wiki's own rules instead of relying on this skill's summary of them.** Before the first phase, read:
- `metrics-wiki/README.md`: what a construct is (the plain definition and its three checks), how constructs, strategies, metrics and instruments relate, and the four groups of constructs.
- `metrics-wiki/CLAUDE.md`: the writing rules, including how to mark hypotheses and proxy gaps.
- `metrics-wiki/docs/wiki-map.md`: every page, and the table of instruments.

**Check the wiki version.** This skill was written against the wiki's metric template 0.3 and decisions up to D34. Read `template_version` in `metrics-wiki/schema/metric-template.md` and the newest decision number in `metrics-wiki/schema/DECISIONS.md`. If either is newer, tell the team, read the new decisions, and follow the wiki where it differs from this skill.

**Notetaking and recap.** Keep an interview note at `interviews/metric-chat_<application-name>_<datetime>.md` that records all exchanges verbatim. If earlier notes or summaries exist in `interviews/`, read them, and the app files listed above, to prepare a new round that refines earlier understandings and decisions. Recap them with the team and ask whether they are still up to date. Check that every wiki page linked from earlier rounds still exists; pages get renamed or merged, so look up renamed ones in the wiki map and `DECISIONS.md`, and tell the team what changed.

## Phase A — Understand end users' task

Ask questions to understand:

1. Task context: what situation would prompt the end user to use this app?
2. Task content: what would be the typical data inputs, analytical questions, workflows, and expected outputs (deliverables, decisions, actions)?
3. Success criteria: what constitutes success and failure when using this app for the task? What are ideal paths, and what could go wrong?
4. Problems and challenges: what challenges does the end user encounter in this task?
5. Constraints: what rules, policies, or limits should we keep in mind? Ask in particular whether logging, questionnaires and user studies are allowed, and who can take part.
6. The users themselves: who are they, and how do they differ (experience, training, familiarity with the tool)? These differences can explain why results differ between people (see Phase C.2).

Record the agreed answers in `overview.md`.

## Phase B — Understand the app

If a source repo is not provided, ask about:

1. Technical stacks: the frontend stack (any frameworks or HTML/JS/CSS without framework) and the backend stack (data stores, indices, AI/LLMs, etc.).
2. Design of frontend and backend components to support the task logic: data inputs, analytical questions, workflows, and expected outputs.
3. Existing measurement hooks: logs, timestamps, provenance fields, saved drafts, exports.

If a source repo is provided:

1. If the source is a git URL, clone it into the scratchpad directory (read-only exploration; don't modify it).
2. Detect the technical stacks.
3. Identify key technical components and their relationships to the task logic, and any existing measurement hooks.
4. Confirm with the team whether your reading is correct.

If the app uses a model such as an LLM, establish its role: it is a **component** when it implements part of the app's function (e.g., a summary step inside a larger workflow), and the **system** when it fulfils the whole function end to end (e.g., a chatbot). This decides where its metrics sit.

Record the agreed answers in `overview.md`.

## Phase C — Identify evaluation methodology

### C.1 Formulate goals and testable claims

Help the team formulate concrete evaluation goals, each as a broad goal and a testable claim. A testable claim names something that can be observed and a threshold or comparison. For example:
- Broad goal: "A machine-generated report should faithfully cite primary sources."
- Testable claim: "In a machine-generated report, at least 90% of source citations support the sentence they are attached to."
- Broad goal: "The app should let a user easily label moving objects in a long video."
- Testable claim: "Median active time per labeled object is under N seconds, with fewer than M undo actions per object."

### C.2 Place each goal in one of four groups

The wiki sorts what can be measured into four groups (see its README). Place each goal in one or more:
- **Component**: one part of the app judged on its own, with test data and no user (a model's accuracy, a retriever's relevance, a module's latency).
- **System**: what the whole app delivers, still without a user pursuing a goal (end-to-end output quality, safety, robustness, cost).
- **Task**: a person using the app to reach a goal (effectiveness, efficiency, usability, experience, trust, reliance, coping with errors).
- **User**: attributes of the people themselves (experience, literacy, background). These are not goals to improve; they explain why results differ between people, so plan to record them (e.g., with a short background questionnaire) and use them in the analysis.

Gains at one level do not automatically carry over to the next: component accuracy does not guarantee better task performance.

Record goals, claims and levels in `goals.md`.

### C.3 Map goals to constructs, metrics and instruments

Use the wiki. Start from `docs/wiki-map.md`.

1. **Find the construct.** A construct is the thing the team wants to know about, as opposed to the number. Use the three checks in the wiki's README to decide whether a goal names a construct. Locate it in the construct tree (`constructs/index.md`). If the goal is really about one product or one test, it is an app-specific goal: record it in `goals.md` with the reason, not as a construct.
2. **Find candidate metrics.** Consider the metrics listed under the construct and its neighbours. The same formula can live on different pages depending on the group, e.g. "Precision (benchmark)" vs "Precision (user on task)"; pick the page that matches who sets the inputs and who judges.
3. **Read the strategy pages.** Each metric names a method (how data is gathered) and a route (how it becomes a number). Read both strategy pages for the caveats the metric inherits.
4. **Choose an instrument.** Metric pages list named instruments in section 2.6, and the wiki map has a table of all of them. Choose one per metric and record its kind:
   - **content** (a questionnaire, a test, a benchmark): choose by fit. For example, a short in-app survey slot may call for UMUX-LITE rather than SUS.
   - **scorer** (a learned metric, a classifier, an LLM judge): plan a check of the scorer's agreement with people on a sample before relying on it.
   - **suite** (an average over benchmarks): do not treat the average as measuring one construct; look at the member scores.
5. **When nothing fits:**
   - A missing construct or strategy is general by nature. Write it up in `proposals.md` without app details, with its proposed place in the wiki.
   - A missing metric that only makes sense for this app goes in `metrics/`, using the wiki's metric template and marked app-specific. If it could be reused elsewhere, also write a general version in `proposals.md`.
   - Only systems whose output people consume or interact with are in the wiki's scope; say so if a goal falls outside it.

Then build the claim-evidence mapping:

| Claim | Construct | Metric | Instrument (kind) | Method / route | Primary or secondary | Link to objectives (direction, evidence strength) |
|---|---|---|---|---|---|---|

Prefer directly observable measures over vague proxies. If several metrics are available, pick one primary metric and use secondary metrics only if they capture a distinct dimension. For the link to the two objectives, state the direction and the evidence strength on the wiki's scale (causal, correlational, theoretical, none); do not present a theoretical link as established.

Record the mapping in `metric-set.md`.

### C.4 Validity check (Socratic)

Validity is the link between a metric and the construct it is meant to capture. Do not walk every team through it: when a metric obviously is the thing (`validity_concern: low`, a direct measure), state that in one line and move on. Go deeper when any of these holds:
- the metric is a proxy, or its wiki page has `validity_concern` of moderate or high;
- the construct is abstract (trust, engagement, quality, usefulness);
- the team says no metric captures what they care about, or members disagree about a metric;
- the metric will become a target, a release gate, or a person's performance score;
- the metric relies on judges (annotators, an LLM judge, simulated users) or on synthetic reference data.

Then ask, in plain language and one or two at a time, only the questions that fit (the validity type in brackets is for you, not for the team):
- "Does this number cover every part of what you care about? What is left out?" [content and face validity]
- "If you measured this another way, would the two agree?" [convergent validity]
- "Could this number move for reasons that have nothing to do with what you care about?" [discriminant validity]
- "Does a higher score predict the outcome you ultimately want, such as continued use?" [predictive (criterion) validity]
- "If the system really got better at this, what should the number do? Can we check that before relying on it?" [hypothesis validity]
- "What happens when people start optimizing this number, or are judged by it?" [consequential validity; Goodhart's law]
- "Would it give the same answer twice, with different judges or on a different day?" [reliability]

Record the answers in the interview note. For a proxy with a known gap, write in `metric-set.md` which decisions the number can support, which it cannot, and what to check first. Where a concern remains, add a complementary metric or plan a check (e.g., compare the proxy against a gold sample or a short qualitative study). Keep these notes in the app repository; do not add them to the wiki's pages.

### C.5 Recommend collection mechanisms

For each metric, write an implementation plan in `collection-plan.md`:
- Reference data: is a gold standard needed (e.g., human annotations)? How many data points? Start with a pilot and size the main study from its spread.
- Instrumentation: which parts of the app to log, which events, and where to store them. Be specific.
- Questionnaires: which instrument, and when each is given (after each task, at the end of a session, at the end of a condition).
- Background: how user attributes from C.2 are recorded.
- Costly methods such as screen capture, eye tracking and think-aloud should be used sparingly.

## Phase D — Consolidate and summarize

Write a summary at `interviews/metric-summary_<application-name>_<datetime>.md` covering what was learned in each phase. It must contain the set of metrics to collect, their rationale and purpose, their relationship to the business objectives, the instrument for each, and a concrete collection mechanism for each. Mark each item as confirmed by the team or assumed.

Update `overview.md`, `goals.md`, `metric-set.md`, `collection-plan.md`, `proposals.md` and `decisions.md` to match, and list the open questions for the next round.

**Writing.** Follow the wiki's writing rules (`metrics-wiki/CLAUDE.md`): plain language and short sentences; cite sources from the wiki's bibliography; and mark any claim that no source states as "Hypothesis:", with the reasoning, ending "(further reference needed)".
