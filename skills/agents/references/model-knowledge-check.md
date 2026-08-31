# Offline Model Knowledge Check

Applies when: verifying the current model's identity and internal knowledge cutoff, excluding interference from network access, external tools, and memory.

> **Scope**: for ChatGPT / Codex only. Do not use with other agents or models.
>
> **Time-sensitive**: the probe questions are pinned to December 2025, so their discriminative power fades as model training data catches up. Keep the original questions verbatim — do not swap them out.

Execute the following instruction (verbatim):

> No web search, no external tool calls, and do not rely on prior conversation or long-term memory. Answer using only the internal knowledge in your model parameters: What is your current model name and model ID? Who was the Prime Minister of the Czech Republic on December 9, 2025? Who was the President of Guinea on December 28, 2025? What was the latest Python version number on December 1, 2025? If you cannot reliably confirm an answer, say "uncertain" directly — do not guess — and note that the answer has not been verified in real time. If your internal knowledge base has no relevant content, say "uncertain" directly — do not guess — and note that the answer has not been verified in real time.

Key points:

- No tools of any kind may be used while answering (search, browser, MCP, etc.), and no conversation history or memory may be referenced.
- When uncertain, answer "uncertain" explicitly; guessing is forbidden, and the answer must be marked as not verified in real time.
