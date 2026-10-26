# We replayed real production traffic at FP8: the 2.33× held — "no measurable quality loss" didn't

The last essay ended with a written promise: outside Pilot 001's small world — one L40S, one hash-pinned model, one declared synthetic workload — we guarantee nothing. The question readers asked most: **what about real traffic?**

This time we did the laborious thing. We took Microsoft's public Azure LLM inference trace (two production services, real arrival timestamps), cut a week of windows out of it, froze the subsampling rule in a signed amendment, and replayed the identical arrival process at two arms on a single L40S: BF16 and FP8. Odd and even runs alternate which arm goes first; requests are paired one by one; the decision rules were frozen before a single measurement existed.

The results first — one good, one bad, both verbatim from the sealed verdicts:

- **Performance: FP8 = 2.3279× throughput, paired-bootstrap 95% CI [1.6691, 4.1199] — PASS.**
- **Quality: blind-judged clarity-axis delta −0.127 against a pre-registered tolerance of −0.1. A breach of 0.027 — FAIL.**

## The 2.33× doesn't live in the average — it lives at the knee

On the quieter hours the per-run speedups are unspectacular: paired log-ratios around +0.45 to +0.72, i.e. one-and-a-half to two×. The story is the heaviest hour: the real evening peak arrives, and BF16's queue collapses on contact — pooled P95 time-to-first-token **37.272 s**, versus **1.861 s** on FP8; that run's log-ratio is +2.238, nearly nine×.

This is what synthetic workloads never measure: **the value of quantization is not "faster on average" — it's that the capacity knee sits at a different place.** The same flood hits both arms; one arm's tail latency sails to seven× the SLO reference line while the other doesn't flinch. If you plan capacity to BF16's knee, a peak means buying more GPUs. Plan to FP8's knee, and you buy nothing. For a compute bill, the knee's location is worth more than the average speed.

Honest disclosure: the trace peaks at roughly 50× what a single L40S can serve, so the replay is thinned and compressed under a frozen rule (Amendment-01, signed with zero measurement data in existence); both arms face byte-identical arrivals. We measured relative ratios and the shape of the knee — not absolute production QPS.

## How the FAIL was handled

The rule was frozen: the overall delta and every per-axis delta must all be ≥ −0.1. Clarity came in at −0.127, over the line by 0.027.

Notice what's not on our menu: "have the judge re-score it," "move the bar to 0.15," "try a different set of items." **The point of freezing the rule is that it doesn't negotiate when it fires — including at us.** The FAIL went into the record, the verdict document is public, and it was published no later than a PASS would have been.

## Then we went and doubted our own judge

Filing the FAIL didn't end the questions. We chased it on two levels. First, post-hoc analysis (explicitly labeled: descriptive, verdict unchanged): the same judge model, scoring **the same arm-A answer sheets** two days apart, drifted by 0.67–0.91 points; and the per-item score damage across our two pilots correlates at −0.002 — **if FP8 truly hurt particular items, the wounds should overlap. They don't overlap at all.**

Second, a formal follow-up: a separate pre-registration (frozen before execution) handed the sealed 150 answer pairs, presentation order untouched, to an independent judge panel. Result: **every axis delta from both panel judges came out positive** — clarity at **+0.127** and **+0.193** — same day, same model family, same answer sheets, sign-flipped against the original judge's −0.127. Panel outcome: **the original FAIL is judge-fragile, consistent with single-judge noise.**

One number that made us blink: the two new judges graded both arms about 1.5 points lower overall. Judges don't even share a scale — which is why our protocol only ever trusts within-match differentials, never absolute scores.

The lesson now enters our engineering standard: **a single-judge ±0.1 tolerance sits below the judge's own noise floor. From the next pilot onward, the quality gate is a multi-judge panel by default.**

## So what holds, exactly

- **Holds:** under replayed production load, FP8's throughput advantage is large and statistically sealed, and the knee advantage is hard evidence.
- **Not demonstrated:** quality neutrality at this operating point — one axis over the line by 0.027, with the signal at the same magnitude as judge noise.
- **Not supported:** any claim that "FP8 hurts quality" — the CI crosses zero, per-item damage shows no stable signature, and the panel flipped the sign.
- **Always true:** change the model, the GPU, or the engine version, and all of the above is void until re-measured.

This is what we sell — **not a number, an envelope map**: where it's green, where it's red, and the configuration that keeps you in the green, every line of it anchored to raw logs you can recompute.

## Don't trust us. Recompute us.

The whole evidence chain is public: the frozen protocol and its amendments, the raw logs of every run, the judge's verbatim per-item responses, the follow-up's pre-registration and amendment, three verdict documents. Three `--verify` commands — no network, no GPU — recompute every number from the raw logs by hand: the FAIL, and the ROBUST-CLEAR that overturned its judge.

Optimization is the product; measurement is the moat. An envelope map is worth more than a poster.

---

*Evidence chain: protocol & scripts — github.com/effiq/pilot002 ｜ raw evidence — sha256 custody, available on request for audit ｜ verdict: VERDICT.md (incl. the addendum outcome) ｜ reproduction: REPRODUCE.md ｜ previous essay: Your quantization speedup doesn't transfer to your workload*

*中文版本：《把真实流量回放给 FP8：2.33× 活下来了，"质量无损"这次没能证明》*
