---
type: workflow/framework guide: what to combine with ICM, and why
project: PTG (Practice-to-Game Lab), and reusable for client work
status: draft v0.1
updated: 2026-11-10
evidence: READ = abstract pulled from the PDF in this folder this session · ◻ = well-known practitioner resource, verify the current version
companions: 03_design/02_system-design.md (DDIA + APIV) · ICM playbook (E:\icmfolder\ICM-PLAYBOOK.md)
---

# Frameworks to Combine with ICM

## 0. The systems lens: each framework fixes a different part of the loop

ICM gives you **structure**: numbered stages, contracts, one agent, files as handoffs. Those papers are about the parts ICM leaves thin:

```
        SPEC ──► PHASES + HUMAN GATES ──► DO (derive / infer) ──► INDEPENDENT JUDGE ──► RECORD
         ▲                                                                              │
         └──── IMPROVE the context files ◄── ATTRIBUTE failures to a context source ◄───┘
```
| Loop part | ICM alone | Borrow from |
|---|---|---|
| What "done" means | Contract per stage | **Spec-Driven Development** |
| When a human steps in | "Read before the next stage" | **Phased Workflow** |
| Which steps are code vs. model | Implied | **Workflow as Knowledge** (derive vs. infer) |
| Who judges "done" | The same agent | **Humanize** (a separate judge) |
| What the loop needs to run safely | Not covered | **Loop Engineering** checklist |
| Why a bad output happened | Manual tracing | **TRACE** (attribution to a context source) |
| How context improves | You edit files | **Meta Context Engineering** (later, gated) |
| Files as the context layer, formalized | Folders | **Everything is Context** |
| When static stops being enough | Not covered | **Workflow optimization survey** |
| The vocabulary of context | Partial | **Context Engineering 2.0** |

---

## 1. The 10 papers you saved (READ: abstracts checked)

| Paper | What it actually says | Evidence type | Borrow for PTG / ICM |
|---|---|---|---|
| **Everything is Context: Agentic File System Abstraction** (CSIRO Data61 + ArcBlock) | "Everything is a file" for context: knowledge, memory, tools and human input mounted as files with **metadata and access control**, for traceability; built in the open-source AIGNE framework | Design paper, 8 pp | The closest cousin to ICM. Borrow **metadata + access rules on context files** (who may read/write each folder) |
| **Context Engineering 2.0** (SJTU/GAIR) | A definition and history of context engineering, from 1990s HCI to agents; design considerations | Conceptual, 28 pp | Vocabulary and the "why" behind loading only what each stage needs |
| **Spec-Driven Development for Agentic SE** (UPM Madrid) | A **productivity paradox**: individual devs speed up, but team throughput, review capacity and stability drop. SDD + a "harness" of mechanisms to govern agents | **Conceptual, mostly gray literature** (the authors say peer-reviewed evidence isn't established yet) | Each `CONTEXT.md` becomes a **spec with acceptance criteria**; the paradox = your review time is the bottleneck |
| **A Phased Workflow for Operating LLM Coding Agents** (Infobip, CIKM '26) | Four phases; **human effort front-loaded**, delegation grows as artifacts mature; context management at every phase. Upstream errors compound; **correcting generated code adds bloat and fragility** | **Practitioner report, 2 pp**, no metrics (they list that as an open problem) | Backs the APIV talk's "iteration makes slop" claim from a second, independent source (still anecdotal) |
| **Workflow as Knowledge: Semantic Persistence** | Workflows, runs, inference records and **context snapshots** as persistent, inspectable, resumable objects. Key distinction: **derive** (deterministic computation) vs. **infer** (LLM judgment under declared context and capability policy) | Conceptual, 39 pp; formal semantics left to future work | **Label every step DERIVE or INFER.** This is foundation P12 in a cleaner vocabulary. Snapshots = your event log |
| **Humanize: Judgement Engineering for Agentic Coding** | "The agent that writes the code is a weak judge of whether it's done." Human approves a **plan contract**; builder agent works in rounds; a **reviewer agent from another vendor** decides completion; **deterministic hooks, not a model**, route work and enforce 72 gates. A defect survives only if both models miss it | Deployment report + 118 public postmortems; large claimed results (self-reported) | **Separate builder from judge**, and route with code, not with a model. Matches your double-annotation and claim-audit design |
| **Loop Engineering: Building Blocks, Adoption, Impact** | The shift prompt → context → harness → **loop** (systems that prompt agents for you). Agreed building blocks: **triggers (schedule/events), machine-checkable stop conditions, persistent state files, verifier subagents, token budgets, defined escalation to humans.** Mining study: 217 repos with autonomous loops. **"No systematic evidence of the claimed benefits"** | Gray-lit review + mining study + experiment design | Use the building-block list as a **checklist** for any loop. Take the hype warning seriously |
| **TRACE: Trajectory Attribution for Automated Context Engineering** | Mines past agent runs for **implicit dissatisfaction signals** (corrections, rephrasing, abandonment) to find which context source failed (prompt, knowledge, tool description, skill); decides **CREATE** (gap) vs. **UPDATE** (stale), 96% operation accuracy on their benchmark | System + benchmark, 21 pp | When a mentor card is wrong, trace back: **which file caused it, and is it missing or stale?** Your adjudication log is the signal source |
| **Meta Context Engineering via Agentic Skill Evolution** (PKU) | A meta-agent **evolves the context-engineering skills themselves**; a base agent optimizes context files and code; 5.6–53.8% relative gains (mean 16.9%) over other agentic CE methods across 5 domains | Method + experiments, 46 pp | **Later, and gated.** Self-changing context conflicts with "rules change only by reviewed revision." Use it to *propose* edits, never auto-apply |
| **From Static Templates to Dynamic Runtime Graphs** (workflow optimization survey) | Sorts methods by **when structure is decided**: static (fixed scaffold) vs. dynamic (generated per run). Separates **reusable templates / realized graphs / execution traces** | Survey, 31 pp | Names your layers: ICM folders = **template**; one session folder = **realized graph**; event log = **trace**. Tells you when a fixed pipeline stops being enough |

✅ **Duplicates resolved (2026-11-10 restructure):** extra copies moved to `_archive/duplicates/`.

---

## 2. Practitioner frameworks (outside the papers) ◻

| Resource | What it is | Why look |
|---|---|---|
| **Anthropic, "Building Effective Agents"** (anthropic.com/research/building-effective-agents) | Workflows (prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer) vs. agents; start simple | The same position as ICM; the **evaluator-optimizer** pattern = Humanize's builder/judge |
| **Anthropic, "Effective context engineering for AI agents"** (anthropic.com/engineering) | Context as a finite budget; just-in-time retrieval; compaction; note-taking | The engineering version of "load only what this stage needs" |
| **Anthropic, Agent Skills** (✅ engineering post found earlier) | Progressive disclosure: name → body → resources | How each ICM stage's "skill" should be packaged |
| **12-Factor Agents** (github.com/humanlayer/12-factor-agents) | Principles for production agents: own your prompts, own your context window, small focused agents, contact humans with tools, stateless reducers | Pairs well with the event log (state outside the model) |
| **GitHub Spec Kit** (github.com/github/spec-kit) | Spec → plan → tasks → implement, as files and commands | A concrete, file-based SDD toolchain; compare with ICM stages |
| **Kiro (AWS) specs** | Requirements → design → tasks inside the IDE | Another SDD reference |
| **BMAD Method** | Role-based agent workflow with documents handed between roles | A heavier, multi-role alternative to compare against |
| **DSPy** (Stanford) | Prompts as programs, optimized against metrics | The "measured" version of Meta Context Engineering |
| **LangGraph** | Code-based graphs with checkpoints and human interrupts | Only if PTG ever needs branching or many concurrent runs |

---

## 3. The combination: "ICM+" for PTG (and for client work)

Keep ICM as the backbone. Add six things, each tied to a loop:

| # | Add | From | What changes in the folders |
|---|---|---|---|
| 1 | **Specs with acceptance criteria** | SDD, Spec Kit | Each `stages/NN/CONTEXT.md` gets `## Acceptance` with machine-checkable tests (e.g., "margin pass-1 vs. pass-2 within tolerance on ≥90%") linked to BR/FR IDs |
| 2 | **DERIVE / INFER labels per step** | Workflow as Knowledge | Every process step is tagged `DERIVE` (script, deterministic) or `INFER` (LLM, with declared inputs and allowed tools). INFER steps can never write numbers |
| 3 | **Phases with front-loaded human gates** | Phased Workflow, APIV | Gates sit where errors compound: event definitions, the hypothesis stage, card sign-off |
| 4 | **Independent judge, routed by code** | Humanize, evaluator-optimizer | Builder ≠ judge (a different model or a script). A deterministic gate decides whether work moves, never the builder's own "done" |
| 5 | **Loop checklist** | Loop Engineering | Any automated loop must declare: trigger · stop condition (machine-checkable) · state file · verifier · budget · escalation to you |
| 6 | **Attribution → reviewed edits** | TRACE, then MCE | Failures get traced to a context file and tagged CREATE/UPDATE; edits are **proposed**, then a human approves them as a versioned revision |

### What a stage contract looks like with ICM+ (format only)
```
# 03_hypothesis — one job: write ONE testable hypothesis
## Inputs        working: ../02_audit/output/… · reference: _shared/evidence-rules.md
## Do NOT load   cue library (biases the hypothesis)
## Steps
  1 DERIVE  pull latest game tags + practice profile (script)
  2 INFER   draft hypothesis + 3 competing explanations (LLM; may not invent numbers)
  3 DERIVE  check every number traces to a source file (script)
## Acceptance    (BR-2 / FR-2.1) all claims labeled · all numbers traced · ≥3 alternatives
## Judge         claim-audit script + you (you write your own hypothesis first)
## Loop          trigger: new audit file · stop: acceptance passes or 2 rounds · escalate: you
## On failure    log to adjudication → TRACE tag: CREATE or UPDATE which file
```

---

## 4. Reading order (about one paper a week)

1. **Anthropic, "Building Effective Agents"**: the baseline philosophy (short)
2. **Everything is Context** (8 pp): the closest to ICM
3. **Workflow as Knowledge** §1–3: the derive/infer distinction
4. **Humanize** (14 pp): builder vs. judge, routing by code
5. **Phased Workflow** (2 pp) + **Spec-Driven Development** (results section)
6. **Loop Engineering** (11 pp): the checklist, and the hype warning
7. **TRACE** (21 pp): debugging context
8. **Workflow optimization survey**: when to go beyond static
9. **Context Engineering 2.0**: background
10. **Meta Context Engineering**: last, once your contracts are stable

**After each one, write a 5-line evidence card** (library format) and answer: *does this change a principle, a contract, or nothing?*

---

## 5. Evidence check (honest)

- **Most of these are conceptual, practitioner reports or gray-literature reviews.** Loop Engineering explicitly found **no systematic evidence** for the claimed benefits. Spec-Driven Development says peer-reviewed evidence isn't established. The Phased Workflow paper has no metrics.
- Humanize and MCE report big numbers, mostly from their own deployments and benchmarks.
- **So treat them like grade-B/C coaching advice:** good patterns to test in your own workflow, not proven laws. Your judgment log and rehearsal data are how you find out what actually works for you.

## 6. Systems check
- **Leverage:** the separate judge (#5 rules) and derive/infer labels (#6 information) beat any tool choice (#12).
- **Trap to avoid:** framework collecting is the KNOW-without-DO trap again. Pick **one** addition (I'd start with #2 derive/infer labels, since it's free and sharpens every contract), use it on rehearsal-01's debrief, then add the next.
