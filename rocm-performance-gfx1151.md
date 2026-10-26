# ROCm, Vulkan and PolyStrata on gfx1151

What the AMD column of this page is measuring, and why the backend that wins keeps
changing. Measured on the Strix Halo box (Ryzen AI Max+ 395, Radeon 8060S, `gfx1151`,
128 GB unified memory).

The short version: for **dense** models, decode here is pinned by memory bandwidth, and
the backend is worth about 7%. For **MoE** models and for **prefill** the backend matters
much more, and it has no single winner. The one change that is worth more than any
backend is speculative decoding, and only on the right workload.

## Dense decode is a bandwidth line

A dense model must read every weight for every token, so its decode rate is bandwidth
divided by file size and nothing else. Multiply this page's dense rows out and they land
on top of each other:

| Model | Size | Decode | Implied read rate | Backend |
|---|---:|---:|---:|---|
| Qwen3-8B | 4.7 GiB | 38.2 tok/s | 193 GB/s | ROCm |
| Qwen3-14B | 8.4 GiB | 22.1 tok/s | 199 GB/s | ROCm |
| Muse Glimmer 30B | 15.6 GiB | 11.5 tok/s | 193 GB/s | ROCm |
| Qwen3.8-27B | 16.2 GiB | 11.0 tok/s | 191 GB/s | ROCm |
| Qwen3-32B | 18.4 GiB | 9.7 tok/s | 192 GB/s | ROCm |
| Qwen3.8-27B | 16.3 GiB | **11.74 tok/s** | **206 GB/s** | Vulkan |
| Nanbeige4.3-33B | 19.4 GiB | **10.33 tok/s** | **215 GB/s** | Vulkan |

Six dense models spanning 8B to 33B, and every ROCm row is within 4% of 193 GB/s. That
is not six coincidences; it is the memory system, which measures about 215 GB/s sustained
on this box against the M1 Ultra's 800. It is why the Macs lead this page on dense decode
and why no amount of kernel work will change that ordering.

The practical use of the line is prediction: a dense model's decode rate here is knowable
before you download it. Divide ~200 GB/s by the file size. Quantizing smaller is the
lever that moves it, because it changes the divisor; switching runtimes is not.

## The backend is worth about 7% on dense decode

The last two rows are the same instrument on the Vulkan backend. Qwen3.8-27B is the
controlled pair — 16.2 GiB on ROCm against 16.3 GiB on Vulkan, near enough the same file:

| Backend | Decode | Implied | Prefill (pp2048) |
|---|---:|---:|---:|
| ROCm, llama.cpp `3173a56` | 11.0 tok/s | 191 GB/s | not published |
| **Vulkan, llama.cpp `2881e5b8`** | **11.74 tok/s** | **206 GB/s** | 344.6 ± 4.0 |

+6.7% for Vulkan, which puts dense decode at 96% of the sustained ceiling where ROCm sits
at about 89%. The builds differ as well as the backends, so some of that gap may be nine
builds of llama.cpp rather than the API.

## On MoE, the backends trade places by phase

Identical weights, identical flags (`-fa -b 8192 -ub 2048`), MiniCPM-V 4.7 35B-A3B,
256-token decode:

| Backend | Decode | Prefill |
|---|---:|---:|
| ROCm/HIP llama.cpp | 58.0 – 58.4 | 1,255 |
| **Vulkan llama.cpp** | **68.2 – 68.9** (median 68.6) | 1,127 |
| PolyStrata (HIP) | 61.4 | ~1,055 |

Vulkan is +18% on decode over ROCm and +12% over PolyStrata, but −10% on prefill against
ROCm. Interactive serving is decode-dominated, so production on this box moved to Vulkan
on 2026-10-17. PolyStrata is retired: Vulkan beat it on both phases, which left it no
argument.

An independent measurement on DeepSeek V4 Flash points the same way, Vulkan ahead on both
phases there (231.8 prefill / 18.9 decode against ROCm's 207.9 / 14.8).

MoE is also where this hardware looks good at all: 56–69 tok/s, because only a fraction
of the weights are read per token. The bandwidth line above is a dense-model constraint,
not a property of the box.

## Speculative decoding is worth more than the backend question

Since dense decode is bandwidth-bound, the only large win is to stop reading the weights
once per token. n-gram lookup drafting needs no draft model — the candidates come from
the prompt — and on Nanbeige4.3-33B it moved decode from 10.1 to **37.9 tok/s**, 3.7x,
with 249 of 249 drafted tokens accepted on an "edit this file" prompt. No backend change
on this hardware has produced anything close to that.

It is workload-shaped, not universal. On free-form prose the same flag gives 10.85 against
10.14: it gains nothing when the lookup misses, and costs nothing either. So it suits
agentic and coding work, where most output tokens already appear in the input, and it is
irrelevant to chat.

It also costs bit-exact reproducibility. Greedy output was byte-identical to
non-speculative decode on copy-heavy prompts and diverged on prose — the same claim in
different words, from the numerics of verifying a batch of drafted tokens instead of one
at a time. Worth checking per model rather than assuming either way.

## What to take from this

* Predict a dense model's decode before downloading it: ~200 GB/s ÷ file size.
* Prefer Vulkan on this box today, by about 7% dense and 18% MoE on decode, at ~10% of
  prefill on MoE.
* Measure the backend per model and per phase. The answer has changed three times here.
* For coding workloads, turn on n-gram drafting before considering anything else.
* vLLM and SGLang are not obviously wrong, but they answer a different question: they win
  on batched concurrency, where this box serves one stream at a time. SGLang has no stock
  gfx1151 lane at all.

## Instruments

Decode figures are llama.cpp `llama-bench`, `tg128`, `r=3` — the same instrument and token
count as the M1 Ultra rows, so those columns compare. Backend and build are stated per row
because they differ. Sizes are as `llama-bench` reports them, in GiB. The MoE comparison
is a 256-token server measurement, not llama-bench. The 3.7x drafting figure is a server
measurement over 320 generated tokens, because llama-bench does not drive speculative
decoding.
