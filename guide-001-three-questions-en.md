# Three Questions to Ask Before Buying Inference Optimization

![evidence-grade](https://img.shields.io/badge/evidence%20grade-E4-blue)
![recomputable](https://img.shields.io/badge/recomputable-one%20command-green)
![fails-public](https://img.shields.io/badge/FAILs-public-orange)
![open-logs](https://img.shields.io/badge/raw%20logs-open-lightgrey)

**The inference-optimization market has an open secret: plenty of claims, very little evidence.**

Quantization, speculative decoding, scheduling — every vendor promises 2×, 4×, 10×. Yet almost none of these numbers ship with confidence intervals, paired statistical tests, or raw logs. And nobody publishes the experiments that failed.

The good news: you don't need to be a performance engineer to tell real from fake. Three questions at procurement time will do.

---

## Question 1: What's the *floor*?

**Don't ask "how fast at best" — ask "what is the lower bound of the 95% confidence interval?"**

A "peak 4×" figure can be manufactured easily: pick the friendliest input length, turn off concurrency, run a hundred times and keep the best one. Under your production traffic it may collapse to 1.2×.

| A good answer sounds like | Red flags |
|---|---|
| "Paired-bootstrap 95% CI [1.43, 2.01] — **the floor is 1.43×**, measured on samples of your load shape" | "Up to 4×", "typically 3×" — no interval, no test method, no load definition |
| States the input-length distribution, concurrency, and traffic model used | A single bar chart with an unexplained X axis |

> Our own published format is mandatory, e.g. from one experiment: two configurations of the same model at **2.33× (peak, with a disclosed quality trade-off)** and **1.63× with a 95% CI floor of 1.43× (quality gate passed)**. The first number is "how fast at best"; the second is "what you can count on." When you buy optimization, you are buying the second.

## Question 2: Can I recompute it?

**Don't listen to explanations — ask for raw logs and one recompute command.**

A serious optimization deliverable includes per-request raw timing logs, the pairing structure, random seeds, and a `--verify` entry point — so that you (or any third party you hire) can regenerate every number in the report from raw data with a single command on your own machine.

| A good answer sounds like | Red flags |
|---|---|
| "Logs, seeds, and recompute scripts are in a public repo — verify any time" | "The data is commercially sensitive", "our internal platform doesn't support export" |
| Recomputation doesn't require the vendor in the room | Verification requires logging into the vendor's closed system |

A number that can only be reproduced on the publisher's machines is not a measurement. It's an advertisement.

## Question 3: Have you ever published a FAIL?

**This is the most lethal of the three.**

Every team doing real optimization has failed — optimization is a bet on complex systems, and nobody wins every bet. A vendor that has never published a failure is one of two things: it hasn't run many real experiments, or it only shows you the wins.

| A good answer sounds like | Red flags |
|---|---|
| "Yes — here's the full record of the experiment where we missed our own bar, including raw judge data" | "All our solutions are thoroughly validated" (= never fails = never tells the whole truth) |
| Failures published under the same criteria as successes | Vague about failures, eloquent about successes |

A vendor willing to publish its FAILs has PASSes that actually mean something — because the same gate has ruled against it.

---

## The Evidence Ladder: score your vendor

| Grade | Standard | What it means |
|---|---|---|
| **E0** | Pure claim | "We're N× faster", no data at all |
| **E1** | Numbers, no statistics | Benchmark charts without CIs, pairing, or load definitions |
| **E2** | Statistics present | CIs and test methods reported, but data not inspectable |
| **E3** | Recomputable | Raw logs, seeds, and scripts public; third parties can independently regenerate results |
| **E4** | Pre-registered + failures public | Pass bars frozen and hash-sealed before execution; FAILs disclosed on equal footing with PASSes |

E0–E1 is marketing material; E2 is where measurement starts; E3 is where evidence starts; **E4 is the only grade you can write into a capacity plan.**

Next time a vendor pitches you inference optimization, send them this table and ask: which grade are your numbers?

---

> [!IMPORTANT]
> Every standard in this guide is one we apply to ourselves first: all experiment protocols are frozen and hash-published before execution, all raw logs and recompute scripts are open, and our published record includes one complete FAIL. Procurement standards shouldn't come in two versions — one for vendors and one for ourselves.

---

*Effiq · Public experiment archives: github.com/effiq (full raw logs, pre-registered protocols, and recompute scripts)*
*中文版本：[买推理优化之前，先问三个问题](./guide-001-three-questions-zh.md)*
