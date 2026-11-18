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

## Steps

1. Copy the exercise kit (Makefile, `valider_donnees.py`, `importer.py`,
   `evaluer_livrable.py`, `EXERCICE_PROJET_NLU.md`) to the root **without
   overwriting `donnees/`**, then `uv sync`.
2. Hand `donnees/ontologie.yaml` to the other group; write their cross test
   (at least 15 sentences) and check it:
   `uv run python valider_donnees.py --test-croise test_croise_<groupe>.csv --ontologie ontologie_<groupe>.yaml`
3. Roman writes `donnees/test.csv` (3 per intention).
4. `make valider` until 0 errors.
5. Tune on validation: `make run` with `TRAIN_MAX_STEPS` = 30, 60, 100, 150
   in `.env`, then `make prompt-systeme` and `make prompt-exemples`.
6. Judge once: `make juger`. Write down how many times the test was read.
7. Finish `donnees/NOTES.md` (scores, gap between our test and the cross test).
8. `make livrer`, then `make evaluer` and
   `uv run python evaluer_livrable.py livrables/livrable_<groupe>.zip --demo`.
   Scores must match `make juger`. Hand in only the `.zip`.
