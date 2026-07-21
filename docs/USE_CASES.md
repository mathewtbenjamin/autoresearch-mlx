# Autoresearch Use Cases

This document ranks potential uses of Autoresearch for Mathew Benjamin's personal and Johari Labs work.

The rankings prioritize:

1. **Leverage** — how much durable value the use case could create.
2. **Measurability** — whether an automatic evaluator can distinguish better from worse.
3. **Readiness** — whether the required data, tests, and workflows already exist.

## Usage modes

- **Direct** — the current MLX repository already supports the use case.
- **Adapt** — reuse the Autoresearch pattern with a purpose-built evaluator and experiment runner.

The reusable pattern is:

```text
Establish baseline
       ↓
Change one controlled artifact
       ↓
Run a fixed-budget evaluation
       ↓
Measure objective metrics
       ↓
Improved? Keep the commit
Worse? Revert the commit
       ↓
Repeat
```

---

## Personal use cases

| Rank | Use case | How Autoresearch applies | Readiness |
|---:|---|---|---|
| **1** | **Optimize personal skills** | Modify one `SKILL.md`; score trigger accuracy, task success, regressions, and token cost; keep or revert. Improvements compound across Pi, Claude Code, and Hermes. | **Now · Adapt** |
| **2** | **Improve piece-of-work classification** | Test classifier instructions against a fixed, human-labelled project set; optimize classification accuracy and taxonomy consistency. | **Now · Adapt** |
| **3** | **Agent autonomy safety laboratory** | Use the existing repository to test bounded permissions, experiment ledgers, rollback behavior, stopping rules, and unattended reliability. | **Now · Direct** |
| **4** | **Tune `scout-patterns` relevance** | Run against historical behavior windows; score whether it finds known valuable patterns while suppressing previously dismissed noise. | **Next · Adapt** |
| **5** | **Benchmark research-agent protocols** | Change `program.md` or orchestration prompts; compare completion rate, cost, evidence quality, and intervention frequency on fixed tasks. | **Next · Adapt** |
| **6** | **Improve voice-note extraction** | Optimize prompts for extracting actions, ideas, decisions, and Chief-of-Staff proposals against hand-labelled voice memos. | **Later · Adapt** |
| **7** | **Evaluate local models and Apple Silicon performance** | Explore model architecture, optimizer, memory, and throughput configurations on the M5 Max. | **Now · Direct** |
| **8** | **Learn practical LLM training** | Study architecture, optimization, statistical noise, and hardware-specific tradeoffs through autonomous experiments. | **Now · Direct** |
| **9** | **Resume and application content evaluation** | Compare summarization or rewriting prompts against a rubric for factual fidelity and role relevance. Human judgement limits full autonomy. | **Later · Adapt** |

### Recommended personal starting point

Start with **personal skills optimization** because it supports the P1 harness-neutral skills repository and compounds across every agent environment.

Use `piece-of-work-classifier` as the first candidate because it can be evaluated against a finite, human-labelled set of existing projects. Do not attempt to optimize the entire skills repository at once.

---

## Work use cases

| Rank | Use case | How Autoresearch applies | Readiness |
|---:|---|---|---|
| **1** | **Hermes retrieval optimization** | Vary chunking, embedding, query expansion, reranking, and top-k settings; score answer accuracy, citation coverage, latency, and cost. | **Next · Adapt** |
| **2** | **General-research orchestration** | Modify research prompts and agent structure; evaluate against gold claims for source validity, freshness, coverage, contradictions, and cost. | **Next · Adapt** |
| **3** | **Hermes model routing and prompts** | Compare models, prompts, context sizes, and retry strategies against fixed workflows; optimize quality per dollar and latency. | **Later · Adapt** |
| **4** | **Enterprise document extraction** | Optimize extraction of obligations, precedents, risks, procedures, or claims from customer documents using labelled examples. | **After pilot data · Adapt** |
| **5** | **Hermes ingestion quality** | Experiment with parsing, metadata, deduplication, document segmentation, and enrichment while measuring downstream retrieval quality. | **Later · Adapt** |
| **6** | **Research verification agent** | Tune a verifier to catch unsupported, stale, or contradictory claims using historical accepted and rejected research evidence. | **Later · Adapt** |
| **7** | **Customer-specific workflow configuration** | Optimize an agent workflow against a customer's representative task set before deployment. | **After paid pilot · Adapt** |
| **8** | **Knowledge-store performance tuning** | Compare indexes, query strategies, caching, and storage parameters against latency and recall benchmarks. Conventional benchmarking may be simpler. | **Later · Adapt** |
| **9** | **Domain-specific model training** | Train or fine-tune a small model after accumulating proprietary data and proving that API-model costs justify it. | **Much later · Adapt or rebuild** |
| **10** | **Outreach-message optimization** | Use reply or meeting rates as the score. Feedback is slow, sparse, and heavily confounded, so conventional experiments are preferable. | **Not recommended** |

### Recommended work starting point

The strongest commercial application is **Hermes retrieval optimization**, but only after Johari Labs has:

1. A representative customer document corpus.
2. Approximately 30–100 realistic questions.
3. Expected answers or relevance labels.
4. Citation, latency, and cost metrics.

Until that evaluation set exists, customer outreach and problem validation remain more valuable than building optimization infrastructure.

---

## Overall priority

1. **Personal skills evaluation harness**
2. **Piece-of-work classifier optimization**
3. **Bounded autonomy experiments using the current MLX repository**
4. **Hermes retrieval optimization after an evaluation corpus exists**
5. **General-research orchestration optimization**
6. **Other applications only after repeated evidence justifies the infrastructure**

## Operating guidance

- Keep the MLX repository as an isolated personal sandbox.
- Use a dedicated branch for every experiment campaign.
- Change only one controlled artifact per campaign.
- Define the evaluator and stopping rules before autonomous execution.
- Begin with supervised, bounded runs before allowing overnight operation.
- Confirm small improvements across multiple runs or seeds.
- Do not permit automatic remote pushes, dependency changes, secret access, or production mutations.
- Promote the workflow into a durable skill or service only after several useful manual campaigns.

Most production applications should reuse Autoresearch's **evaluate → mutate → measure → keep/revert** pattern rather than importing its model-training code.
