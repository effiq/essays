# After the 2.33×, we published our own FAIL

![protocol](https://img.shields.io/badge/protocol-frozen_before_execution-1f6feb)
![verdict](https://img.shields.io/badge/verdict-FAIL_·_published_in_full-d73a49)
![statistics](https://img.shields.io/badge/bootstrap-paired_·_seeded_·_100k-6e7781)
![evidence](https://img.shields.io/badge/raw_logs-public_·_recomputable-2da44e)

Look at this set of numbers first.

Throughput up **2.33×**, paired-bootstrap 95% CI [1.6691, 4.1199], statistically sealed. A tuned repair build: measured mean another **63%** faster, **2.67×** in the busiest hour, and the tail of first-token latency at peak down to **one-sixth** of the unquantized baseline. Blind quality judging passed under all three judges — including the one who issued the original red dot, who personally revised clarity from −0.127 to **+0.033**.

You can recite what any inference-optimization company does with numbers like these: the homepage banner becomes "up to 2.3× faster," sales starts booking calls, and 2.33 gets the biggest font in the deck. We have searched the literature on how this industry reports numbers — selective submission is open practice, a single run passed off as a conclusion, the same weights wearing a different face under a different harness. Third-party benchmarking exists as a business precisely because both sides assume vendor numbers need discounting.

**We didn't change the banner. We published a FAIL.**

| Gate | Result | Headline number |
|:---|:---:|---|
| Quality (single-shot confirmation; three-judge panel, mandatory red-dot judge seat) | **PASS** | worst judge +0.102 (tolerance −0.1); the red-dot judge reversed −0.127 → **+0.033** |
| Performance (pre-registered bar: CI lower bound ≥ 1.50×) | **FAIL** | point estimate **1.6344×**, paired-bootstrap 95% CI **[1.4328, 2.0062]** — short by 0.067 |

---

## A distance of 0.067

The task was red-dot repair: take the publicly published quality red dot from the previous essay (clarity −0.127) and fix it with a tuned FP8 configuration — keep the most sensitive attention layers in high precision, quantize the rest as before. The rules were frozen before the window opened: **both** a quality gate and a performance gate must pass to win, and the performance bar was a conservative lower bound of **1.50×**.

The result: the quality gate passed, cleanly. The performance number is — point estimate 1.6344×, paired-bootstrap 95% CI **[1.4328, 2.0062]**.

Bar 1.50, lower bound 1.4328. **Short by 0.067. FAIL.**

Notice what's missing from our option list — no "run two more rounds and see," no "move the bar to 1.40," no "quote the mean externally." The protocol does not negotiate once fired, including with us. The 1.50 line was not imposed on us; we pinned it ourselves before a single data point existed: **an optimization below 1.50× is not worth a customer's signature** — 1.20× can only promise 17% time saved; 1.50× promises a third. Those are two different businesses.

## Why we speak in lower bounds

Because the mean is the average of luck, and the lower bound is the promise you keep when luck runs out.

Across six measurement windows, five quiet hours ran at 1.38–1.63×, and the busiest hour spiked to 2.67× — and that outlier high score is exactly the culprit that widens the interval: the n=6 paired bootstrap sees that variance and drags the conservative end down. In marketing language, 2.67× is "peak performance"; in contract language, 1.4328× is the only number you can write into an SLA.

That is the way of thinking we want to sell you: **your capacity planning should be staked on the lower bound, not the mean.** Whoever sells you the mean is transferring the risk of your luck onto your bill.

## Was the red dot repaired? Yes — with witnesses

What failed was the performance gate, not the repair. The evidence, in full:

- **Screening phase:** the control arm — the exact configuration that issued the red dot — reproduces the breach on the screening set (−0.250). The instrument is sensitive; we didn't fix the instrument.
- **Confirmation phase:** the frozen 150 items, three judges, a mandatory seat for the original red-dot judge, at least two vendor families — all three rules are frozen text. 450/450 judgments completed; worst judge +0.102, every axis back within tolerance.
- The repair recipe and checkpoint are hash-anchored; the same checkpoint was rebuilt three times, byte-identical.

The cost is written into the verdict just as honestly: exempting attention layers eats compute — in the five quiet windows the tuned build is measurably slower than pure FP8, and its escape capability in the busiest hour is visibly weaker. That mechanism is currently only a hypothesis, not isolated by experiment; we labeled it as such and did not smuggle it into a conclusion.

## So what can we sell now

The FAIL kills one advertising slogan, not the product. Per the pre-registered type-丙 route, both configurations ship:

- **Repaired build:** red dot fixed, floor of **43%** (CI lower bound 1.4328×), mean 63% — for workloads with a strict quality line.
- **Speed build:** **2.33×** point estimate, CI [1.6691, 4.1199], with the clarity red dot (−0.127) disclosed as-is — for throughput-first workloads with quality headroom.

Two configurations, two envelopes, both chained to raw logs that anyone can recompute with one command. Whichever configuration you pick depends on where you draw your quality line — that is not a compromise; it is what optimization engineering was always supposed to deliver.

## Don't trust us — recompute us

> [!IMPORTANT]
> The full evidence chain is public: the frozen protocol and its four execution addenda, the raw logs of the eight-arm screening, 450 raw judge responses, per-request data from all six performance runs, and three verdict documents. Three `--verify` commands, no network, no GPU — every number recomputed by your own hands from the raw logs, **including the PASS and including the FAIL.**

We also rehearsed the most disappointing path: if this FAIL is followed by one more FAIL, our kill-page triggers a full review. The rules bind us too — which is exactly why you can trust them.

---

**Optimization is the product; measurement is the moat. The floor is worth more than the peak.**

---

*Evidence chain: protocol, addenda & scripts — github.com/effiq/pilot003 ｜ all raw logs — github.com/effiq/pilot-logs ｜ verdict: VERDICT.md ｜ reproduction: REPRODUCE.md ｜ previous essay: We replayed real production traffic at FP8: the 2.33× held — "no measurable quality loss" didn't*

*中文版本：《2.33× 之后，我们公开了自己的 FAIL》*
