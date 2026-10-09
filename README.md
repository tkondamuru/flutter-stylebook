# TJ's Flutter best-practices → LoRA

## Workflow
1. TJ uses the app and explains a part he likes in minute detail.
2. Marley writes one component card per part in `components/` (see CARD_TEMPLATE.md).
3. Each card is expanded into instruction→code training pairs, appended to `pairs.jsonl`.

## Training pair format (Alpaca JSONL)
One JSON object per line: `{"instruction": ..., "input": ..., "output": ...}`.
- `instruction`: the task, phrased the way TJ would ask for it
  (e.g. "Build a bottom nav with 4 tabs and badges, following my conventions").
- `input`: optional extra context (usually empty).
- `output`: Dart code embodying the component's rules.

This format loads directly into common LoRA trainers (Unsloth, Axolotl, TRL)
and converts trivially to chat/ShareGPT format if the chosen trainer wants it.

## Consistency bar
A few hundred consistent pairs beat a thousand mixed-quality ones. Every pair
must obey its card's Do/Don't list — when a card's rules change, its pairs get
regenerated.
