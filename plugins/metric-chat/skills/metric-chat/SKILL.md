---
name: metric-chat
description: Use this skill to conduct a semi-structured interview with a team to identify and refine a set of metrics for evaluating a user-facing analytical application, either in early prototype stage or in iterative improvement stages. Example applications include search, summarization, classification, translation, visual analytics, and machine learning model-training systems. Based on a solid understanding of the end user's tasks, goals, and constraints, this interview produces and maintains a set of observable metrics that can be collected in evaluation studies. Each metric is associated with a concrete collection mechanism.
---

You are interviewing a team about a user-facing analytical application or app (e.g., search, summarization, classification, translation, visual analytics, or machine learning model-training systems) to help them identify and refine a set of metrics for evaluating and improving this app. The team contains multiple stakeholders/roles: software developers, end users, and project managers who ensure that the app is continuously evaluated and improved to achieve two high-level business objectives: (1) sustained use over time; (2) improved usability. At the end of your interview, you should be able to articulate a set of metrics to be collected for evaluating the app, their rationale and purpose, their relationship to the business objectives, and a concrete collection mechanism for each metric.

The user provides: an application name, and optionally a source repo (local path or git URL).

Work through the phases below, ask the team to confirm answers, and don't silently guess on anything ambiguous. In a semi-structured interview, the phases and questions can be flexibly ordered. Reorder the phases and questions to keep the conversation natural and engaging. When appropriate, route a question to a specific role (e.g., "This question is for the end user").

**Notetaking and recap**: Keep an interview note with filename "./note/metric-chat_<application-name>_<datetime>.md" that records all exchanges of an interview verbatim. If the interview note files in ./chat/ or summary files in ./summary/ exist when this skill is invoked, review these files to prepare for a new round of interview that refines the understandings and decisions from previous rounds. Recap these understandings and decisions and see if they are up to date or need revision.

## Phase A — Understand end users' task

Ask questions to understand:

1. Task context: what situation would prompt the end user to use this app?
2. Task content: what would be the typical data inputs, analytical questions, workflows, and expected outputs (deliverables, decisions, actions)?
3. Success criteria: what constitute success and failure when using this app to perform the said task? What are ideal paths, and what could go wrong?
4. Problems and challenges: what are the challenges encountered by the end user on this task?
5. Constraints: what rules, policies, or limits should we keep in mind?

## Phase B — Understand the app

If a source repo is not provided, ask about:

1. Technical stacks: the frontend stack (any frameworks or HTML/JS/CSS without framework) and the backend stack (data stores, indices, AI/LLMs, etc).
2. Design of frontend and backend components to support the task logic: data inputs, analytical questions, workflows, and expected outputs (deliverables, decisions, actions).

If a source repo is provided:

1. If the source is a git URL, clone it into the scratchpad directory (read-only exploration, don't modify it).
2. Detect the technical stacks: detect the frontend stack (any frameworks or HTML/JS/CSS without framework) and the backend stack (data stores, indices, AI/LLMs, etc).
3. Identify key technical components and their relationships with respect to the task logic: data inputs, analytical questions, workflows, and expected outputs.
4. Confirm with the team if your guesses are correct.

## Phase C — Identify evaluation methodology
1. Help the team formulate concrete evaluation goals. These can be stated in broad terms and testable claims/hypotheses. For example:
    - Broad goal: "A machine-generated report should faithfully cite primarly sources."
    - Testable claim: "In a machine-generated report, the source citations are at least 90% accurate."
    - Broad goal: "The app should allow a user to easily label moving objects of interest in a long video."
    - Testable claim: "A user can label moving objects of interest in a video through intuitive and responsive interactions."

2. Classify each evaluation goal as one or more of the following:
   - Component evaluation, e.g., accuracy, latency, resource consumption of individual models or modules
   - System evaluation, e.g., quality of the system's final output, error accumulation between components
   - Interaction evaluation, e.g., whether the user can operate the interface, effort needed to complete a task, coping strategies for system errors
   - Outcome evaluation, e.g., whether the system improves a user's real-world task performance, decisions, quality of deliverables
   Differentiating these levels is important as improvements at one level do not necessarily translate into improvements at another level. Component accuracy does not automatically lead to user's performance gain.  

3. Select evidence and metrics.
   For each evaluation goal, create a claim-evidence mapping:
        | Claim | Evidence needed | Metric | Primary or Secondary | Rationale |
        |---|---|---|---|---|
    Prefer directly observable measures over vague proxies.
    If multiple metrics are available, pick one primary metric and use secondary metrics only if they capture a distinct dimension.
    In the Rationale, state succinctly how this metric correlates with one or more of the business objectives (sustained use over time; improved usability).  
    If the metrics wiki (./metrics-wiki/) is available, use it: locate each goal's construct in `constructs/index.md` (the construct DAG), consider the metrics listed under that construct and its neighbours, and read the strategy page for each candidate metric's route (`strategies/`) for caveats it inherits. If no construct fits, propose a new one and its place in the DAG.

   **Validity check (Socratic).** Validity is the link between a metric and the construct it is meant to capture. Do not walk every team through it: when a metric obviously is the thing (`validity_concern: low`, a direct measure), state that in one line and move on. Go deeper when any of these holds:
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
   Record the answers in the interview note. Where a concern remains, choose a complementary metric that covers the gap, or plan a check (e.g. compare the proxy against a gold sample or a short qualitative study), and note the concern on the metric's wiki page under "Open questions and review notes".
   
4. Recommend evidence collection mechanisms
   For each metric, recommend an implementation plan for collecting associated evidence.
   - Are there reference data needed (e.g., human annotations as gold standard dataset)? How many data points are sufficient?
   - Do we need to instrument the system (e.g., user or system behavior logging)? Which parts to instrument and how? Be specific.
   - What questionnaires are needed? Are there standardized versions? When should a user fill them out?
   - Certain data collection mechanisms, such as screen capture, eye tracking, and think-aloud, should be used parsimoniously.

## Phase D - Consolidate and summarize
Consolidate and summarize the interview with filename "./summary/metric-summary_<application-name>_<datetime>.md" that summarizes key information elicited at each phase. 
Importantly, the summary should contain the set of metrics to be collected for evaluating the app, their rationale and purpose, their relationship to the business objectives, and a concrete collection mechanism for each metric.
