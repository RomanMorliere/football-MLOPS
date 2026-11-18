# football-MLOPS

NLU project (exercise "fine-tune a model on data you choose"): an English
assistant for booking football pitches in Lebanon, based on the Foot 7joz app.
The model (Qwen3-0.6B + LoRA) reads a short message and answers
`intention | slot=value | ...`. It is not integrated into the app: we only
validate the model.

Example: `book Goal Zone on Friday at 9 pm` ->
`book_pitch | venue=goal zone | date=friday | time=9 pm`

## Contents

| File | Purpose |
|---|---|
| `donnees/ontologie.yaml` | 8 intentions, 8 slots, annotation rules (frozen: goes to the other group) |
| `donnees/train.csv` | 120 sentences, 15 per intention |
| `donnees/validation.csv` | 24 sentences, 3 per intention |
| `donnees/test.csv` | **written by Roman**, without looking at train (see `docs/prompt_test.md`) |
| `donnees/NOTES.md` | source, licence, choices, limits, number of test reads |

## Setup (once per machine)

```powershell
$env:PYTHONUTF8 = "1"   # the kit prints emojis; the Windows console needs UTF-8
uv sync
make setup              # MLflow + RustFS in Docker (Docker Desktop must be running)
```

Create a `.env` at the root (not versioned) with at least:

```
GROUPE=<our group name>
PROMPT_SYSTEME_FILE=prompts/examples_train_en.txt
TRAIN_MAX_STEPS=150
```

Everything else falls back to the defaults in `src/config.py`.

## Prompts

The kit builds its system prompt in French (`make prompt-systeme`). Our
assistant is English, so the English versions are in `prompts/`:

- `prompts/system_en.txt`: ontology only
- `prompts/examples_train_en.txt`: ontology + one train example per intention

Measure them on validation:

```powershell
uv run python prompt_lab.py prompts/system_en.txt validation
uv run python prompt_lab.py prompts/examples_train_en.txt validation
```

Point `PROMPT_SYSTEME_FILE` at the better one: that is the base+prompt column
the LoRA must beat.

## Steps

1. `make valider`: 0 errors (done).
2. Hand `donnees/ontologie.yaml` to the other group; write their cross test
   (at least 15 sentences) and check it:
   `uv run python valider_donnees.py --test-croise test_croise_<groupe>.csv --ontologie ontologie_<groupe>.yaml`
3. Tune on validation: `make run` with `TRAIN_MAX_STEPS` = 30, 60, 100, 150
   in `.env`, and measure the two English prompts.
4. Judge once: `make juger`. Write down how many times the test was read.
5. Finish `donnees/NOTES.md` (scores, gap between our test and the cross test).
6. `make livrer`, then `make evaluer` and
   `uv run python evaluer_livrable.py livrables/livrable_<groupe>.zip --demo`.
   Scores must match `make juger`. Hand in only the `.zip`.

The course's own instructions are in `EXERCICE_PROJET_NLU.md` and `KIT_README.md`.
