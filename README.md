[README (2).md](https://github.com/user-attachments/files/33447173/README.2.md)
# Budget-Disciplined Prompting for Small Local Software Engineering Agents

A prompt-only Gemma 4 31B software-engineering agent configuration using
an early-edit policy, a bounded verification loop, and an eight-minute
execution budget.

> **Research status:** This repository documents a configuration and a
> reported public leaderboard result. The current evidence does not
> establish that the prompting policy outperforms alternative policies.
> Ablations and trace-level failure analysis are not reported as
> completed.

## Overview

This project investigates how explicit prompting rules can guide a local
software-engineering agent operating under tight execution constraints.

The submitted configuration used:

-   **Model:** `gemma-4-31b-it-qat-w4a16-ct`
-   **Approach:** Prompt-only; no LoRA adapters or subagents
-   **Temperature:** `0.2`
-   **Top-p:** `0.95`
-   **Maximum output tokens:** `8192`
-   **Thinking budget:** `4096`, with thoughts retained
-   **Execution budget:** 8 minutes
-   **Tool-call budget:** 28 calls
-   **Command timeout:** 240 seconds
-   **Turn limit:** 80 turns
-   **Reported public leaderboard score:** `0.13`

The score above is the public leaderboard score for the reported
submission. It should not be interpreted as a resolution rate or as
evidence of improvement over a baseline.

## Prompting policy

The prompt organizes agent behavior around six rule groups:

1.  **Contract first:** Identify exact symbols, strings, status codes,
    and function signatures before editing.
2.  **Focused localization:** Use one focused `git grep` and read no
    more than 120 lines at a time.
3.  **Edit early:** Aim to make an edit by tool call 12.
4.  **Bounded verification:** Run at most one focused `pytest -k` check
    and allow one repair attempt; avoid running bare `pytest`.
5.  **Submit deliberately:** Keep scratch scripts in `/tmp`, submit the
    patch, and submit when fewer than 90 seconds remain even if the
    local check is still red.
6.  **Avoid unproductive exploration:** Do not conclude that the code is
    already correct, do not search outside `/workspace`, and avoid broad
    exploration after the first edit.

These are the intended policies encoded in the prompt. Their individual
effects have not yet been isolated by completed ablation experiments.

## Tool configuration

Six tools were enabled:

-   `run_command`
-   `read_file`
-   `edit_file`
-   `write_file`
-   `get_status`
-   `submit_patch`

The following graph-oriented tools were not enabled:

-   `get_code_neighbors`
-   `search_similar_code`
-   `get_code_subgraph`

The omission of graph-oriented tools is a configuration choice, not a
demonstrated performance optimization.

## Evaluation and evidence

The reported public leaderboard score for the `googleabcd` submission,
version 2, was **0.13**.

The available record does not establish:

-   a verified baseline score for comparison;
-   completed ablation results;
-   trace-level failure counts;
-   the complete local evaluation command, concurrency, and task count;
    or
-   the cause of other submission attempts that errored or timed out.

These gaps are intentionally disclosed rather than filled with
estimates. The claim that budget allocation matters more than total
budget remains a research hypothesis, not a demonstrated conclusion.

## Proposed follow-up experiments

The following experiments are proposed and should be marked as **not
run** unless results are added:

-   Remove the edit-by-call-12 constraint.
-   Remove the one-check/one-repair constraint.
-   Enable the graph-oriented tools.
-   Disable model thinking.
-   Compare against a larger execution budget.
-   Analyze agent traces using explicit failure categories, such as no
    edit, wrong file or function, incorrect behavior, exact-string
    mismatch, stray files, truncated calls, and time/budget exhaustion.

For a meaningful comparison, record the harness version, exact command,
concurrency, task count, per-task outcomes, and all configuration
differences.

## Reproducibility

A reproducible release should include the exact files used for the
scored submission, including the prompt and configuration files,
together with:

-   the competition harness/version;
-   the exact evaluation command;
-   the concurrency setting and number of tasks;
-   per-task pass/fail outcomes, where permitted;
-   the environment and dependency requirements; and
-   a clear distinction between completed experiments and proposed work.

**Important:** This README is a summary of the reported configuration.
It is not a substitute for the original submission files. Before
publishing, verify every value against the exact submitted prompt,
configuration, and competition harness. Do not describe a reconstructed
configuration as the original experiment.

## Related work

-   Jimenez, C. E., Yang, J., Wettig, A., Yao, S., Pei, K., Press, O., &
    Narasimhan, K. R. (2024). [SWE-bench: Can language models resolve
    real-world GitHub
    issues?](https://openreview.net/forum?id=VTF8yNQM66). In *The
    Twelfth International Conference on Learning Representations (ICLR
    2024)*.
-   Xia, C. S., Deng, Y., Dunn, S., & Zhang, L. (2024). [Agentless:
    Demystifying LLM-based software engineering
    agents](https://arxiv.org/abs/2407.01489). *arXiv*.
    https://doi.org/10.48550/arXiv.2407.01489
-   Yang, J., Jimenez, C. E., Wettig, A., Lieret, K., Yao, S.,
    Narasimhan, K. R., & Press, O. (2024). [SWE-agent: Agent-computer
    interfaces enable automated software
    engineering](https://doi.org/10.52202/079017-1601). In *Advances in
    Neural Information Processing Systems* (Vol. 37).

## License

No license is specified in this repository template. Add a license only
after confirming the applicable competition rules, ownership of the
submitted materials, and compatibility with dependencies and third-party
components. If the competition requires an OSI-approved license that
permits commercial use for a winning solution, select a compatible
license before release.

## Citation

If you use or discuss this work, cite the associated paper once its
public version and bibliographic details are finalized. Until then, do
not invent a publication venue, DOI, or arXiv identifier.
