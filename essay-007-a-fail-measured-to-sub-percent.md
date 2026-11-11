# A FAIL, Measured to ±0.3%

We just published another FAIL of our own. This time, the word "nothing" was weighed: 95% CI half-width ±0.3%.

## What we measured

Pilot 007 asked a plain question: on a domestic-accelerator inference stack (single card, eager execution, a 27B dense model, OpenAI-compatible serving), without touching the engine — only the serving-layer knobs every inference framework exposes to operators: concurrency caps, batching budgets, prefix caching — how much free performance is left?

Six knobs, all pre-registered: a 3-run screening first, point estimate above 1.00 required to advance; then 6 paired confirmation runs, 95% CI lower bound above 1.00 required to survive; to win, the CI lower bound had to reach 1.15. Request-level paired bootstrap, 100,000 resamples, frozen seed. The protocol was published before the first measurement, the verdict script decides independently of us, and every raw log carries a hash.

## The answer

- Best legal combination: throughput **+0.92%**, CI [1.0058, 1.0129]. The effect is real — the CI excludes zero. It is also useless — an order of magnitude below the 1.15 bar.
- Prefix caching: a consistent **−6% to −10%** under this share-nothing replay workload. A clean negative, with stated boundaries.
- Quality: both arms produced **byte-identical outputs** on all 150 frozen-set items. Zero degradation is not a verdict here; it is an identity.
- Overall verdict: **FAIL**. Under rules we froze ourselves, we don't get to argue.

## Why this FAIL is valuable

The industry is full of "we tuned your serving parameters" stories and almost no receipts with confidence intervals. This report is the receipt. Next time someone offers you conservative tuning on this class of stack, ask one question: what is your CI lower bound? Ours is +0.9%, raw logs attached.

This instrument resolves ±0.3%. Today it proved "there is no oil here"; tomorrow it will prove "there is oil there". Being able to measure zero honestly is what entitles you to claim anything non-zero.

## Where the oil actually is

Calibration had already shown us: on the same stack, throughput differs by 2.7× between naive low-concurrency serving and the frozen operating point — an engine-level pathology that serving knobs cannot touch. The conservative layer is now swept; the conclusion is "known, and small". The remaining performance lives below the serving layer. That is where the next pilot goes — and it is what we actually sell as an optimization company.

Measurement is the moat. Optimization is the product. This FAIL is one more brick in the moat — laid, and back to building.

*Effiq — Optimization is the product. Measurement is the moat.*
*Full verdict and reproduction guide: github.com/effiq/pilot007 ｜ Raw evidence: github.com/effiq/pilot007 (evidence/)*
