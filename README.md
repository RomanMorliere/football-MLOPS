# football-MLOPS

Projet NLU (exercice « fine-tuner un modèle sur des données choisies ») : un
assistant de réservation de terrains de football au Liban, inspiré de
l'application Foot 7joz. Le modèle (Qwen3-0.6B + LoRA) lit un message court et
répond `intention | slot=valeur | ...`. Il n'intègre pas l'application : on
valide seulement le modèle.

## Contenu

| Fichier | Rôle |
|---|---|
| `donnees/ontologie.yaml` | 8 intentions, 8 slots, règles d'annotation (figée : partie chez l'autre groupe) |
| `donnees/train.csv` | 120 phrases, 15 par intention |
| `donnees/validation.csv` | 24 phrases, 3 par intention |
| `donnees/test.csv` | **à écrire par Roman**, sans regarder le train (voir `docs/prompt_test.md`) |
| `donnees/NOTES.md` | source, licence, choix, limites, lectures du test |

## Étapes

1. Copier le kit de l'exercice (Makefile, `valider_donnees.py`, `importer.py`,
   `evaluer_livrable.py`, `EXERCICE_PROJET_NLU.md`) à la racine, **sans écraser
   `donnees/`**, puis `uv sync`.
2. Remettre `donnees/ontologie.yaml` à l'autre groupe ; écrire leur test croisé
   (au moins 15 phrases) et le vérifier :
   `uv run python valider_donnees.py --test-croise test_croise_<groupe>.csv --ontologie ontologie_<groupe>.yaml`
3. Roman écrit `donnees/test.csv` (3 par intention).
4. `make valider` jusqu'à 0 erreur.
5. Régler sur la validation : `make run` avec `TRAIN_MAX_STEPS` = 30, 60, 100, 150
   dans `.env`, puis `make prompt-systeme` et `make prompt-exemples`.
6. Juger une seule fois : `make juger`. Noter le nombre de lectures du test.
7. Compléter `donnees/NOTES.md` (scores, écart test / test croisé).
8. `make livrer`, puis `make evaluer` et
   `uv run python evaluer_livrable.py livrables/livrable_<groupe>.zip --demo`.
   Les scores doivent correspondre à `make juger`. Rendre seulement le `.zip`.
