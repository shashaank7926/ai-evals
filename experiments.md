# Experiments log

## Summary

| # | Experiment | Area | Status |
|---|---|---|---|
| 1 | Langfuse exploration | system | done |
| 2 | Trace pulls, 4 flows | IEP, chat, actions, proactive | done |
| 3 | Synthetic golden datasets | IEP | done |
| 4 | Langfuse evaluator designs | IEP, chat, actions | designed, not stored as results |
| 5 | First LLM judge on IEP | IEP | done |
| 6 | IEP rewrite issue review | IEP | done, 12 issues open |
| 7 | Diff-first gate evaluator | IEP | done |
| 8 | Strict gate-judge prompts | IEP | only the card judge was used; no saved results for the rest |
| 9 | Sound-alike gate study | IEP | done |
| 10 | Prompt v7 / v8 / v9 × 2 models | IEP | done, directional |
| 11 | Phonetic sound-alike check | IEP | done |
| 12 | Prompt v7_1 / v7_2 | IEP | done |
| 13 | Entity digest edge replay | entity digest | done |
| 14 | How bad entities and aliases enter | entities | done |
| 15 | Convergence gate | entities | prototype, ready for the backend |
| 16 | Entity/relationship classifier | entities | baselines done, Jev not run |

---

## System mapping

### 1. Langfuse exploration
`sessions.md`, `flow.md`, `possible_traces.json`
- 606 sessions, 5 pipelines, the model behind each, and trace counts per flow.
- At the time, Langfuse had no score configs, no datasets and no scores.
- Planning notes from the same period: `eval_criteria.md`, `metrics.md`, `interaction.md` and `issues.md` (issues spotted by hand).

### 2. Trace pulls
`IEP/`, `chat_response/`, `actions_crud/`, `proactive_tools/`
- The 5 newest traces for each flow (Sep 11), rendered as markdown, each with a `dataset_v1/` builder.

### 3. Synthetic golden datasets
`tcs.py`, `tcs1.py`, `tcs_bad.py`
- Generate synthetic transcripts with expected outputs, plus broken transcripts that should come back as `DATA_ERROR`.
- Output: `golden_dataset*.csv`, `malformed_transcripts_only.csv`.

### 4. Langfuse evaluator designs
`evaluators_v1/`
- Judge prompts for the IEP rewrite (`transcript_faithfulness`, …), chat_response (`context_groundedness`, …) and actions_crud (`action_extraction_completeness`, …).

---

## IEP rewrite

### 5. First LLM judge
`IEP/dataset_v1/judge_transcripts.py`
- 16 generations, scored 0–1 on two things: faithful to the raw transcript, and card/entities grounded in it.
- Mean score **0.64** (range 0.30–1.00).

### 6. Issue review
`reports/IEP_2026-09-28.md`
- 84 generations (production, staging and experiment runs), each issue confirmed by diffing raw against rewritten text.
- 12 issues. The worst is **T-001**: in long transcripts, a line gets the next line's text and the shift carries on to the end (11 traces).
- Then: text moved between lines (15 traces), ordinary words "fixed" into other words, and speaker tokens written into the speech.

### 7. Diff-first gate evaluator
`IEP/gate_evals/`
- Code extracts each edit and decides the mechanical cases. An LLM judges only name changes and entity convergences.
- 62 hand-labelled reference pairs, for checking any judge before trusting it.
- Against the old Langfuse judges on 31 traces (average score gap): sound-alike 0.20, all-caps **0.54**, form-only 0.15. The Langfuse sound-alike judge scored 14 traces that had nothing to judge.

### 8. Strict gate-judge prompts
`IEP/gate_evals/strict/`
- Rewritten Langfuse judges: sound-alike, all-caps, form-only, evidence-order and card. `run_strict.py` runs them offline.
- Only `card_gate.txt` has been used (by #12). The others have no saved results.

### 9. Sound-alike gate study
`IEP/sound_alike_gate_report.md`
- Data: discarded edits from 3,966 staging scenes.
- The gate mostly blocks real overcorrections ("data" → Audria, "pretty" → Pradeep). Real fixes are lost in three narrow places:
  - **F1, a bug:** punctuation-only edits are rejected (108 edits).
  - **F2:** split words and spelled-out acronyms ("Chat G BT" → ChatGPT, 23 edits).
  - **F3:** true sound-alikes that aren't spelled alike ("clot" → Claude, "author" → Otter).

### 10. Prompt v7 / v8 / v9 × gemini-3.5-flash-lite / gemini-3.7-flash
`IEP_experiments/results/report.md`
- 5 scenes, scored through the production gate code; cost about $0.64.
- Results are directional only: run-to-run noise is about ±5 repairs, and the judge matches hand labels only 74% of the time.

| Finding | Result |
|---|---|
| v9 (list only changed lines, stricter rules) | 45% cheaper and about 2× faster; 93% of edits pass the gate (v7: 77%); but fewer good fixes (gemini-3.7-flash: 24 against 82) |
| v8 (world-knowledge fix) | Didn't produce its intended effect in this sample; made flash-lite unstable (20 bad swaps, 17 speaker tokens in the text). Not ready to ship. |
| gemini-3.7-flash vs flash-lite | Edits more in both directions (82 good / 20 bad against 10 / 3); 1.7× the cost |
| Gate leaks | "eye" → "I" is blocked; punctuation-only edits are rejected (F1) |

### 11. Phonetic sound-alike check
`IEP_experiments/backend/gate_phonetic.py`
- Rule: pass if the consonant patterns match exactly **and** the target is a known entity.
- On 107 real outputs it changed no outcomes.
- On the staging pairs it recovers 5 of the 10 real errors the current rule blocks; one new false pass (cheer → Jira).

### 12. Prompt v7_1 (name rules) and v7_2 (memory-card rules)
`IEP_experiments/prompts/`, `results/`
- On the 18-scene targeted set, v7_1 against v7:
  - entity convergences 64 → 33;
  - name violations 41 → 25;
  - bad evidence quotes 60 → 39.
- Card eval: v7, v7_1 and v7_2 all fail the strict card judge on 16–18 of the 18 scenes. Word limits and structure are the main failures, and v7_2 doesn't fix them.

---

## Entities

### 13. Entity digest edge replay
`entity_digest/entity_digest_issues_report.md`, `replay_gate.py`
- 51 digests and 63 edges, replayed through server checks 1–5. Check 6 needs the production database, so it isn't replayed.
- 57% of edges restate existing ones; 28 cite the example tag copied from the prompt.
- The "no world knowledge" rule isn't enforced on the server (`Apple PRODUCES iOS`).
- The Langfuse evals never look at edges: 16 of the 19 digests with a clearly wrong edge scored at least 0.9.

### 14. How bad entities and aliases enter
`how_bad_entities_enter.md`, `iep_known_issues.md`
- The loop: IEP maps a word onto a known name → the resolver merges it → `_merge_entity` stores the heard word as an alias, unchecked → the next IEP call sees that alias as a known name and repeats the mistake.
- Evidence: 14,426 staging entity rows; 120 IEP traces. Examples: Audria has 37 aliases (including `limitless`, `app`, `WhatsApp`); Peyton is stored under Audrey; GR under Jira; India under US.

### 15. Convergence gate
`entity_digest/alias_gate/`
- One check for every entity type: may a heard name be recorded as an existing name?
- It runs in three places:
  - **G1:** in the IEP validator, before an entity is accepted;
  - **G2:** when the resolver picks which existing row to merge into;
  - **G3:** when deciding whether to store the heard name as an alias (it replaces the unchecked append).
- `alias_gate.py` is the check on its own; it gives the same decision as the full module on all 1,965 aliases. 109 tests pass.
- Dry run on 1,965 staging aliases: 20% rejected (396), 51% pending (1,008), 9% confirmed (184).
- In a hand check of 50 rejects and 50 keeps: about 9 legitimate aliases were wrongly rejected (4 fixed since), and about 4 bad ones were wrongly kept.

### 16. Entity/relationship classifier
`entity_jev/results/summary.md`
- 4 tasks. The gold labels have **not** been reviewed by a second person, and the rules (B1) were written after reading the items, so B1's numbers are optimistic.

| Task | Today (B0) | Rules (B1) | gemini-3.7-flash |
|---|---|---|---|
| T1 alias admission: accuracy / bad items let in | 54% / 90% | 81% / 13% | 91% / 14% |
| T3 relationships: accuracy / bad items let in | 44% / 65% | 73% / 26% | 78% / 22% |

- The Jev runs (typesafe.ai) are built but haven't been run.
