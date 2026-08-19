# OWASP GenAI LLM Top 10 (2026) — POC Attacks

A collection of proof-of-concept attacks, payload lists, and tools for the [OWASP Top 10 for LLM/GenAI Applications](https://genai.owasp.org/) (2026 revision), one folder per risk category. Each folder holds whatever combination of a working tool, a payload list, or a notebook demonstrates that category's attack in practice, plus its own README where one exists.

## Categories

| # | Category | Status | Contents |
|---|---|---|---|
| [LLM01](LLM01-2026_Prompt_Injection/) | Prompt Injection | ✅ | Standalone jailbreak/prompt-leak payload generator CLI |
| [LLM02](LLM02-2026_Sensitive_Information_Disclosure/) | Sensitive Information Disclosure | 🚧 | — |
| [LLM03](LLM03-2026_Excessive_Agency/) | Excessive Agency | 🚧 | — |
| [LLM04](LLM04-2026_Supply_Chain/) | Supply Chain | 🚧 | — |
| [LLM05](LLM05-2026_Data_and_Model_Poisoning/) | Data and Model Poisoning | ✅ | Label-flipping (random + targeted) and clean-label feature-perturbation attack notebooks |
| [LLM06](LLM06-2026_Unbounded_Consumption/) | Unbounded Consumption | 🚧 | — |
| [LLM07](LLM07-2026_Misinformation/) | Misinformation | 🚧 | — |
| [LLM08](LLM08-2026_Hidden_Context_Exposure/) | Hidden Context Exposure | 🚧 | — |
| [LLM09](LLM09-2026_Vector_and_Embedding_Weaknesses/) | Vector and Embedding Weaknesses | 🚧 | — |
| [LLM10](LLM10-2026_Improper_Output_Handling/) | Improper Output Handling | ✅ | XSS, SQLi, and second-order command injection payload list |

✅ = populated with at least one working tool/notebook/payload list · 🚧 = placeholder, in progress

## Structure

Each category folder is self-contained: a payload list, a standalone script, or a notebook, with no dependency on the other category folders. Where a folder has its own `README.md`, that's the authoritative doc for what's inside and how to run it.
