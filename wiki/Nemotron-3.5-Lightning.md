# Nemotron 3.5 Lightning 30B-A3B

> **Status: in development.** This entry is **not** in any released or TestFlight
> build of Local Mind, and no release date is promised. It is documented here
> because what has been verified about it is worth recording precisely.

A 30-billion-parameter mixture-of-experts model from NVIDIA, published by
EigenLabs as a 4-bit MLX pack. Local Mind catalogues that pack.

## What the catalogue records

| | |
|---|---|
| Format | MLX checkpoint directory, ten files |
| Total size | 18,544,223,673 bytes (about 18.5 GB) |
| Quantisation | 4-bit affine, group size 64 |
| Extra head | embedded multi-token prediction, one predicted layer |
| Trained context | 262,144 tokens |
| Licence | OpenMDW-1.1 |

Every one of the ten files is catalogued with its size and SHA-256 digest, taken
from the publisher's repository at a pinned revision.

## What has been verified

The pack was downloaded and checked end to end:

- **All ten digests matched**, and their sizes sum to exactly the recorded total.
- **A stock MLX server loaded the directory** and began serving. Of the pack's 763
  tensors, **34 belong to the multi-token-prediction head**, and these are
  tolerated rather than rejected as unexpected keys.
- **It answered a prompt correctly**, with its reasoning content separated from
  the answer — which also confirms that the reasoning format is derived correctly
  from the chat template recorded in the catalogue.
- The required pre-weight file set was **measured rather than assumed**: given a
  directory holding only the configuration file, the server's first complaint is
  about a missing weight tensor, so no auxiliary transform file is needed. Some
  packs do require one, and that failure mode would have appeared here.

## What is *not* claimed

**No decode or prefill speed.** The one run to date was a single request, on a
machine concurrently serving two other models, with the weights on an external
USB volume. Those conditions move throughput by far more than any difference
worth reporting, so the entry keeps its
[unevaluated badge](Model-catalogue.md#the-unevaluated-badge) and every speed shown
for it is modelled from the model's structure rather than measured.

A measurement will replace this section when one exists in the shipped
configuration, on a quiet machine, with the weights on internal storage.

## Engine

The model declares a tuned MLX engine by name; see
[Inference engines](Inference-engines.md). Performance work for this architecture
takes place in a public optimisation track, and no standing or score in that track
is claimed here.
