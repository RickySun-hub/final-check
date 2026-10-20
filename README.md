# Final Check

**A small, on-demand review convention for evidence, scope, and readiness.**

Final Check helps an agent review whether work is ready for its intended next step. It keeps attention on active requirements, supporting evidence, authorized corrections, and material gaps. A clean review is a valid result.

**Current release: v2.1.0 — the tested slim instructions, explicit invocation only.** The instruction body is identical to the slim candidate used in the Astra study; only version metadata was added. It is not presented as a necessary reasoning upgrade for strong models.

[中文说明](#中文说明) · [Usage](#usage) · [Scope](#scope) · [Astra comparison](#astra-comparison) · [Installation](#installation)

## Usage

Invoke the skill when you want a deliberate completion review:

```text
$final-check Review this change before handoff. Check the active requirements
and material evidence gaps. Review only; do not edit files.
```

```text
$final-check 检查这份交付是否满足最终要求。范围内已经授权的修复直接完成，
仍然适用的验证不要重复运行，只报告影响下一步的重要问题。
```

The Codex invocation policy in `agents/openai.yaml` is:

```yaml
policy:
  allow_implicit_invocation: false
```

This keeps the skill available through explicit `$final-check` invocation without implicitly adding its instructions to ordinary tasks. The policy depends on host support; other agents may interpret skill metadata differently. Global skill enablement and unrelated configuration are not changed by this repository.

## Scope

Use it when a requested review benefits from a shared convention: recover the final requirements, inspect the actual deliverables, distinguish a defect from an evidence gap, and give a bounded readiness conclusion. Examples include code handoff, reports, research screening, application materials, and message drafts.

It can reuse valid checks and perform corrections that the user already authorized. Reviewing alone does not authorize edits, sending, deployment, sensitive-data access, destructive work, or spending.

Do not use it as a mandatory gate for every task. It supplies instructions, not an evidence database, test runner, scientific validator, or external-service verification. It cannot recover inaccessible information or establish facts from a passing intermediate step alone. Strong models may already exhibit the same behavior without it.

The intended benefit is a consistent review convention with little extra instruction text. Improved accuracy, lower cost, faster execution, and universal compatibility are **not established**.

## Astra comparison

On 2026-10-19, a local study compared **no added skill**, the **slim candidate**, and **v2.0.0**, explicitly requesting `gpt-6-astra` for execution. It used six compound scenarios, three rounds, and three conditions: **54 scenario requests**. Inputs were byte-identical across conditions; the added review instructions were the intended difference.

| Observed measure | No skill | Slim candidate | v2.0.0 |
| --- | ---: | ---: | ---: |
| Cases meeting material acceptance criteria | 18/18 | 18/18 | 18/18 |
| Required tax-runtime checks, after setup | 3 | 3 | 3 |
| Unchanged auth/build script reruns | 0/0 | 0/0 | 0/0 |
| Already-valid package script reruns | 0 | 0 | 0 |
| Out-of-scope product changes found by file audit | 0 | 0 | 0 |
| Mean user-response characters | 244.6 | 268.3 | 219.6 |
| Added skill-file characters in the experiment | 0 | 1,713 | 6,133 |

**No material correctness or workflow advantage was detected for either skill condition in this study.** The slim candidate used 72.1% fewer instruction characters than v2.0.0 without an observed acceptance regression. v2 responses were about 10.2% shorter than no-skill responses; slim responses were about 9.7% longer. Shorter instructions did not imply shorter answers. Character counts are not token, cost, or latency measurements; release version metadata is not included in the candidate's 1,713-character figure.

The scenarios covered evolving requirements and analytical denominators, partially stale verification, constrained research ranking, authorized local fixes alongside inaccessible evidence, a clean handoff with distracting old failures, and restore identity plus approval gaps. Three fresh-context Astra graders scored anonymized round-level bundles against a prespecified rubric and inspected the edited artifacts, with parent file/hash and script-count audits as corroboration.

### What the study does and does not support

- It supports choosing the smaller instructions for **optional consistency**, not claiming that they make Astra more capable.
- Each condition had three execution sessions; six cases shared a session within a round. The 54 decisions are not 54 fully independent statistical samples. No significance or equivalence claim is made.
- Cases were synthetic and clearly scoped. All conditions scored at the ceiling; harder real-world failures could differentiate them.
- “No skill” meant no additional skill body. All conditions retained the same Codex platform instructions, permissions, and tool environment. Automatic skill discovery was not tested.
- Model selection was specified in the tool calls; no provider-side model attestation or explicit reasoning-effort override was recorded. Execution and grading used the same model family, so shared blind spots remain possible.
- Records contain command summaries, not complete tool traces. Script counters cover the supplied scripts, and filesystem audits do not independently prove the absence of every possible external action.

Evidence: [full report](tests/results/astra-2026-10-19/REPORT.zh-CN.md), [summary and scores](tests/results/astra-2026-10-19/summary.json), [54 observation records](tests/results/astra-2026-10-19/observations.json), [prespecified rubric](tests/results/astra-2026-10-19/rubric.md), and [reproduction inputs](tests/results/astra-2026-10-19/experiment-inputs.zip). The report preserves the historical state before promotion; v2.1.0 is the subsequent release decision.

## Installation

Keep `SKILL.md` and `agents/openai.yaml` together in a directory named `final-check` inside the host's configured skills directory. The YAML file carries the explicit-only invocation policy; copying only `SKILL.md` does not install that policy.

For a new Codex installation in PowerShell:

```powershell
$skillHome = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $HOME '.codex' }
$skillTarget = Join-Path $skillHome 'skills/final-check'
if (Test-Path -LiteralPath $skillTarget) {
    throw 'final-check already exists. Review and back it up before updating.'
}
git clone https://github.com/RickySun-hub/final-check.git $skillTarget
```

For an update, compare existing files, back up local customizations, and update the two runtime files together. Refresh skill discovery or start a new session as required by the host. The skill itself requires no API key, package installation, external service, or mandatory delegation. Optional experiment scripts use Python's standard library.

## Validation and history

The skill's structural validator checks metadata and format; it does not prove review quality. For the Astra experiment, use the instructions in the archived input package and keep the rubric away from the agent being evaluated. Checks that only confirm a file's format are not behavioral test passes.

- **v2.1.0:** adopts the exact tested slim instruction body and sets explicit-only invocation. No fresh 54-case rerun is claimed for the added version metadata or invocation setting; body equivalence, YAML policy, and structural checks validate that promotion separately.
- **v2.0.0:** introduced explicit evidence/scope/stopping rules. Its earlier [11-scenario acceptance run](tests/results/2026-10-19/REPORT.md) passed; that run did not independently identify its model and should not be attributed to Astra. The [earlier scenarios](tests/scenarios.md), generator and auditor remain for historical regression work. Running the generator against the current checkout uses the current skill, not a frozen v2 body.

Future changes should address demonstrated failures or add useful evidence-handling capabilities, rather than accumulate generic review reminders.

## 中文说明

当前正式版是 **v2.1.0 精简版，仅按需调用**。使用 `$final-check` 明确发起复查；不把它作为日常任务的强制前置步骤。安装时要同时保留 `SKILL.md` 与 `agents/openai.yaml`，后者设置了 `allow_implicit_invocation: false`。

它适合需要统一审查口径的场合：核对最终要求、实际交付物和验证证据，区分真实缺陷与未知，完成已有授权范围内的修复，并在重要问题处理完后停止。它不会自动提供额外事实、执行所有领域检查或扩大操作授权。

**Astra 实测没有显示额外的正确率或流程收益。** 在六类复合场景、三个条件、三轮共 54 次请求中，无 skill、精简版和旧 v2 均为 18/18 通过；测试复用和授权修改也没有拉开差距。选择精简版，是为了保留可选的工作约定，同时比旧 v2 减少约 72% 的指令字符，而不是宣称能增强 Astra 的推理能力。

这不是“所有真实任务都无用”的结论：样本是合成任务，目标明确，存在满分上限；同轮六案共享上下文，平台基础规则也始终存在。未测量实际 token、费用或延迟。正式正文与本次测试候选完全相同，仅增加版本元数据并设置显式调用策略；历史报告和原始记录保留，便于核查。
