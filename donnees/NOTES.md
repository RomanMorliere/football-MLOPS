# Notes

## Sujet
Assistant de réservation de terrains de football au Liban, inspiré de
l'application Foot 7joz (React Native + Spring Boot). Les intentions
correspondent à ce que l'application fait vraiment : chercher un terrain,
vérifier un créneau, demander un prix, réserver, consulter ses réservations,
annuler, changer le moyen de paiement. Tout le reste, y compris déplacer ou
prolonger une réservation (que l'application ne permet pas), va dans
`hors_domaine`.

## Source et licence des données
Écrites à la main par le groupe, en français. Aucun jeu de données en ligne.
Noms de complexes inventés ; villes et quartiers libanais publics ; aucune
donnée personnelle.

## Choix
- Valeurs recopiées du message (minuscules, sans article), comme le demande
  l'exercice : la conversion « demain 20h » -> date réelle reste le travail de
  l'application, pas du modèle.
- Ordre de décision écrit dans l'ontologie pour que deux personnes choisissent
  la même intention.
- `hors_domaine` contient des « presque-réservations » (déplacer, prolonger,
  liste d'attente, remboursement) pour que le modèle ne range pas tout ce qui
  parle d'une réservation dans une intention d'action.

## Limites
- Une même idée a plusieurs valeurs (couvert, couverts, intérieur, en salle).
- Heures sans matin/soir (« à 8h ») et dates relatives (« lundi prochain »)
  restent ambiguës.
- Pas d'arabe ni d'arabizi, alors que les vrais utilisateurs en écriraient.
- `nb_joueurs` ne dit pas si c'est un total ou par équipe (« 5 contre 5 »).

## Réglages et résultats
À compléter : TRAIN_MAX_STEPS retenu, meilleur prompt, scores validation,
scores test (LoRA / base / base+prompt), score test croisé.

## Lectures du test
À compléter (compteur `lectures_test` du rapport). Toute modification des
données après lecture du test sera notée ici.

## Écart entre notre test et le test croisé
À compléter après réception du score.
