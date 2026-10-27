# AMR Gene Family Feature Analysis — Project Context

Working notes from an extended analysis session on ESKAPE ARG gene family classification using sequence-derived features (Pfeature + ProFET). Captures what was built, what was found, what's verified vs. still open, and what's still needed before any of this goes into a manuscript.

---

## 1. Project Scope

Question: can AMR gene families (CARD annotation) and/or resistance mechanisms be distinguished using sequence-derived composition and biophysical features alone — no alignment, no homology search, no structure prediction? And if so, which features drive that separation, for which families?

Dataset: 6206 ARG protein sequences across 395 named CARD "AMR Gene Family" labels, restricted to 84 families with n≥10 sequences for any statistical work (median family size across all 395 is 2; 175 families are singletons).

---

## 2. Feature Extraction Pipeline

### 2.1 Pfeature (composition-based, 1000 features/sequence)

Package: `pip install pfeature` (v1.4) — CLI tool (`pfeature_comp`), **not** the function-level library the PDF manual describes (that's a separate GitHub download).

**16 job types used**, chosen after direct speed/correctness testing of all available jobs:
`AAC, DPC, PCP, ATC, BTC, RRI, PRI, DDR, SEP, SER, SPC, CTC, PAAC, APAAC, QSO, SOC` = 1000 columns total.

**Excluded, with reasons:**
- `TPC`, `AAI`, `ACR` — didn't finish in 25s for 5 short test sequences (8000-dim, 553-dim, 1659-dim respectively; impractical at family scale)
- `CeTD`, `ALLCOMP` — crash on any pandas ≥2.0 (`DataFrame.append()` was removed; package still calls it)
- Evolutionary info (PSSM) and Structure module — not in the pip package at all; PSSM needs legacy `blastpgp`/`makemat` binaries (pre-2009, largely unmaintained) + a local BLAST database; Structure needs PDB files + external tools (PaDEL, NACCESS, DSSP)

**Bugs found and fixed:**
- `tqdm` is used internally but undeclared as a dependency — `pip install pfeature` alone leaves it broken.
- **Critical concurrency bug**: every job writes intermediate output to a hardcoded, non-unique relative filename (`tempfile_out`, `sam_input.csv`) in the process's working directory. Concurrent `pfeature_comp` calls sharing a cwd silently clobber each other's scratch files — no error, no bad exit code, output just goes missing or wrong. Confirmed directly: a family's `DPC.csv` vanished under 4-way parallelism while the script reported "success." **Fix**: run each subprocess call in its own isolated temp working directory. Stress-tested at 22 families × 16 jobs × 12 workers (352 calls), zero missing/corrupted output afterward, including a specific check that concurrently-processed identical-content families came back byte-identical (rules out silent cross-family data swapping, not just missing files).

Delivered: `arg_pfeature_batch_extraction.ipynb`

### 2.2 ProFET-style biophysical features (Biopython, 40 features/sequence)

10 whole-sequence properties × 4 (whole/N-third/M-third/C-third) = 40: `sequence_length, molecular_weight_da, isoelectric_point, aromaticity, instability_index, gravy, net_charge_pH_5, net_charge_pH_7, net_charge_pH_9, aliphatic_index`.

**Original user-provided code had two real bugs, both fixed:**
1. Missing 4 of 10 requested features — `net_charge_pH_5/7/9` (Biopython's `charge_at_pH()` exists, just wasn't called) and `aliphatic_index` (not a Biopython method at all — computed manually from the Ikai 1980 formula: `A% + 2.9×V% + 3.9×(I%+L%)`, using `analysis.amino_acids_percent`, a plain dict attribute already on a 0–100 scale).
2. **Crash on any non-standard residue** (`X`/`B`/`Z`/`U`/`O`/`J`) — `molecular_weight()`, `instability_index()`, `gravy()` all fail on these (confirmed: `ValueError`/`KeyError`); `isoelectric_point()`, `aromaticity()`, `charge_at_pH()`, `amino_acids_percent` all survive. Original code had no error handling, so one bad residue anywhere killed the entire batch with zero output. **Fix**: per-feature `try/except` (`safe_call`), not per-sequence — preserves the features that don't fail even when others do for the same sequence.

Delivered: `profet_features_batch_extraction.ipynb` — restructured to match the Pfeature notebook's `family`+`sequence_id` keying so the two outputs merge cleanly.

### 2.3 Data quality findings (not methodology bugs — findings about the actual data)

- **PDC beta-lactamase's `PRI_*` columns (24 features) failed systematically** — all 639 PDC sequences, zero exceptions, every other family unaffected. Root cause never diagnosed; median-imputed as a workaround and explicitly excluded from every downstream ranking/importance result rather than treated as real signal.
- **A separately-uploaded `PAAC.csv` was traced to nucleotide (CDS/DNA) sequences run through the protein-composition tool by mistake** — diagnosed from the composition pattern itself (only A/C/G/T-coded amino acid columns ever non-zero across 280/280 rows, summing to ~100%, plus one `N`-ambiguity hit) before the user confirmed it directly.

---

## 3. Notebooks Delivered

| Notebook | Purpose |
|---|---|
| `arg_pfeature_batch_extraction.ipynb` | Batch Pfeature composition extraction, 16 jobs, isolated-cwd concurrency fix |
| `profet_features_batch_extraction.ipynb` | Batch ProFET biophysical extraction, per-feature error handling |
| `pca_mechanism_exploration.ipynb` | Sequence-level PCA colored by RGI resistance mechanism |
| `family_centroid_pca.ipynb` | Family-centroid PCA (84 reliable families), refit-on-subset methodology |
| `family_classifier_importance.ipynb` | 84 one-vs-rest LightGBM models, SHAP + permutation importance, feature-similarity CV grouping |

**Not yet packaged** — ran only as ad-hoc session code, flagged explicitly as a gap: the per-family Cohen's d one-vs-rest analysis (`family_feature_effects.csv` and related outputs), and the PC-loading/F-statistic/cosine-similarity work built on top of the family-centroid PCA.

---

## 4. Key Analyses and Verified Findings

### 4.1 Mechanism vs. family separability

Silhouette score (full 1035-feature merged set): **mechanism = 0.056** (efflux specifically = 0.003, indistinguishable from random); **family = 0.434**. Physical/ProFET features alone: family silhouette = **0.376** (87% of the full set's separability from 4% of the dimensions).

Interpretation: family is the compositionally coherent unit; mechanism is not — consistent with the same mechanism being reachable via structurally unrelated protein folds.

### 4.2 Which families separate, and which don't (physical features alone)

51/84 families: silhouette > 0.25 (well separated). 15/84: negative silhouette (worse than random) — and those 15 are almost entirely **broad catch-all CARD labels**, not real failures of the method: "OXA beta-lactamase without subfamily," "RND antibiotic efflux pump," "ABC antibiotic efflux pump," "antibiotic efflux regulatory protein," "glycopeptide resistance gene cluster." This reframes the analysis as a **label-coherence audit** of CARD's ontology, not just a classification exercise.

Named OXA subfamilies (OXA-61-like, OXA-63-like, OXA-184-like, OXA-114-like, OXA-286-like, etc.) scatter across nearly the entire family-centroid PCA rather than clustering together — "OXA" as a naming convention is not compositionally unified even at the subfamily level.

### 4.3 Family-centroid PCA (84 reliable families, n≥10 each)

Refit specifically on the 84-family subset (not filtered post-hoc from a fit on all 395 — confirmed the two approaches give materially different, and the post-hoc-filtered one misleading, results: every "extreme" centroid in the unfiltered 395-family fit had n≤5, i.e., no averaging, just raw single-sequence noise).

PC1 = 10.2%, PC2 = 7.3% variance. PC1 loadings dominated by an alanine/small-residue-vs-lysine axis (`PCP_Z2`, `PCP_AL`, `AAC_A`, `AAC_K`); PC2 by a charge axis (`PCP_AC`/`PCP_NC`, `AAC_D`, `AAC_Q`, `isoelectric_point`). Both composition-derived, not from the ProFET physical set.

### 4.4 Per-family feature effect sizes (Cohen's d, one-vs-rest)

For each of 84 families, that family's sequences vs. every other sequence, per feature: Cohen's d for effect size, Welch's t-test + BH-FDR for significance. **FDR is nearly useless at this scale** — 12/84 families had >1000 of ~1014 features come back "significant"; ranking by |Cohen's d| is the informative axis, not the p-value.

Standout results: **qnr** (`CTC_762`, d=23.1 — mean 0.64% in-family vs. 0.004% elsewhere), **PDC** (`CTC_727`, d=9.3, after excluding the imputed `PRI_*` columns). CTC (conjoint-triad) and DPC (dipeptide) features dominate almost every family's top hit — composition and local sequence order carry more per-family discriminating power than whole-protein biophysical scalars.

Physical-feature-specific findings, each independently mechanistically plausible: **SMR** efflux pump → elevated GRAVY/aliphatic index (known to be among the smallest, most hydrophobic multidrug transporters); **APH(3′)** → strongly acidic net charge/low pI (plausible electrostatic complementarity to polycationic aminoglycoside substrates); **Erm** methyltransferase → strongly basic (plausible RNA-binding, RNA being polyanionic); **CAT** → elevated aromaticity (chloramphenicol itself carries an aromatic ring). Effect sizes for these (d=2–6) are consistently smaller than the CTC/DPC-led findings (d=8–23) — physical features are real, interpretable, secondary contributors, not the dominant signal.

### 4.5 Per-family classifiers (LightGBM, one-vs-rest, 84 models)

Design decisions, each resolved through explicit back-and-forth rather than assumed:
- **Single-label**, not multi-label — multi-label was dropped after confirming no working path exists: `sequence_id` doesn't match CARD's raw Protein/DNA Accession columns (0/6206 matches checked directly), so multi-family membership can't be derived from annotation metadata, and no sequences are available in this session to detect it via similarity either.
- **No CD-HIT** (declined) — replaced with an approximate substitute: near-duplicate detection via nearest-neighbor distance in standardized feature space (composition features are deterministic functions of sequence, so near-identical vectors imply near-identical sequences). Threshold=1.0 chosen empirically from the actual nearest-neighbor distance distribution — confirmed a looser threshold (3.0) collapses 73% of PDC into a single CV group, which would break stratification for that family entirely.
- **Two-stage feature importance** — naive permutation importance over all ~1035 features timed at ~9 hours for 84 families; fixed via gain-importance triage (free) to shortlist ~80 candidates, then permutation importance only on the shortlist (~48 min for 84 families).
- **SHAP is the primary importance signal, not permutation importance.** For families with near-perfect separation (qnr: AP=1.000 on held-out folds), permutation importance goes to exactly zero — the model has multiple redundant features that could each alone achieve perfect separation, so shuffling any one doesn't measurably hurt the score. This isn't a bug; SHAP doesn't share the blind spot (confirmed: still ranks `CTC_762` first for qnr). **SHAP magnitudes are not comparable across families** — they're computed in raw model-margin space, and extreme class imbalance (e.g., SMR: 12 positive vs. ~5467 negative) pushes that scale far higher than a more balanced problem, independent of true feature importance.
- Validated against known-good answers before trusting at scale: qnr recovered `CTC_762` via both SHAP and gain importance; SMR (n=12) returned honestly poor performance (AUC=0.69, AP=0.13), matching the small-sample caution already raised from the univariate work — multiple independent methods agreeing, not just multiple analyses saying similar things by coincidence.

### 4.6 PC score cosine similarity, and F-statistic per-PC feature ranking

Built (by the user, extending the family-centroid PCA output) to identify each family's "dominant PC" (`argmax(|score|)` across 20 components) and compute pairwise cosine similarity, on the **full 20-dimensional PC score vector**, between families sharing a dominant PC — confirmed by reverse-engineering against the actual uploaded file's numbers (ruled out raw ~1038-dim feature vectors and 2D PC1/PC2-only as the compared vectors; only the full 20-dim score vector matched). Number of pairs per group is $\binom{n}{2}$, not $n$ — verified directly for PC1's 24-member group (276 pairs, reproduced mean/min/max closely).

Separately, a one-way ANOVA F-test (`f_classif`, sequence-level, family as class label) was cross-referenced against each PC's top-20 loading features. F-statistic formula verified from raw SSB/SSW computation, exact match to `sklearn` and to the user's saved values. **Two issues identified in that pipeline, unresolved:**
1. **No redundancy control on the F-test** — same pseudo-replication risk as everything else that isn't the LightGBM notebook; families with many near-duplicate sequences (PDC, OXA-51-like) likely have inflated F-statistics from underestimated within-group variance (MSW sits in F's denominator).
2. **18/20 PCs show zero overlap with the global top-20 F-stat feature list — explained mechanistically, not a bug**: the global top-20 is dominated by features that are near-diagnostic for a single family (e.g., qnr's `CTC_762`, F=1266, the dataset's single highest); such a feature concentrates its loading influence onto the one PC that isolates its family (confirmed: `CTC_762` is PC7's top loading, and qnr's dominant PC is PC7) and contributes negligibly to the other 19. The overlap check tests "is this PC built around one hyper-diagnostic feature," not "are these loadings meaningful."

**Precision correction on PCA loading interpretation**: "top-20 loading features are responsible for the variation of these families" is a substantial overstatement. Loadings for one PC form a unit vector (sum of squares = 1.0, verified); the top 20 account for only 14.3% of that budget, the other 1018 features hold 85.7%. Combined with PC1 itself being only 10.2% of total variance, the top-20's real share of total family variance is ≈1.5%. Accurate framing: most individually influential contributors to one diffuse, minor axis — not dominant drivers of family variation overall.

---

## 5. Known Limitations (as of this export)

- **Redundancy/pseudo-replication control was only ever applied to the LightGBM classifier's cross-validation.** The Cohen's d one-vs-rest analysis, the family-centroid PCA, and the F-statistic ANOVA all ran on raw sequence counts with no near-duplicate control. This is the single biggest open methodological gap — it likely inflates apparent family-distinctiveness for families with many near-identical variants (PDC, OXA-51-like, ADC), and affects every descriptive/exploratory result in this project, not just one analysis.
- **Organism/phylogenetic confound never tested.** No organism metadata available in this session to check whether physical-feature family separation partly reflects uneven organism sampling (GC-content-driven amino acid usage bias) rather than genuine family-level biology.
- **Physical-features-only supervised classification was never run** — only the unsupervised silhouette (0.376). The direct supervised-learning version of "can physical features alone classify families" doesn't have a number yet.
- **Multi-family CARD membership dropped, not resolved** — no sequences available to test via similarity, no working ID bridge to CARD's raw multi-family annotation metadata found.
- **PDC's `PRI_*` failure was patched (imputed + excluded), never root-caused.** If it's a genuine Pfeature bug rather than a data artifact, it may affect other users of the tool.
- **Dataset composition is heavily beta-lactamase-skewed** — 227/395 families are singletons/pairs; the numerically dominant, best-powered families (PDC, OXA-51-like, ADC, CTX-M, KPC, SHV, TEM) are all beta-lactamases. Findings are better evidenced for antibiotic-inactivating enzymes than for other mechanisms.
- **Cohen's d one-vs-rest analysis and the PC-loading/F-statistic work exist only as session code, not as a reusable notebook** (unlike the five items in Section 3).

---

## 6. Literature Context

Located via live search, not recalled from training — cited directly rather than reconstructed from memory:

- **Cai, C.Z. et al. (2003), "SVM-Prot," *Nucleic Acids Research*** — SVM classification of proteins into functional families using physicochemical properties (not alignment). Reported amino acid composition, hydrophobicity, polarity, and charge as the most critical features — the same pattern found independently here. Also documented multi-family protein membership as a known source of misclassification in this exact type of approach.
- **Varshavsky, R. et al. (2007), "When Less Is More: Improving Classification of Protein Families with a Minimal Set of Global Features," WABI proceedings, Springer** — tests directly whether a small set of global sequence-derived features suffices for family classification (the supervised version of the physical-features-only question raised in Section 5). Cited within the ProFET paper itself.
- **Ofer, D. & Linial, M. (2015), "ProFET," *Bioinformatics*** — the template for the biophysical feature set used here. Their own results (Section 3.3 of that paper) found AA composition and residue-level entropy dominated their thermophile/neuropeptide-precursor benchmarks over whole-protein biophysical scalars — consistent with the composition/CTC/DPC-dominance finding here.

---

## 7. Recommended Next Steps, in Order of Leverage

1. **Apply the redundancy-control fix consistently** — extend the feature-similarity grouping (or real CD-HIT, if sequences become available) to the Cohen's d and F-statistic analyses, not just the classifier. Everything else is downstream of trusting these numbers.
2. **Package the Cohen's d one-vs-rest analysis as a notebook** — same gap the family-centroid PCA had before it was consolidated.
3. **Run the physical-features-only LightGBM ablation** — same pipeline as the existing classifier notebook, restricted to the 40 ProFET columns, compared against the full-feature-set AP/AUC per family. Turns the "physical features can classify families" claim from a plausible inference into a measured result.
4. **Test the organism confound**, if organism metadata can be joined in — stratify the physical-feature silhouette by organism, or at minimum report the organism composition of the dataset alongside any biophysical-signature claim.
5. **Diagnose PDC's `PRI_*` failure** rather than leave it as a permanent exclusion — worth knowing whether it's a data artifact specific to this dataset or a real bug in the tool.
