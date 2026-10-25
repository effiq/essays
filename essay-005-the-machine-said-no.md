# The machine said no — twice

**— the pipeline's first batch of verdicts includes two it was built to be unable to hide**

![protocol](https://img.shields.io/badge/protocol-frozen_before_execution-1f6feb)
![verdict](https://img.shields.io/badge/verdict-1_PASS_·_2_FAIL-critical)
![statistics](https://img.shields.io/badge/bootstrap-paired_·_seeded_·_100k-6e7781)
![evidence](https://img.shields.io/badge/custody-sha256_·_on_request-2da44e)

Pilot 003's FAIL was a decision made by hand: a run missed its own line, and a human chose to publish the miss. Pilot 005 is different in kind. It is the first pilot executed end-to-end by the frozen pipeline — calibration, profiling, synthesis, screening, confirmation, verdict, fully automatic — across three pre-registered cells, with verdict-bearing status assigned to cells 2 and 3 *before ignition*. When the registry closed on October 23, it read: **one baseline PASS, two FAILs.** Nobody in the loop had a mechanism to make it read otherwise. That is the whole story, and it is a good one.

## The shakedown cell — a PASS with its caveats printed on its face

Cell-1 (spec: a 32B general-purpose model × H100, public since protocol lock) cleared both frozen gates: migration CI lower bound **1.5537** against the 1.20 bar; recipe CI lower bound **1.5395** against the 1.50 bar. The margin on the recipe gate is 0.04, and we are not calling that a rout.

| gate | CI lower bound | frozen bar | outcome |
|---|---|---|---|
| cell-1 migration | 1.5537 | ≥ 1.20 | PASS |
| cell-1 recipe | 1.5395 | ≥ 1.50 | PASS |
| cell-2 migration | 1.4433 | ≥ 1.20 | PASS |
| cell-2 recipe | 1.2918 | ≥ 1.50 | **FAIL** |

*Paired bootstrap, n=10 runs, 100,000 resamples, seed 20262002. Figure version: ledger entry 07 at effiq.tech. Source: stage-19 verdict records, github.com/effiq/pilot005.*

But cell-1 is the shakedown cell: under the frozen protocol its result carries **no verdict weight**, and its publication page says so at the top. It ran before the judge-calibration instrument existed, so every quality figure on that page comes from a single uncalibrated local seat — a load-bearing caveat, printed where the numbers are. Its numbers must not be compared on the same scale as cells 2 and 3. It also ran the pre-fix arm-selection flow and thereby exposed a defect in it, which is now registered and repaired. Exposing that defect is what a shakedown cell is *for*. A PASS that publishes its own caveats is worth more than one that doesn't.

## The judge that has now sat in four pilots

Cell-2 (spec: a 14B reasoning-family distill × H100) reached the dual gates and failed both. Speed: recipe CI lower bound **1.2918** against the 1.50 bar — the table above. Quality: three calibrated judges, blinded, 150 items. Two seats passed. The third scored the correctness axis **−0.1067** against a tolerance of −0.10 — short by seven thousandths. The worst-judge rule binds, and the gate fails.

That seat's identity is the part we would not have scripted. It is the same judge that issued Pilot 002's red dot, passed the repaired build in Pilot 003, and passed the minimal-exemption recipe in Pilot 004. It has now sat in four pilots and voted both ways:

| pilot | margin | axis | tolerance −0.10 |
|---|---|---|---|
| Pilot 002 | −0.127 | clarity | FAIL (red dot) |
| Pilot 003 | +0.033 | clarity | pass |
| Pilot 004 | +0.013 | overall | pass |
| Pilot 005 · cell-2 | −0.1067 | correctness | **FAIL** |

*The instrument got neither stricter nor looser; what changed is what sat under it. Sources: pilot002–pilot005 verdict records.*

And why no second attempt: the confirmation phase exists to certify a *success* claim. Under the frozen no-rerun rule, re-measuring after a FAIL is lose-lose — a repeated FAIL buys zero information, and a flipped PASS would be unpublishable by definition. One clean negative is publication-grade. The cell was sealed as its terminal state, with the reasoning frozen in an addendum.

## The cell that never reached the gates

Cell-3 (spec: an Arabic-native 27B model × H100) ended at a gate most benchmark write-ups don't know exists: **screening**. The frozen proceed rule says the screening winner's clarity delta must be ≥ −0.05 to earn formal measurement. The winner scored **−0.200**, and the pipeline terminated the cell before any stage-18/19 data existed. No latency numbers, no throughput claims — nothing to cherry-pick from, because the machine never produced it.

The observation that matters is in the control arm: stock FP8 — no recipe, plain migration — scored **−0.55** on clarity. On this combination, plain FP8 migration itself carries a quality cost of the same order as any recipe arm:

| arm | screening clarity delta |
|---|---|
| proceed bar (frozen) | ≥ −0.05 |
| best recipe arm (b4-w2) | −0.200 |
| stock FP8 control (b0) | −0.550 |

*n=40 blinded items per arm, single indicative judge seat; screening is indicative per protocol. Source: cell-3 screening record.*

Context, honestly labeled: that quantization disproportionately harms non-Latin-script languages is established in the literature (Marchisio et al., *How Does Quantization Affect Multilingual LLMs?*, EMNLP Findings 2024). To our knowledge — literature search dated 2026-10-22 — no public measurement existed for FP8 dynamic quantization with FP8 KV cache on an Arabic-native 27B model under a pre-registered blind-judge protocol. Now one exists, and it is negative. That is a scoped first measurement in one specific setting, not a general priority claim. Also on the record: cell-3's first attempt was sealed *before producing any data* — the intervention counter hit 9 against a frozen cap of 8, and the run was terminated unmeasured, exactly as designed. The second attempt ran from ignition to verdict in 2 hours 43 minutes with zero adaptation edits.

## What the two FAILs are — and what they are for you

The semantics, verbatim from the verdict records: under the pre-registered process, budget, and statistical discipline, the lossless-speedup test on these model × quantization × hardware combinations returned negative. A negative result is neither a defect verdict on the models nor a capability verdict on us — it is a **compressibility fact** about each combination under the frozen process. We are an optimization company, not a rating agency: these pages certify our own optimization work, and nothing else.

And the buyer's translation: if you run Arabic-language workloads on saturated H100 capacity, "just turn on FP8" now has a published counterexample with a recomputation sheet attached. Before anyone touches your stack, ask them the three questions in entry 04 of our ledger — including whether their screening stage is allowed to say no.

## Don't trust us — recompute us

Public since protocol lock: the frozen protocol (v4.2 and its twenty registered addenda), the three cell specs, the per-cell verdict publications with their reproduction sheets, and the pilot-level closure — all at [github.com/effiq/pilot005](https://github.com/effiq/pilot005). Raw evidence is archived under sha256 custody and available on request for audit. The ledger grows by accretion: Pilot 003's FAIL stays where it is, Pilot 004's PASS stays where it is, and Pilot 005's two negatives go in next to them. Nothing is rewritten.

The next pilot will be pre-registered before any measurement, and whatever it returns will be published under the same frozen rules. **Optimization is the product; measurement is the moat. The machine said no twice — and published both.**

---

*Evidence chain: protocol, addenda, cell specs, verdicts & reproductions — github.com/effiq/pilot005 (see verdicts/PILOT005-TOTAL-VERDICT.md) ｜ figures: ledger entry 07 at effiq.tech ｜ previous essay: Three days after the FAIL, we published a PASS*
