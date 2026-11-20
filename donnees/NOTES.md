# Notes

## Subject
An English assistant for booking football pitches in Lebanon, based on the
Foot 7joz app (React Native + Spring Boot). The intentions match what the app
actually does: find a pitch, check a slot, ask a price, book, view bookings,
cancel, change the payment method. Everything else, including moving or
extending a booking (which the app does not support), goes to `out_of_scope`.
Intention names, slot names, messages and values are all in English.

## Data source and licence
Written by hand by the group, in English. No online dataset. Venue names are
invented; Lebanese cities and areas are public places; no personal data.

## Choices
- Values are copied from the message (lowercase, no leading article), as the
  exercise requires: turning "tomorrow 8 pm" into a real date is the app's job,
  not the model's.
- A decision order is written in the ontology so two people pick the same
  intention.
- `out_of_scope` contains near-bookings (move, extend, waiting list, refund) so
  the model does not file everything that mentions a booking under an action.

## Limits
- One idea has several values (indoor, covered; outdoor, open-air; cash, on site).
- Times without am/pm ("at 8") and relative dates ("next Monday") stay ambiguous.
- English only: no French, Arabic or Arabizi, which real users in Lebanon would write.
- `players` does not say whether it is a total or per team ("5-a-side").
- The course scorer strips French articles from values, not English ones, so
  the "no leading article" rule is applied by hand in the data.

## Data change during tuning (before reading the test)
First tuning round on 120 training sentences, validation only:

| Steps | val intent | val slot F1 | val exact |
|---|---|---|---|
| 30 | 0.00 | 0.17 | 0.00 |
| 60 | 0.08 | 0.41 | 0.04 |
| 100 | 0.46 | 0.79 | 0.33 |
| 150 | 0.71 | 0.87 | 0.67 |
| 200 | 0.71 | 0.83 | 0.63 |
| 300 | 0.88 | 0.85 | 0.71 |
| 400 | 0.83 | 0.91 | 0.79 |

Base model + English prompt with one example per intention: 0.58 intent.
The same errors survived every step count, so the problem was the data, not
the training length. Five families: small talk and football news labelled as a
booking intention, "do I have a match" (view_bookings), "slots left"
(check_availability), "switch my match to Whish" (change_payment), "how much is
a match" (ask_price), plus invented labels. 30 training sentences were added
for those families (train 120 -> 150), written fresh, none copied from
validation. The test had not been read at that point. The retrained runs are in
the MLflow experiment `projet-nlu-v2`, so the judge cannot pick a run trained
on the old data.

## Settings and results
- Base model: Qwen/Qwen3-0.6B, LoRA r=8, alpha=16, dropout=0.05, learning rate 2e-4.
- Chosen on validation: 300 steps (best validation intent accuracy, retrained on
  the 150-sentence train). MLflow experiment `projet-nlu-v2`.
- Prompt for the base+prompt column: `prompts/examples_train_en.txt` (English,
  ontology + one train example per intention). The kit builds its prompt in
  French; the English one without examples scored 0.00 on validation because
  the base model copied the `<intention>` placeholders, so the version with
  examples (0.58 on validation) is the honest baseline.

Validation (24 sentences):

| | intent | slot F1 | exact |
|---|---|---|---|
| base + prompt | 0.58 | 0.57 | 0.17 |
| LoRA 300 steps, 150 train | 0.88 | 0.94 | 0.83 |

Test (25 sentences, written by Roman without seeing train, read once):

| | intent | slot F1 | exact |
|---|---|---|---|
| LoRA | 0.88 | 0.84 | 0.60 |
| base | 0.00 | 0.20 | 0.00 |
| base + prompt | 0.48 | 0.47 | 0.20 |

Gate: PASS (intent 0.88 >= 0.70, lead +40 points >= 30).

Intent accuracy is the same on validation and test (0.88), so the intentions
generalise. Exact match drops from 0.83 to 0.60: the slot values are the
fragile part (a value written differently, an extra or missing slot).

Remaining errors on validation are mostly invented labels (`ask_pitch`,
`cancel_pitch`): with 15 to 25 sentences per intention the model learns the
shape `word_word | slot=value` faster than the closed list of names.

## Test reads
1 (`make juger`, after the data change and the choice of 300 steps). No data or
setting was changed after reading the test.

## Gap between our test and the cross test
No cross test: no other group was available to exchange ontologies with, so
nobody outside the group wrote messages for this model, and we wrote none for
another group.

What stands in for it, and its limits:
- The test was written by Roman alone, from the ontology only, without seeing
  train or validation, and it was read once. The validation was never used to
  write it.
- Intent accuracy is the same on validation (0.88) and on the test (0.88).
  That says the intentions generalise to new phrasings from someone in the
  group. It does not say what an outside user would write: Roman knew the
  labelling rules and the domain, which is exactly the bias a cross test
  removes (TP3: a test written by the people who wrote the train flatters the
  model).
- Exact match falls from 0.83 (validation) to 0.60 (test). We expect an
  outside test to land lower still, mostly on slot values and on messages that
  sit between two intentions (view_bookings vs check_availability).
