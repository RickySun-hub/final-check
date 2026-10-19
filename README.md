# Final Check

**Know what is ready, what is missing, and what the evidence actually proves.**

Final Check is a lightweight agent skill for reviewing a task before its next meaningful step: handing over a file, using a research result, submitting an application, or releasing a code change. It prioritizes consequential omissions and unsupported completion claims, while allowing a clean result when no material issue is found.

The skill is model-version independent. It gives capable models decision criteria, evidence boundaries, and a stopping rule rather than a rigid checklist. It does not promise perfect reviews or replace domain expertise.

[中文说明](#中文说明) · [Usage](#usage) · [Installation](#installation) · [Evaluation](#evaluation)

## What changed in v2

| Earlier approach | v2 behavior |
| --- | --- |
| Start by treating the user as potentially overconfident | Assess the artifact and completion claim without assuming the user is wrong |
| Walk through broad review categories | Select checks that can affect the intended outcome |
| Identify missing tests | Ask what evidence is needed; reuse applicable results |
| Always fill a fixed report template | Give a short, evidence-bounded conclusion; expand only for material findings |
| Recommend another step | Stop when remaining checks are unlikely to change the decision |
| Leave review/fix scope implicit | Review is read-only unless fixes are already authorized |

## Usage

Invoke it explicitly with a concrete target and next step:

```text
$final-check Review this change before I open a PR. Focus on the requested
behavior and regressions. Review only; do not edit files.
```

```text
$final-check Check this report before handoff. Verify required sections,
numerical claims, the exported PDF, and the final file link.
```

```text
$final-check Review the implementation and fix confirmed issues within
the existing scope. Reuse checks whose relevant inputs have not changed.
```

Natural-language requests such as “final check,” “what am I missing?” or “最后检查一下，看看有没有重要遗漏” also match its description when the host supports skill discovery. It is not intended to become a mandatory gate for every ordinary task.

### Example result

> Not yet ready for the report's claimed result. The job log confirms completion, but the plotted output belongs to an older configuration. Regenerate the plot from the matching output and verify its metadata. No new compute job is justified by this finding alone.

For a clean review:

> Ready for document handoff within the reviewed scope. The requested sections are present, the PDF renders correctly, and its delivery link points to the checked file. Numerical source data was supplied by you and was not independently audited.

These are illustrative outputs, not records of executed reviews.

## How it works

The agent recovers the user's requirements, examines the relevant artifact, and tests consequential completion claims against evidence. It distinguishes demonstrated defects from missing evidence and optional improvements, then selects the smallest permitted verification that could change the decision.

Findings explain the requirement, evidence, consequence, and remedy. The conclusion is scoped to the next step: ready, ready with limitations, or not yet ready. No mandatory headings, issue quota, numeric score, or repeated test suite is required.

## Installation

This repository is the skill folder: keep `SKILL.md` and `agents/openai.yaml` together. Install it as a directory named `final-check` inside your host's configured skills directory. For Codex setups using `CODEX_HOME`, the target is `$CODEX_HOME/skills/final-check`; if unset, the conventional location is `~/.codex/skills/final-check`.

PowerShell, for a new installation:

```powershell
$skillHome = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $HOME '.codex' }
$skillTarget = Join-Path $skillHome 'skills/final-check'
if (Test-Path -LiteralPath $skillTarget) {
    throw 'final-check already exists. Review and back it up before updating.'
}
git clone https://github.com/RickySun-hub/final-check.git $skillTarget
```

For an existing installation, compare the files first, preserve local customizations, and replace only the intended skill files after backing them up. Refresh skill discovery or start a new session as required by your host. This repository does not change global skill enablement or other configuration.

The skill itself needs no API key, package install, script runner, or external service. A review may need tools appropriate to its target; unavailable evidence must remain explicitly unverified. Other agents that support `SKILL.md` may be able to use it, but cross-host compatibility has not been established here.

## Files

```text
final-check/
├── SKILL.md              Agent instructions and version metadata
├── agents/openai.yaml    Display metadata and suggested invocation
├── README.md             Human-facing usage and installation guide
└── tests/scenarios.md    Behavioral review cases and acceptance criteria
```

## Boundaries

- A review is read-only unless the user also authorizes corrections.
- Existing permission stays valid; invoking the skill does not authorize deployment, spending, messages, destructive actions, or new sensitive-data access.
- Current evidence matters. A passing build is not proof of a live integration, and a completed job is not proof of a valid scientific result.
- Findings are bounded by inspected evidence. Inaccessible data is unknown, and no findings does not guarantee the absence of all defects.
- No automatic delegation, model switching, or paid evaluation is required.

## Evaluation

[Behavioral scenarios](tests/scenarios.md) cover clean reviews, stale evidence, critical unknowns, review-only boundaries, authorized repairs, and changing requirements. They are reusable evaluation fixtures, not a claim of benchmark performance.

When the Codex `skill-creator` validator is installed, run its `scripts/quick_validate.py` against this directory. This checks the skill's structure; it does not establish review quality. For behavioral evaluation, give an agent a scenario and only its input artifacts, then compare its actions and conclusion with the acceptance criteria. Keep external actions disabled for these fixtures.

No model-version comparison or measured accuracy improvement is claimed. Future changes should address observed review failures without adding universal rituals.

## 中文说明

Final Check 用于回答：**这件事是否已具备进入下一步的条件，还有什么会影响结果的重要遗漏？**

新版重点是减少无效流程，同时提高结论的证据要求：

- 不默认用户判断错误，也不为了显得认真而强行找问题。
- 区分真实缺陷、关键证据不足和可选优化。
- 已有验证仍适用时复用；只有相关变化、失败或未解决风险才补测。
- 单纯审查默认只读；已授权修复的任务可以继续完成范围内的修改。
- 有明确停止条件；检查完重要要求即可交付，不无限追求满分。
- 可以给出“未发现重要问题”，但要说明检查范围和影响判断的限制。

示例：`$final-check 帮我检查这份交付是否满足原始要求，只报告影响使用的问题；已经验证且输入未变的项目不要重复测试。`

适用于代码、科研结果、申请材料、邮件草稿、文档和界面交付。具体深度取决于任务风险，不机械套用全部检查项。此版本的优化来自工作流设计调整，并非已完成的模型性能对比实验。
