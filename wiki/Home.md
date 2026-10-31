# Local Mind

Local Mind runs language models on your own Mac. Models are downloaded once and
answered locally; nothing in a conversation is sent anywhere to be answered.

This wiki documents how the app chooses models and engines, and what each
catalogue entry has actually been shown to do.

## Pages

- **[Model catalogue](Model-catalogue.md)** — what a catalogue entry records, how
  downloads are verified, and what the *unevaluated* badge means.
- **[Inference engines](Inference-engines.md)** — how an engine is selected, and why a
  tuned build is offered under its own name.
- **[Nemotron 3.5 Lightning](Nemotron-3.5-Lightning.md)** — a 30B mixture-of-experts
  model with mamba blocks, in development.

## How to read availability on this wiki

Every page says plainly whether something is in a released build, in a TestFlight
build, or only on a development branch. These are three different things and the
pages do not blur them. A feature described as *in development* is not available
to download, and no date is promised for it.

Likewise, no page quotes a speed figure without naming the model and
quantisation, the machine, the storage the weights were read from, the engine and
the context length, and saying how it was measured. Where a figure in the app is
modelled rather than measured, the page says so.
