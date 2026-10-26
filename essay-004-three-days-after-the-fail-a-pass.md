# Three days after the FAIL, we published a PASS

**— the floor cleared the bar; only then did the peak earn its place on the page**

![protocol](https://img.shields.io/badge/protocol-frozen_before_execution-1f6feb)
![verdict](https://img.shields.io/badge/verdict-PASS_·_published_in_full-brightgreen)
![statistics](https://img.shields.io/badge/bootstrap-paired_·_seeded_·_100k-6e7781)
![evidence](https://img.shields.io/badge/raw_logs-public_·_recomputable-2da44e)

Look at this set of numbers first.

Throughput **1.92×**, paired-bootstrap 95% CI **[1.627, 2.324]** — bar 1.50, and this time the conservative lower bound cleared it on its own. Side by side, the stock-FP8 migration replication: **1.96×**, CI [1.654, 2.370], bar 1.20. Blind quality judging passed under all three judges; the worst seat's overall delta is **+0.013** (tolerance −0.1) — and that seat belongs to the judge who issued the original red dot. Tail latency: P95 TPOT at **0.30** of baseline, first-token latency at **0.31**.

You remember how the last essay ended: the performance gate missed by 0.067, the FAIL was published in full, and we left the words on the record — *one more FAIL, and the kill-page starts a full review.*

This is the other ending.

## Boundaries first

This PASS overturns nothing. Pilot 003's FAIL on L40S is **permanently valid** — it hangs in the repository, one byte unchanged. Pilot 004 is a new claim, and its boundary is printed at the top of the verdict: **a single NVIDIA H100 SXM 80GB, Qwen2.5-14B-Instruct (pinned revision), an Azure-trace-anchored workload, a calibrated operating point** (concurrency 16, SLO compliance 0.9735). Outside this one cell, we claim nothing.

The hardware moved a generation; not one rule loosened. The protocol was frozen before measurement (this time with four execution addenda, each archived before any further execution); both gates must pass to win; the statistics stayed sealed until all ten COMPLETE markers existed. On day 3 of the 30-day stop-loss window, the gavel came down.

## What the recipe did right this time

Pilot 003's FAIL wrote down the lesson: exempting attention layers repairs the red dot, but it eats compute — in the five quiet windows, the repaired build ran measurably slower than pure FP8. Pilot 004's answer: **exempt less, and exempt precisely.**

The method: 192 attention projections × 256 calibration texts, per-module sensitivity profiling, exempt only the true outlier projections, FP8 everywhere else. Five candidate arms ran the screening, and one negative result is worth the record: the arm that deterministically merged the top two exemption sets scored **negative** (−0.017) — **exemption sets do not compose naively.** That null result is part of the lever map, on the record like everything else.

The most direct comparison comes from the anchor arm: the previous generation's heavier-exemption recipe, rebuilt as-is and run alongside (descriptive only, never entering any interval estimate). On the heaviest load slice, the old recipe delivers 2.26×; the new one, ≈**3×** — fewer exemptions, more speed, and it is the one that passed the panel.

## A margin of +0.013

The correct reading of the quality gate is "**no measurable quality loss beyond tolerance**," not "better than BF16." Under the strictest judge the margin is +0.013, and we are not calling that a rout. 450 blind judgments, judges blinded to arm identity, the worst-judge rule — all three are frozen text.

This judge's trajectory deserves a line of its own. In Pilot 002 it issued the clarity red dot at −0.127. In 003 the repaired build reversed to +0.033 under its hand. This time, the recipe with the fewest exemptions and the highest speed passed under it at +0.013. The instrument got neither stricter nor looser — what changed is the recipe.

## The peak and the floor, together

Last essay we said the floor is worth more than the peak. That stands: the only citable statistic on the verdict is the conservative one that weights the ten runs equally. But the load dependence is real, and hiding it is the same kind of dishonesty as inflating it — so here is the full table, computed directly from the published run-level numbers:

| Load slice (light → heavy) | Migration (stock FP8) | Retry (our recipe) |
|---|---|---|
| Lightest | 1.50× | 1.48× |
| Second | 1.56× | 1.55× |
| Middle band | 1.66× – 1.95× | 1.61× – 1.92× |
| Heaviest | **3.03×** | **2.97×** |

**≈3× is the descriptive peak, ≈1.5× the descriptive floor, 1.92× the citable figure** — all three true; the difference is only the hat each one wears. For capacity planning, use the smallest. For how fast it goes under heavy load, the table is on the record.

## What we can sell now

Pilot 003's FAIL left a two-configuration compromise: quality at the price of speed, or speed with a red dot attached. Within the H100 boundary, Pilot 004 cancels the compromise:

- **One recipe:** 1.92× (CI lower bound 1.63×, over the 1.50 bar), quality certified by a three-judge blind panel, tail latency cut by roughly 70%.
- The control — stock FP8 — is nearly as fast (1.96×), but its quality is not gated: it gives you the speed; the risk stays with you.

The boundary is still that one cell: Qwen2.5-14B × H100 SXM × controlled load. Other models, other cards re-enter the same pipeline for their own license — calibration, profiling, synthesis, screening, confirmation, verdict, fully automatic. You have already seen what every verdict looks like.

## Don't trust us — recompute us

The full evidence chain is public: the frozen protocol and its four addenda, the raw records of the five-arm screening, 450 raw judge responses, per-request data from all ten performance runs, the verdict and the reproduction guide. Every stage ships a `--verify` entry — no network, no GPU — every number recomputed by your own hands from the raw logs, including the last FAIL and including this PASS.

The FAIL stays where it is; the red zones are not erased. The green of the new cell is drawn standing on the red of the old one.

**Optimization is the product; measurement is the moat. The floor cleared the bar — and only then did the peak earn its place on the page.**

---

*Evidence chain: protocol, addenda & scripts — github.com/effiq/pilot004 ｜ raw evidence — sha256 custody, available on request for audit ｜ verdict: VERDICT.md ｜ reproduction: REPRODUCE.md ｜ previous essay: After the 2.33×, we published our own FAIL*

*中文版本：《FAIL 挂出三天后，我们发布了 PASS》*
