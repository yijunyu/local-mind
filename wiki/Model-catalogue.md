# Model catalogue

Each entry in Local Mind's catalogue records exactly which files make up a model,
how large each one is, and the SHA-256 digest of each. A download is checked
against those digests before the model is offered, so a truncated or substituted
file is refused rather than loaded.

## Two shapes of model

- **Single-file GGUF.** One file holds the weights and the metadata.
- **MLX checkpoint directory.** A folder of configuration, tokenizer and
  safetensors shards that are loaded together. All of the files are catalogued,
  and a missing one is a failure, not a silently degraded load.

For a checkpoint directory, only the files the engine actually reads are
catalogued. Licence text and README prose are deliberately left out, because the
engine never reads them, and including them would make the recorded total
disagree with the bytes that must be present to load.

## The *unevaluated* badge

An entry carries this badge when the app can describe the model but has not
measured it in the configuration it ships. While the badge is present:

- any speed shown for that entry is **modelled from the model's own structure**,
  not measured, and
- the entry is still fully specified — file list, sizes, digests, context length
  and licence are recorded facts, not estimates.

The badge is removed when a measurement exists in the shipped configuration on a
machine that was not doing other work at the time. A single request on a busy
machine, or weights read from an external disk, does not clear it; those
conditions move the result by far more than the differences worth reporting.
