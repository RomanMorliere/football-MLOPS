# Prompt pour écrire donnees/test.csv

À coller dans une IA en joignant **uniquement** `donnees/ontologie.yaml`.
Ne jamais lui donner `train.csv` ni `validation.csv`, et ne pas les lire soi-même.

```
Tu vas écrire le jeu de TEST d'un petit modèle NLU. Je joins le fichier ontologie.yaml : c'est la seule chose que tu dois utiliser. Le domaine : un assistant de réservation de terrains de football au Liban.

Écris un fichier CSV en français, séparé par des points-virgules, avec exactement l'en-tête :
phrase;reponse

Format de la colonne reponse :
intention | slot=valeur | slot=valeur
- intention : une des 8 intentions de l'ontologie, rien d'autre.
- slots : seulement ceux de l'ontologie, dans l'ordre où l'ontologie les liste, seulement ceux présents dans la phrase.
- valeur : recopiée EXACTEMENT de la phrase, en minuscules, sans article au début (le, la, les, un, une, des, l'). Ne normalise rien : « 20h » reste 20h, « demain » reste demain, « Beyrouth » devient beyrouth. Pas de « à » ni « pour » dans la valeur.
- hors_domaine n'a jamais de slot.
- Applique l'ordre de décision écrit en commentaire en haut de l'ontologie pour choisir l'intention.

Exemple de forme (inventé, ne le réutilise pas) :
réserve Futsal Horizon jeudi à 21h;reserver_terrain | nom_terrain=futsal horizon | date=jeudi | heure=21h

Contenu :
- 3 phrases par intention, donc 24 lignes (4 pour hors_domaine si tu veux, 25 au total).
- Des phrases comme un vrai utilisateur les taperait sur son téléphone : courtes (moins de 300 caractères), registres variés (poli, familier, sans ponctuation, avec une faute de frappe légère), verbes et tournures variés.
- Varie les valeurs : invente tes propres noms de complexes de foot (fictifs), utilise des villes et quartiers libanais variés, des heures, des durées et des dates différentes.
- Pour hors_domaine, mets au moins une phrase qui ressemble à une demande de réservation mais qui n'est pas supportée (déplacer une réservation, prolonger un match, liste d'attente, remboursement…).
- Aucune donnée personnelle (pas de vrai nom, de numéro, d'adresse précise).
- Aucun point-virgule dans les phrases.

Ensuite, relis chaque ligne et vérifie : l'intention est-elle la seule possible ? chaque valeur apparaît-elle mot pour mot dans la phrase ? Corrige, puis donne-moi le CSV final dans un bloc de code, sans commentaire.
```

Enregistrer le résultat dans `donnees/test.csv`, lancer `make valider`, puis ne
plus l'ouvrir avant `make juger`.
