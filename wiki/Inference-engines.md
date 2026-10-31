# Inference engines

Local Mind can serve a model through more than one engine. Engines are selected
**by name**, and a catalogue entry may declare which engine serves it.

## Why a tuned build gets its own name

An engine tuned for one model architecture is offered as its own engine identity
rather than as a switch on the general-purpose one. Two reasons:

- A catalogue entry can then *name* the engine it expects, so the right code path
  is chosen from the model rather than from a user setting that happens to be on.
- When that build is not installed, the app can say so and fall back, instead of
  starting the general engine and reporting whatever it produces as though the
  tuned path had run.

The second point is the important one. An engine selected by a flag on another
engine cannot report its own absence; an engine selected by name can.

## Availability

Engine availability depends on which builds are installed on the machine. The app
shows an engine as available only when the binary that implements it is present.
The named-engine mechanism described here is **on a development branch and is not
in a released or TestFlight build**.
