# Nine Ways Inference Benchmarks Lie

### A field guide to unreliable numbers — and the discipline that catches each one

*Effiq · October 2026*

---

Every week someone publishes a number: "2.4× faster inference," "40% cheaper serving," "beats GPT-4 on our suite." Most of these numbers are produced by people who are not trying to deceive anyone. That is precisely the problem. Inference benchmarking is full of traps that catch honest engineers, and an incentive structure that rewards the ones who fall in — because the trap usually makes your result look better.

We run pre-registered inference benchmarks for a living. Below are the nine failure modes we design against, in roughly the order they corrupt a result: from what you measure, to what you measure it on, to how you report it. Each one comes with its tell — the detail that should make a reader suspicious — and its antidote, which in every case is a piece of discipline, not a piece of cleverness.

Everything we claim about our own process is backed by public repositories: a [methodology](https://github.com/effiq/protocol), a [complete run with a published failure](https://github.com/effiq/practice), and this essay's claims are checkable against both.

---

## 1. The cherry-picked workload

**The lie:** The benchmark measures the workload where the optimization wins.

Speedups are workload-dependent at a deep level. A technique that accelerates long, repetitive generation may do nothing for short answers. Batch-friendly optimizations shine at high concurrency and vanish at batch size one. Prompt processing (prefill) and token generation (decode) have different compute profiles — a method that speeds up one can be neutral or negative on the other.

Pick your workload after you know your optimization, and you will, without lying once, produce a number that does not generalize.

**The tell:** A single benchmark task, chosen by the party being measured. No sensitivity analysis ("results hold across prompt lengths from 500 to 8,000 tokens"). No statement of what the workload was designed to represent.

**The antidote:** Fix the workload *before* touching the candidate optimization, in writing. In our protocol, the workload is generated from a fixed seed and frozen alongside the hypothesis. If the workload turns out to be unrepresentative, that is a finding — you report it and design a new experiment, you do not silently swap tasks.

---

## 2. The unverifiable artifact

**The lie:** "Q4 quantization of model X" — but the weights were never checked.

Model artifacts travel. They get re-uploaded to mirrors, converted, re-quantized, cached by CDNs, and corrupted along the way — silently, because a corrupted GGUF file usually still *loads*; it just produces degraded output. We know this because it happened to us: every reachable mirror of a baseline artifact was serving the same corrupted file, byte for byte. The model still answered questions. It answered them *slightly wrong*, which is the worst kind of corruption — invisible unless you check.

If you benchmark against a corrupt baseline, your candidate wins by default. If you benchmark a corrupt candidate, your conclusion inverts. Either way the number is fiction.

**The tell:** No SHA-256. No publisher reference. "Downloaded from a mirror." A quality result that looks *suspiciously* degraded, shrugged off.

**The antidote:** Hash everything, at download time, against the publisher's official record — before any data is collected. If the hash does not match, you stop. This is Step 0 of our re-run checklist, not a footnote. When we had to switch a baseline artifact for this exact reason, the switch was logged as a dated deviation *before* measurement, with the evidence attached. A benchmark you cannot verify at the artifact level is not a benchmark; it is a rumor with decimals.

---

## 3. The point estimate

**The lie:** "1.31× faster" — measured once, or averaged, with no uncertainty attached.

Wall-clock inference times on shared, thermally constrained, cache-sensitive hardware are noisy. Run-to-run variance of 10–20% is routine; variance that flips your conclusion is common. A point estimate from noisy data is not a measurement of the system — it is a measurement of *that afternoon*.

Here is a concrete example from our own published work. We compared two quantized variants of the same model under identical serving settings, six interleaved runs, on a decision rule requiring the lower bound of the 95% confidence interval to clear 1.20×. The measured ratio was 0.909× with a 95% CI of [0.629, 1.301]. Read that interval: it contains "the candidate is slower," "the candidate is identical," and "the candidate is comfortably faster." **No honest conclusion was available.** The correct output of that experiment is not a number; it is "insufficient evidence."

**The tell:** No confidence intervals. No run count. Averages reported to two decimal places.

**The antidote:** Pre-register a statistical decision rule, then obey it. Interleave the arms (run A, B, A, B…), never batch them, so drift hits both sides equally. Bootstrap the ratio, report the interval, and let the *lower bound* — not the point estimate — do the talking. And when the interval straddles 1.0, publish that. We did: our first public run failed its own decision rule, and the failure is in the repository next to everything else. A number you cannot distinguish from its own noise is not a speedup. It is a coin flip you have decided to call.

---

## 4. The moving goalpost

**The lie:** The pass bar was decided after the data arrived.

"We expected 1.2×… we got 1.08×… which is basically a pass, so we're calling it a pass." Variants: switching the primary metric after the fact (throughput was the target until latency looked better); redefining "representative workload" mid-analysis; promoting a secondary metric to primary because it crossed.

This is the most damaging lie on the list because it is invisible in the final artifact. A reader sees a number and a conclusion. They cannot see that the conclusion was reverse-engineered from the number.

**The tell:** No pre-registered decision rule. Language like "we consider this a meaningful improvement." Thresholds that appear for the first time in the results section.

**The antidote:** Commit to the decision rule in public, with a timestamp, before measurement: the hypothesis, the metric, the threshold, the handling of missing or corrupted data. Deviations are allowed — real runs always surprise you — but each one must be dated, written, and appended to the record *as a deviation*, and the original rule stays visible. In our run, the pass bar (≥ 1.20× on the lower confidence bound) was locked before a single request was served. The measured 0.909 did not clear it. That is the entire point of locking the rule: it cannot be bargained with after the fact, including by us.

---

## 5. The asymmetric setup

**The lie:** Both arms were "the same setup" — except for the seven things that weren't.

Candidate gets batch size 8; baseline gets batch size 1. Candidate's server is warmed; baseline's is cold. Different context lengths, different sampling parameters, different thread counts, different software versions. Sometimes the asymmetry is innocent — the default flags changed between the two tutorials the engineer followed. Sometimes it is the entire effect.

**The tell:** Serving configurations listed for one arm only. "Standard settings." Different llama.cpp / vLLM / TensorRT-LLM versions across arms, with no justification.

**The antidote:** One configuration, applied to both arms, specified to the flag, in the pre-registration. Same context length, same parallel slots, same sampling parameters, same server binary and version — ideally verified from the execution log, not from memory. If the candidate *requires* different settings to show its advantage, that is a legitimate and interesting result — but then you say so explicitly and price the difference into the claim. "Faster, if you also change everything else" is a different claim from "faster."

---

## 6. The free-lunch speedup

**The lie:** Quality is assumed, not measured.

Quantization, pruning, distillation, speculative shortcuts — most inference optimizations trade a little model quality for a lot of speed. Sometimes the trade is excellent. Sometimes the model loses 5 percentage points on exactly the tasks you care about while gaining 30% throughput. Benchmarks that report only the speed side are reporting half of a transaction.

The trap is subtle: quality loss is workload-dependent, and a few-shot benchmark may not expose it, while your customers' prompts will. The faster model that quietly answers "I don't know" more often is not cheaper. It is worse.

**The tell:** No quality gate in the protocol. "Subjective testing showed no degradation" — from the team that built the thing.

**The antidote:** A pre-registered quality gate, evaluated on held-out tasks, with a quantitative threshold, run on *both* arms. In our protocol the gate is fixed before measurement: a defined question set, a scoring rule, a maximum allowed degradation, and the gate result published next to the speed result. Speed that fails the quality gate is not a speedup; it is a regression wearing one. Publishing the pair — "not faster, and quality held" or "faster, at a cost of X" — is the entire difference between marketing and engineering.

---

## 7. The cache and cold-start confound

**The lie:** The timing includes — or excludes — whatever makes the numbers prettier.

Time-to-first-token (TTFT) and steady-state decode throughput are different quantities with different sensitivities. Prompt cache hits change prefill cost by an order of magnitude. Model load time, warm-up passes, allocator behavior, and page-cache state all contaminate short benchmarks. A report that quietly mixes "first request after startup" with "warm steady state," or benchmarks long generations for one arm and short ones for the other, is measuring its own methodology.

**The tell:** No statement of what the timer covers. "End to end" without a definition of either end. Warm-up runs mentioned only in the footnote that excludes them from the average.

**The antidote:** Define the timer in the pre-registration: what is measured (per-request wall time? TTFT? tokens per second after warm-up?), what is excluded (startup, warm-up) and how those are handled symmetrically across arms. Discard the first pass explicitly, or include it explicitly — but decide before you see which choice flatters you. Our own metric was deliberately unglamorous: completion tokens per request wall, everything logged per request, every raw file published. Boring measurement is a feature.

---

## 8. The hardware flex

**The lie:** "Runs great on modest hardware" — modest by whose standards?

An undisclosed GPU, an undisclosed cloud instance, a laptop from the future. Hardware claims without environment disclosure are unverifiable by construction, and cost claims without hardware and energy accounting are worse: "10× cheaper" usually means "10× cheaper on the one machine where we ran it, amortizing nothing."

**The tell:** No environment section, or one that says "consumer GPU." Price claims with no machine, no utilization assumption, no cost basis.

**The antidote:** Publish the environment like you publish the code: CPU/GPU model, memory, OS, server binary and version, driver stack. If the claim is about cost, publish the cost model — instance price, utilization assumption, tokens served per dollar under a stated load. A speedup on hardware the reader cannot identify is a speedup they cannot plan around, and a cost claim without a cost model is astrology.

---

## 9. The survivor's report

**The lie:** You are reading the one run that looked good.

Ten runs happened. Runs 1, 2, 4, 6, and 9 are in the trash. The blog post describes run 7. Nothing in the artifact tells you this, because the artifact was written after the cull. Publication bias does not require malice — it only requires that ugly runs be easier to lose than pretty ones, which is true everywhere.

**The tell:** A beautiful result with no execution log. Run counts that do not add up. "Representative run shown."

**The antidote:** Write the log *during* execution, not after: every run, every arm, every timestamp, every artifact hash, every server start — kept whether it flatters you or not. Interleaving helps here too, since it makes selective deletion structurally harder: A/B/A/B records are hard to carve into a winner without leaving a wound. Then publish the log with the report. Our first public run came with six interleaved runs' worth of raw JSONL and summaries, and its conclusion — a failed decision rule — survived because the log could not be quietly fixed. The log is not supporting documentation. The log *is* the result.

---

## What this adds up to

None of the nine requires genius to catch. Every one of them is defeated by the same move: **decide before you measure, and preserve everything while you do.**

That is the entire content of our methodology, and it is why we published it:

| Lie | Discipline that catches it |
|---|---|
| Cherry-picked workload | Workload frozen from a fixed seed before the candidate exists |
| Unverifiable artifact | SHA-256 against publisher records at download time, before data collection |
| Point estimate | Interleaved runs, bootstrap CI, pre-registered statistical decision rule |
| Moving goalpost | Decision rule committed publicly with a timestamp |
| Asymmetric setup | One serving configuration, specified to the flag, applied to both arms |
| Free-lunch speedup | Quantitative quality gate on both arms, threshold locked in advance |
| Cache / cold-start confound | Timer defined in writing; warm-up handled symmetrically |
| Hardware flex | Full environment disclosure, cost model for cost claims |
| Survivor's report | Timestamped execution log, written during, published always |

The uncomfortable corollary: this discipline will cost you victories. Our own first public run, executed honestly end to end, produced a number that failed our own bar, and we published it anyway — along with an artifact-integrity deviation that honest hash-checking caught before it could contaminate anything. That failure is not an embarrassment we tolerate. It is the product. Anyone can claim a speedup; the claim is cheap precisely because most benchmarks lie in at least one of these nine ways. What an engineering partner should sell you is not a number. It is the machinery that makes a number hard to fake.

The machinery is open. The run is open. The failure is open.

Everything we claim in public, you can re-run in private.

---

*License: CC BY 4.0. Reproduce freely with attribution.*

*Related: [effiq/protocol](https://github.com/effiq/protocol) · [effiq/practice](https://github.com/effiq/practice) · [effiq.tech](https://effiq.tech)*
