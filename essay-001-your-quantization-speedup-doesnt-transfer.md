# Your quantization speedup doesn't transfer to your workload

You've read those posts: *"FP8 quantization: 1.5× faster, measured."* *"Speculative decoding doubles throughput."* Beautiful numbers, polished charts. You deploy it — and you get 8%. Or you get 40% and your outputs start talking nonsense.

That's not your fault. It's this: **the number was only ever true for the workload it was measured on.** It was true under assumptions nobody stated to you — a particular context length, a particular request mix, a particular output-length distribution, a particular GPU. Your workload is not that workload, so for you the number was never a fact. It was an advertisement.

We're an inference-optimization company. Industry custom says this is where we show you our fastest chart. Instead we did something more laborious: **we wrote the rules down, froze them, published them — and then let the data speak, including the data that killed two of our own candidate techniques.**

This is Pilot 001.

## Measure first, aim later — in reverse

Before any GPU was switched on, we froze and published a measurement protocol (Protocol Lock v1.0): the workload generator, the success criterion (bootstrap CI lower bound ≥ 1.20×), the statistical method, the budget, the 30-day stop-loss. After freezing, **changing the rules requires a public, hash-anchored amendment.**

Then we tried the three loudest techniques in the industry. Two died:

**ngram speculative decoding — killed by measurement.** The protocol pre-registered a checkpoint: an offline simulation acceptance rate of ≥ 0.50 to earn a place in formal measurement. On our declared workload, the measured acceptance was **0.134** (0.145 at 4K / 0.154 at 8K / 0.105 at 16K). Off by nearly 4×. Had we measured performance first and checked acceptance later, you might today be reading a post rationalizing why ngram "didn't fit our workload." The point of pre-registration: **the narrative never gets a head start on the data.**

**EAGLE speculative decoding — killed by ecosystem reality.** There is no official EAGLE head for Qwen2.5-14B. A community head exists, but its architecture (`Qwen2ForCausalLMEagle`) is not in vLLM's speculative-decoding registry — it's SGLang-only. Inside our locked boundary (engine built-ins only, no custom kernels), it simply cannot be deployed. That's not a performance failure; it's "your production stack can't even load it" — a death that benchmark posts never show you.

Both amendments (Arm B redefined as pure FP8; interleaving replaced by block-crossover) were signed and published with **zero formal measurement data in existence**. Then FP8 dynamic quantization went on trial.

## The survivor lives in a small world, too

Six formal runs, two machines, two days, 27 paired requests per run, the confidence interval sealed inside an independent verdict script until all six runs were complete. The result:

**FP8 vs BF16 = 1.419×, 95% CI [1.387×, 1.442×], lower bound 1.387 ≥ 1.20 — PASS.**

But look at one number we could have hidden: in run 2, the 4K tier's descriptive speedup was **1.07×** — essentially nothing. The same tier in run 1, same night, was 1.47×. Had we measured for a single night and published, you might have read *"FP8 doesn't help on long context"* — or *"FP8 is 1.47× faster"* — **both "data-backed," both false.** A single-point measurement isn't a measurement; it's a raffle ticket. That's why the protocol demands six runs and a paired bootstrap: 1.419 is the number that survives the noise.

The quality half got no luck either: a frozen 150-item evaluation set, both arms answering every item, scored by a third-party judge (DeepSeek-V3-0324) blind to arm identity — the blind assignment itself is seed-fixed. All three axis deltas came out positive (+0.21 / +0.18 / +0.05), but the descriptive confidence interval crosses zero. So our conclusion is worded as **"no measurable quality loss" — not "FP8 is better."** The first is what the evidence supports; the second isn't. Incidentally, all 150 output pairs diverged textually — every single word different, blind-judged equivalent. Which is exactly why "quantize and pray" is not engineering.

## Why everyone else's benchmark must mislead you (including ours)

A speedup is not a number; it's a function. The three workload factors — context length, request form, output-length distribution — each move the bottleneck. In our own data, the 4K and 16K tiers sped up differently (1.428× vs 1.397×). Change the GPU, change the model, enable prefix caching, add concurrency — any one of these can flip the conclusion.

So vendor numbers aren't lies, exactly. They're **true in another world.** The answer for your world can only come from your own measurement.

## Don't trust us. Recompute us.

Every claim above sits on a public evidence chain: the frozen protocol, both amendments, every script, the raw logs of all six runs, the judge's verbatim per-item responses — two repositories, fully public. Anyone can clone them, run three `--verify` commands, and recompute our verdict from the raw logs by hand. If your numbers differ from ours, the logs you cloned differ from the ones we published — catching that is the point of the design.

Pilot 001's conclusion is true in one small, precisely declared world: a single L40S, Qwen2.5-14B-Instruct (weights pinned by revision hash), vLLM 0.31.0, our declared workload. **Outside that world we guarantee nothing — including to you.**

That is the whole claim: optimization is the product; measurement is the moat. Whether your quantization speedup holds on your workload is a question no one can answer for you — but measurement can.

---

*Evidence chain: protocol & scripts — github.com/effiq/pilot001 ｜ all raw logs — github.com/effiq/pilot-logs ｜ verdict: VERDICT.md ｜ reproduction: REPRODUCE.md*

*中文版本：《你的量化加速为什么在你的负载上不成立》*
