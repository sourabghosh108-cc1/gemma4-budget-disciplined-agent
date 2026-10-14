
# Budget-Disciplined Prompting for Small Local Software Engineering Agents

**A Prompt-Only Gemma 4 31B Agent and the Limits of What a Leaderboard
Score Can Show**

This repository documents the configuration and research context for a
prompt-only Gemma 4 31B software-engineering agent submitted to the
**Google --- Gemma 4 Developer Agent Paper Track**.

## Paper

-   **Author:** Sourab Ghosh
-   **ResearchGate DOI:**
    [10.13140/RG.2.2.29014.41285](https://doi.org/10.13140/RG.2.2.29014.41285)
-   **Paper page / full text:** [Read the paper on
    ResearchGate](https://doi.org/10.13140/RG.2.2.29014.41285)
-   **Competition:** [Google --- The Gemma 4 Developer Agent Paper
    Track](https://www.kaggle.com/competitions/gemma-4-developer-agent-paper)
-   **Research status:** ResearchGate-hosted paper/preprint. The DOI
    provides a persistent link; it does not by itself imply
    peer-reviewed journal publication.

## Overview

The paper investigates whether explicit prompt rules can help a
software-engineering agent allocate a limited interaction budget among
repository exploration, editing, verification, and patch submission.

The approach is **prompt-only**: it uses a single system prompt to
specify an action policy, without fine-tuning, LoRA adapters, or
subagents. The prompt is approximately 3.2 KB and encourages early
editing, bounded verification, and deliberate submission.

The central claim is deliberately limited. The submitted configuration
provides a documented baseline, but the available evidence does not
establish that the policy outperforms alternative prompts, tools, or
budgets.

## Reported configuration

  Setting                    Value
  -------------------------- -----------------------------------------------------
  Model                      `gemma-4-31b-it-qat-w4a16-ct`
  Approach                   Prompt-only; no fine-tuning, adapters, or subagents
  Temperature                `0.2`
  Top-p                      `0.95`
  Maximum output tokens      `8192`
  Thinking budget            `4096` tokens; thoughts retained
  Context window             `32768` tokens
  Agent time budget          8 minutes
  Tool-call limit            28
  Command/test timeout       240 seconds
  Turn limit                 80
  Enabled tools              6 of 9
  Public leaderboard score   `0.13`

The score `0.13` is the reported public leaderboard score for submission
`googleabcd`, version 2. It is **not** described here as a resolution
rate or as evidence of improvement over a baseline.

## Prompt policy

The system prompt encodes six groups of rules:

1.  **Contract first.** Extract exact symbols, signatures, error
    strings, status codes, and return values from the task requirements.
2.  **Bounded localization.** Use a focused `git grep` and inspect a
    limited code region, generally no more than 120 lines at a time.
3.  **Edit early.** Aim to modify a source file by tool call 12.
4.  **Bounded verification.** Run at most one focused check and permit
    one repair attempt; avoid running the full test suite.
5.  **Submit deliberately.** Keep scratch scripts in `/tmp`, prepare a
    clean patch, and submit before the remaining time becomes too short.
6.  **Avoid unproductive exploration.** Stay within `/workspace` and
    avoid broad searches after the first edit.

These are the policy rules used by the submitted configuration. Their
individual effects have not been established by completed ablation
experiments. Some rules are benchmark-specific and should not be copied
into production workflows without reconsideration---for example, real
engineering must allow an agent to conclude that an issue is already
resolved.

## Tool selection

The submitted configuration enables:

-   `run_command`
-   `read_file`
-   `edit_file`
-   `write_file`
-   `get_status`
-   `submit_patch`

The following graph-oriented tools are not enabled:

-   `get_code_neighbors`
-   `search_similar_code`
-   `get_code_subgraph`

This is a design choice, not a demonstrated optimization. Whether
enabling graph tools would improve performance remains an open
experimental question.

## Evaluation result and limitations

The only scored submission reported in the paper is `googleabcd`,
version 2, with a public leaderboard score of `0.13`.

The current evidence does **not** establish:

-   performance improvement over a verified baseline;
-   the causal contribution of any individual prompt rule;
-   that omitting graph tools improves performance;
-   that the eight-minute budget is better than a larger budget;
-   trace-level counts explaining why tasks failed; or
-   generalization beyond the competition environment.

Other submission attempts reportedly errored or timed out, but their
causes have not been established and are not attributed to specific
faults here.

## Proposed experiments

The paper proposes, but does not report as completed, the following
ablations:

-   Remove the edit-by-call-12 rule.
-   Remove the one-check/one-repair limit.
-   Enable the three graph-oriented tools.
-   Set the thinking budget to zero.
-   Compare with the default 60-minute, 100-call budget.
-   Classify task traces by failure mode.

A rigorous comparison should hold the task set and other settings
constant, and report the harness version, evaluation command,
concurrency, number of tasks, per-task outcomes, and execution failures.
Proposed experiments must remain labelled **not run** until results are
actually collected.

## Artifact and reproducibility

The paper describes the submitted artifact as a 2.4 KB archive
containing exactly four files:

``` text
agent.yaml
configs/sampling.yaml
eval_config.yaml
prompts/system.md
```

For faithful reproduction, use the original files from the scored
submission rather than a reconstructed configuration. The exact harness
version, local evaluation command, concurrency, local task count, and
task-level traces were not established in the available record.

Harness behavior described in the paper---including patch recovery,
handling of untracked files, protected test/configuration changes, call
accounting, and timing---should be checked against the exact harness
version used for any reproduction.

## Related work

-   Jimenez, C. E., Yang, J., Wettig, A., Yao, S., Pei, K., Press, O., &
    Narasimhan, K. R. (2024). [SWE-bench: Can language models resolve
    real-world GitHub issues?](https://arxiv.org/abs/2310.06770). *The
    Twelfth International Conference on Learning Representations (ICLR
    2024)*.
-   Xia, C. S., Deng, Y., Dunn, S., & Zhang, L. (2024). [Agentless:
    Demystifying LLM-based software engineering
    agents](https://arxiv.org/abs/2407.01489). *arXiv*.
    https://doi.org/10.48550/arXiv.2407.01489
-   Yang, J., Jimenez, C. E., Wettig, A., Lieret, K., Yao, S.,
    Narasimhan, K. R., & Press, O. (2024). [SWE-agent: Agent-computer
    interfaces enable automated software
    engineering](https://arxiv.org/abs/2405.15793). *Advances in Neural
    Information Processing Systems, 37*.

## License

No repository license is specified by this README. Before adding one,
confirm the competition's code-release requirements, rights to publish
the original submission, and compatibility with dependencies and
third-party materials. If a winning submission must use an OSI-approved
license permitting commercial use, choose a compatible license and
verify the requirement against the official competition rules.

## Citation

Ghosh, S. (2026, October 14). *Budget-disciplined prompting for small
local software engineering agents: A prompt-only Gemma 4 31B agent and
the limits of what a leaderboard score can show*. ResearchGate.
https://doi.org/10.13140/RG.2.2.29014.41285

If you reuse this work, cite the paper using the DOI above. Do not imply
that the leaderboard result proves the effectiveness of any individual
policy rule; the paper explicitly treats it as a baseline pending
controlled experiments.
