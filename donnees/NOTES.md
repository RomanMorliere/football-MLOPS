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
To fill in: chosen TRAIN_MAX_STEPS, best prompt, validation scores, test scores
(LoRA / base / base+prompt), cross-test score.

## Test reads
To fill in (the `lectures_test` counter in the report). Any change to the data
after reading the test will be noted here.

## Gap between our test and the cross test
To fill in once the score comes back.
