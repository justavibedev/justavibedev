# AI agents, evaluation reliability, and inference infrastructure

I work on agentic workflows and the systems used to evaluate and run them. My current open-source focus is reproducible failures, bounded fixes, and regression coverage.

**Start here:** [checkpoint recovery experiment](https://github.com/justavibedev/tau2-bench/tree/ec199b93253cd17e0e457751c8f7aed607770522/src/experiments/checkpoint_failure_recovery) · [tool-call parsing experiment](https://github.com/justavibedev/sglang/tree/909759032d7f292aabb3e9e78600aabb920252d1/experiments/hunyuan-schema-type-arrays) · [merged async cleanup fix](https://github.com/cocoindex-io/cocoindex/pull/2374)

## Selected contributions

| Project | My contribution | Upstream status¹ |
| --- | --- | --- |
| **CocoIndex** | Moved producer-thread joining off the event loop during async iterator cleanup, retained the cleanup timeout, and added a regression proving the loop can advance while the producer exits. | [#2374 — merged](https://github.com/cocoindex-io/cocoindex/pull/2374) |
| **kvcached** | Serialized sleep/wake transitions with a per-model lock; added handler coverage so concurrent callers both succeed when one has already woken the model. Tests mock the server boundary. | [#484 — open PR](https://github.com/ovg-project/kvcached/pull/484) |
| **SGLang** | Added support for JSON Schema type arrays in Hunyuan tool arguments, preserving existing coercion rules; added streaming and non-streaming regressions. | [#38940 — open PR](https://github.com/sgl-project/sglang/pull/38940) |
| **τ²-bench** | Changed checkpoint replacement ordering to preserve the previous trajectory and index when writing the replacement fails; added six fault-injection regressions and retry checks. | [#533 — draft PR](https://github.com/sierra-research/tau2-bench/pull/533) |

## Reproducible engineering experiments

Each link points to an immutable commit with reproduction instructions, source versions, recorded dependencies, raw results, and limitations.

### Checkpoint recovery under write failures · τ²-bench

Injected failures at temporary-file creation, serialization, and trajectory rename, using both new and reused simulation IDs. The baseline preserved the original checkpoint in **0/6 selected cases**; the patch preserved it in **6/6**. Retries succeeded in all six cases for both versions.

[Method, code, and raw results](https://github.com/justavibedev/tau2-bench/tree/ec199b93253cd17e0e457751c8f7aed607770522/src/experiments/checkpoint_failure_recovery)

Scope: deterministic local filesystem cases. This does not establish crash atomicity or improve an agent's benchmark score.

### Tool-schema compatibility across streaming boundaries · SGLang

Replayed **30 distinct schema/value fixtures** through the actual Hunyuan detector with complete, seven-character, and single-character chunks. The patch passes all **183 regression checks**, including repeated mode/chunk checks and three normalization checks; all fail on the baseline. The fixed direct-detector suite passes 43 test methods.

[Method, code, and raw results](https://github.com/justavibedev/sglang/tree/909759032d7f292aabb3e9e78600aabb920252d1/experiments/hunyuan-schema-type-arrays)

Scope: CPU source harness. Package startup and four higher-level integration tests are excluded; no GPU, serving-performance, or model-quality result is claimed.

### Stored completion references in evaluation logs · Inspect AI

Published a reproduction and proposed regression contract for existing [issue #4515](https://github.com/UKGovernmentBEIS/inspect_ai/issues/4515#issuecomment-5625134407): resolved choice text can coexist with an unresolved stored completion reference. The baseline exposes **15 failing assertions**, with **10 passing controls**; 30 existing attachment tests pass.

[Reproduction, proposed tests, and raw results](https://github.com/justavibedev/inspect_ai/tree/a3134ea6b6c2d735a9be363b0f3124fe9ce0551e/experiments/completion-resolution-4515)

Status: investigation and evidence only. No implementation or upstream PR; the proposed resolution scope awaits maintainer acceptance.

---

¹ Contribution status checked September 10, 2026; the linked upstream pages show current review and CI state. The contributions and experiments use AI coding assistance, including Codex. Linked patches and artifacts identify the scope of the work; test results describe recorded automated runs.
