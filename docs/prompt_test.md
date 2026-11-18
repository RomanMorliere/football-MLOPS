# Prompt for writing donnees/test.csv

Paste into an AI, attaching **only** `donnees/ontologie.yaml`.
Never give it `train.csv` or `validation.csv`, and do not read them yourself.

```
You are writing the TEST set for a small NLU model. I am attaching ontologie.yaml: it is the only thing you may use. The domain: an English-speaking assistant for booking football pitches in Lebanon.

Write a CSV file, semicolon-separated, with exactly this header:
phrase;reponse

Format of the reponse column:
intention | slot=value | slot=value
- intention: one of the 8 intentions in the ontology, nothing else. Keep the names exactly as written (they are French words, that is expected).
- slots: only those in the ontology, in the order the ontology lists them, only the ones present in the message.
- value: copied EXACTLY from the message, lowercased, without a leading article (the, a, an). Normalise nothing: "8 PM" stays 8 pm, "tomorrow" stays tomorrow, "Beirut" becomes beirut. No "at", "on" or "for" in the value.
- hors_domaine never has a slot.
- Use the decision order written in the comment at the top of the ontology to choose the intention.

Example of the shape (made up, do not reuse it):
book Futsal Horizon on Thursday at 9 pm;reserver_terrain | nom_terrain=futsal horizon | date=thursday | heure=9 pm

Content:
- 3 messages per intention, so 24 lines (4 for hors_domaine if you like, 25 in total).
- Messages in English, the way a real user would type them on a phone: short (under 300 characters), varied tone (polite, casual, no punctuation, a light typo), varied verbs and phrasings.
- Vary the values: invent your own (fictional) football venue names, use varied Lebanese cities and areas, different times, lengths and dates.
- For hors_domaine, include at least one message that looks like a booking request but is not supported (moving a booking, extending a match, waiting list, refund...).
- No personal data (no real names, phone numbers or precise addresses).
- No semicolons inside the messages.

Then re-read every line and check: is that intention the only possible one? Does every value appear word for word in the message? Fix anything wrong, then give me the final CSV in a code block, with no comments.
```

Save the result as `donnees/test.csv`, run `make valider`, then do not open it
again until `make juger`.
