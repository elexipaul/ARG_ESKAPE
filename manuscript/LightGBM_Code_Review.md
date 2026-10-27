# Code review: LightGBM classification in `protein_annotation_physicochemical_integration.ipynb`

**Scope.** Code review of the notebook's configuration (cell 1), sequence grouping (cell 11), model and CV functions (cell 17) and the ablation loop (cell 18). I read the code and the outputs saved in the notebook. I did not re-run it, and I did not see `ablation_metrics.csv` or `ablation_exclusions.csv`, so statements about results come from the notebook's printed output and progress log only.

## 1. What the code does

- One binary LightGBM classifier per CARD family or drug-class label (one-versus-rest), fitted on 5 feature blocks: R1 physicochemical (996 descriptors), R2 Pfam/InterPro, R3 generic UniProt counts, R4 = R2+R3, R5 = R1+R2+R3.
- Parameters, same for every target: 300 trees, 15 leaves, learning rate 0.05, `min_child_samples=10`, `reg_lambda=1`, `is_unbalance=True`, no early stopping.
- Evaluation is nested and grouped. Outer folds use `StratifiedGroupKFold` on MMseqs2 sequence clusters (40% identity, 80% coverage of both sequences, connected components). Inner grouped folds choose an F1-optimal decision threshold. Imputation and constant-feature removal are fitted on the training partition only.
- Targets and folds are fixed across the five blocks, so the ablation is paired. A shuffled-label control is run once.

## 2. What is done well

1. Group disjointness is asserted in code (`assert not set(groups[train]) & set(groups[test])`).
2. The decision threshold is chosen only from inner folds, never from outer test data.
3. Imputation and feature filtering happen inside the training partition.
4. Targets that cannot be evaluated are excluded with a logged reason instead of being repaired with random splits. The identity threshold is not loosened to rescue them.
5. Run hashes and caching make the analysis reproducible.
6. UniProt features are restricted to generic counts (R3), which avoids leaking GO or EC terms into family labels.

## 3. Findings that affect what you can claim

### 3.1 Strict grouping leaves only a small set of testable targets (high importance)

The progress log of cell 18 lists every model that was fitted. For the `all_proteins` cohort, the observed R1 runs cover **10 family targets and 24 drug-class targets**. Other cohorts: `uniprot_exact_mapped` 9 families and 21 drug classes; `uniprot_reviewed` 5 families and 16 drug classes. The 10 families are AAC(6'), ABC efflux pump, Erm-like methyltransferase, ABC-F ribosomal protection proteins, efflux regulatory protein, glycopeptide resistance gene cluster, LNU, MFS efflux pump, RND efflux pump and dfr.

Your earlier LightGBM study reported 84 families, using descriptor-space proximity groups. This notebook shows that only a minority of those survive an evaluation grouped by sequence identity. Performance for the other families was never tested at the identity-grouped level, so the 84-family numbers should not be presented as generalisation to dissimilar sequences. This is also the situation your Cryptic roadmap anticipates in section 4.1.

Confirm with `ablation_exclusions.csv`, which lists each excluded label and its reason.

### 3.2 A giant connected component drives the exclusions (high importance)

The group summary printed by cell 11 shows 369 groups over 6,059 proteins. The median group size is 1 and the largest is **1,541 proteins (25%)**. Connected components chain every protein linked through any qualifying alignment, so one group absorbs many distant relatives. Consequences:

- One test fold holds at least 25% of the data, so folds are unbalanced.
- Any family whose members sit mostly in the giant component has too few independent groups and is excluded.

This is the conservative choice, and it is correct for a leakage guarantee. It is also why the evaluable set is small. Options:

- Repeat the grouping at several thresholds (for example 30%, 40%, 60%, 80% identity) and report performance against threshold. That is the identity-stratified analysis from section 5 of your roadmap, and it uses the same code with `CLUSTER_IDENTITY` changed.
- Compare connected components with an MMseqs2 cluster mode that does not chain, and state which was used and why.

### 3.3 The shuffled-label control cannot detect sequence leakage (medium)

Cell 18 permutes the label matrix across all proteins (`ycohort[rng.permutation(len(ix))]`). After the shuffle, near-identical sequences carry unrelated labels, so there is nothing for leaked neighbours to help with. A null of about 0.5 therefore only shows that the pipeline has no label-independent artefact. To measure leakage, compare against an ungrouped or descriptor-grouped run and report the drop. With `NULL_REPEATS = 1` it is also a single draw, as the notebook itself notes.

### 3.4 Domain features reintroduce homology (interpretation)

Pfam and InterPro matches are profile-HMM matches, so R2 gains over R1 partly reflect homology information. Report R1 alone for any claim about non-homology detection.

## 4. Smaller points

- **Fold count can change.** `choose_outer_folds` tries 5 folds down to 2 and keeps the split that retains the most targets. A 2-fold split halves the training data. Report the chosen number of folds for each cohort.
- **Rare targets and `min_child_samples=10`.** Targets with 10 positives have at most about 8 in a training fold. With leaves needing 10 samples, small families may be underfit. Treat low scores for the smallest families as a capacity limit as well as a feature limit.
- **Fixed hyperparameters.** No tuning or early stopping, identical for every target. This is acceptable for a matched ablation but means absolute scores are not optimised.
- **Median imputation of the UniProt block.** About 1,999 of 6,059 proteins have no exact UniProt match (4,060 do). Their R3 values are filled with training medians, which look like real "no annotated site" values. LightGBM handles missing values natively, so imputing is optional here and may dilute R3.
- **Pooled out-of-fold metrics.** AP and AUROC are computed on scores pooled across fold models, with per-fold metrics also saved. If a family's positives fall in a single fold, the pooled value reflects one model.
- **sklearn warnings in cell 18.** "The least populated class in y has only 1 members" comes from stratifying on the rarest label of each protein. It is expected for singleton targets and is handled by the later eligibility check.

## 5. Suggested next steps

1. Open `ablation_exclusions.csv` and tabulate the exclusion reasons by family.
2. Re-run the grouping at several identity thresholds and plot performance of R1 against identity (your roadmap's gating experiment).
3. Run an ungrouped or descriptor-grouped comparison for the same 10 families to quantify inflation.
4. Add a cluster-level label permutation as a second control, and increase `NULL_REPEATS`.
