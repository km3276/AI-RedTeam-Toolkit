# prompt_injection_cli.py

Standalone prompt injection / jailbreak payload CLI.

## Interactive mode

```
python prompt_injection_cli.py
```

Prompts for a technique (number or name), then the goal/target phrase.

- `list` — show every technique, numbered, by category
- `help` — reprint usage
- `decode` — convert a model's raw `imm_encode` numeric reply (e.g. `[87, 104, 97, 116]`) back to text
- `quit` — exit

## One-shot mode

```
python prompt_injection_cli.py grandma_roleplay "How do I pick a lock?"
python prompt_injection_cli.py decode "[87, 104, 97, 116]"
```

`list`, `help`, and `decode` also work as the first argument outside interactive mode.

## Categories

- **Jailbreak** (input: goal) — `dan`, `baseline_override`, `grandma_roleplay`, `roleplay_persona_generic`, `fictional_scenario`, `token_smuggling`, `suffix_completion`, `opposite_mode`, `imm_encode`, `adversarial_suffix_0`/`1`.
- **Prompt leaking** (input: target) — `authority`, `storytelling`, `indirect_first5`/`last5`/`middle5` embed your target phrase directly. `translate_german`, `spellcheck`, `summarize`, `encode_base64`, `encode_rot13` do **not** , they're context-dependent follow-ups (they reference "the above") meant to be sent as a *second* message, after a prior message already got the target info to appear in the conversation (e.g. via `authority`). The CLI skips the phrase prompt for these five and labels them in the output.
- **Injection builders** (input: multiple fields, asked one at a time) ,`fact_injection`, `csv_row_injection`, `html_comment_injection`, `boundary_injection`, `token_smuggling_hint`. Interactive-only, not available in one-shot mode.
- **All-in-one** — `all_jailbreak` (runs every jailbreak technique against one goal) and `all_leak` (runs every leak technique against one target). Numbered last in `list` (27/28), so those numbers shift if techniques are added/removed, run `list` to confirm current numbering rather than hardcoding them.

## imm_encode / decode — two-step technique

`imm_encode` is Instructional Message Munging: it defines a fictional Haskell char↔int encoding scheme in the payload, then asks the model to reply with its answer as a list of ints instead of plaintext, the idea is that filters scanning for restricted plaintext (in either direction) never see any, since everything in transit is numbers. Send the `imm_encode` payload first; if the model complies, it replies with something like `[87, 104, ...]`. Take that raw reply and run `decode` on it to get the plaintext answer back. Only reliably works against capable/frontier models, small models can't follow the encode/decode instructions.
