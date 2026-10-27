# Biological and statistical audit of the protein-annotation analysis

Date: 22 September 2026. Analysis reviewed: `protein_annotation_physicochemical_integration.ipynb` and its saved `Protein_Annotation_Physicochemical_Association` outputs.

**Assessment.** The results support an exploratory connection between protein composition, domain architecture, and prediction of selected CARD labels. The strongest integrated case is the membrane-related signature of MFS efflux proteins; the strongest target-specific predictive result is the Erm family. The analysis does not yet establish new resistance mechanisms, validate arbitrary novel proteins, or demonstrate a universal benefit from combining all features. “Bootstrap-stable” should remain an exploratory designation.

This audit inspected saved tables and source code and performed seven small, explicitly post-hoc sensitivity calculations. It did not execute the notebook, rerun annotations, repeat its bootstrap, or retrain models. The original notebook and results were left intact. Reproducible audit code and derived tables are in [the audit directory](Protein_Annotation_Audit_2026-09-22/).

**1. What the completed analysis actually contains**

| Quantity | Verified result |
|---|---:|
| Unique ARO proteins and unique amino-acid sequence hashes | 6,059 each |
| Protein–family memberships | 6,206 |
| Proteins assigned to multiple families | 136 |
| Represented family / drug-class labels | 396 / 39 |
| Labels with at least 10 proteins | 84 families / 27 drug classes |
| Descriptors | 996: 956 Pfeature and 40 ProFET-style |
| Completed InterProScan protein scans | 6,059 |
| Proteins with Pfam / integrated InterPro hits | 6,017 / 6,054 |
| Exact UniProt mappings | 4,060, approximately 67.0% |
| Reviewed UniProt representatives | 272, approximately 4.5% of the cohort |
| MMseqs2-derived connected components | 369 |
| Global descriptor–annotation tests | 253,361 |
| Screened and bootstrapped pairs | 22,682 |
| Pairs passing the bootstrap stability rule | 17,182 |

All four current input-file hashes match the run manifest. The saved matrices have consistent ARO indices; unassigned UniProt records remain missing rather than being assigned functional negatives. The outer-fold manifest has no cluster split across test folds. The notebook's last completion-check cell was not recorded as executed, although the main stages and report generation completed. This audit independently checked selected saved-data invariants; it does not substitute for every in-memory assertion in that cell.

The analysis population is CARD reference proteins. It is not a sample of isolates with measured antimicrobial susceptibility, and this notebook does not establish ESKAPE-host-specific effects. Family membership is correctly retained as multiple labels from the ARO index in the current code. One protein may contribute to several family-specific descriptions, but contributes only once to the global matrix.

**2. Biological findings with the clearest support**

**MFS proteins: a coherent membrane-composition signature.** PF07690 (`MFS_1`) was observed in 93 of 119 proteins assigned to the MFS antibiotic-efflux family, compared with 3 of 5,940 proteins outside that family. This is descriptive prevalence, not a separately calculated enrichment p-value.

For proteins with a usable GRAVY value, PF07690-positive proteins had median GRAVY 0.877 versus −0.190 in proteins without that hit. The length-adjusted rank correlation was 0.208, with an exploratory cluster-bootstrap interval of [0.130, 0.337]; positive proteins occupied 39 sequence groups. Excluding the largest positive group retained the direction and a similar magnitude, rho = 0.185.

The previous family classifier's highest-ranked SHAP feature for MFS was `M_aliphatic_index`, the aliphatic index of the middle third of the sequence. Its global association with PF07690 was rho = 0.215, interval [0.140, 0.345]. Middle-third boundaries are arbitrary sequence subdivisions and should not be presented as experimentally identified domains.

There is useful within-family evidence: among the 119 MFS-labelled proteins, the association between PF07690 and `M_aliphatic_index` remained positive (rho = 0.382; panel-adjusted q = 0.0189), as did the association with GRAVY (rho = 0.545; q = 7.96 × 10⁻⁷). These are post-hoc screens without separate bootstrap validation. Domain no-hits within this family can reflect divergent architecture or detection sensitivity, so they are not a set of experimentally proven non-MFS proteins.

The physical interpretation is consistent with the membrane-spanning architecture established for MFS transporters in structural work, including the MdfA antiporter. This is an interpretation of the observed composition pattern, not evidence that a particular composition value determines transport of a particular antibiotic. [Primary structural study](https://www.nature.com/articles/s41467-018-06306-x).

**Transmembrane annotation provides related contextual support.** Among exact UniProt mappings, 241 transmembrane-annotated proteins with usable GRAVY values occupied 98 groups. GRAVY showed rho = 0.356, interval [0.216, 0.508], and rho = 0.337 after removing the largest positive group. Within the RND family, the corresponding screen remained positive (rho = 0.531; q = 0.000679). In contrast, the UniProt transmembrane–GRAVY screen inside MFS did not pass q < 0.05 (q = 0.223). That difference should be retained in the report: agreement is not universal, and UniProt annotation coverage is incomplete.

**Erm methyltransferases: strong recovery of an established functional class.** PF00398 (`RrnaAD`) occurred in 47 of 48 Erm-labelled proteins and no proteins outside that family in this dataset. Its global relationship with the SHAP-ranked feature `SEP_PC` was modest: rho = 0.143, interval [0.071, 0.284], with 12 positive groups. The direction remained positive after dropping the largest positive group (rho = 0.116).

Erm family average precision increased from 0.5205 with physicochemical features to 0.9795 with Pfam/InterPro features. The combined model had AP 0.9794 and F1 0.9895. Its two outer-fold AP values were 1.000 and 0.9586, which is more reassuring than a pooled metric alone.

The known biological relationship is that Erm enzymes modify ribosomal RNA and can confer macrolide–lincosamide–streptogramin B resistance. The results recover this established functional organization. They do not show that `SEP_PC` causes methyltransferase activity. [CARD Erm definition](https://card.mcmaster.ca/ontology/36699).

Crucially, PF00398 is a broader RNA-methyltransferase domain that also includes KsgA-related proteins. Its apparent specificity for Erm in this CARD-only cohort is not specificity across all natural proteins. Background homologues are needed to test that. [Pfam domain description](https://www.ebi.ac.uk/interpro/entry/pfam/PF00398).

**Beta-lactamase composition: plausible associations with substantial cluster dependence.**

| Pair | Positive proteins / groups | Partial rho | Exploratory bootstrap interval | Rho after removing largest positive group |
|---|---:|---:|---|---:|
| PF00753 `Lactamase_B`–`DPC1_GH` | 492 / 8 | 0.562 | [0.149, 0.701] | 0.401 |
| PF13354 `Beta-lactamase2`–aromaticity | 1,544 / 7 | −0.671 | [−0.774, −0.054] | −0.185 |
| IPR009045 `Zn_M74/Hedgehog-like`–`DPC1_WH` | 18 / 6 | 0.331 | [0.143, 0.489] | 0.253 |

The installed Pfeature implementation confirms that `DPC1_GH` is the percentage of adjacent glycine–histidine pairs, and `DPC1_WH` the percentage of adjacent tryptophan–histidine pairs. These whole-protein summaries do not locate catalytic residues or establish metal binding. The Hedgehog-related text is an annotation name; it is not evidence of Hedgehog signalling in these bacteria.

The aromaticity result is particularly dependent on one group: 93.7% of PF13354-positive proteins belong to `cluster_00006`. Removing that group reduces the magnitude markedly, although the direction remains negative. Appropriate wording is “a composition pattern among the sampled PF13354-positive proteins,” not “a universal property of beta-lactamases.” The seven leave-group-out checks were selected after seeing the results and are sensitivity diagnostics, not independent replication.

The installed CTC implementation groups amino acids into seven categories. For example, `CTC_171` represents `[A/G/V]–C–[A/G/V]`, normalized against the maximum triad count in that protein. A value of 0.167 is not 16.7% of sequence positions and not an activity measurement. Motif-level explanations require locating occurrences relative to annotated domains and experimentally characterized sites.

**3. What the ablation results support**

The main all-protein evaluation retained **10 families and 24 drug classes**, not all 84 and 27 labels passing the sequence-count filter. The code reduced the main and exact-UniProt evaluations to **two outer folds**; only the small reviewed cohort used five. Targets must also have sufficient positive and negative groups and valid inner folds.

TEM, CTX-M, KPC and NDM were excluded from this grouped prediction assessment because their positive proteins occupied one connected component each. Among drug classes, azole and imidazole were excluded for insufficient sequence groups; nitroimidazole was excluded for insufficient inner grouped folds. Exclusion indicates insufficient independent evaluation support under this design, not absence of biological importance.

Median AP across the eligible targets:

| Inputs | Family AP, n = 10 targets | Drug-class AP, n = 24 targets |
|---|---:|---:|
| Physicochemical only | 0.242 | 0.185 |
| Pfam/InterPro only | 0.440 | 0.267 |
| Generic UniProt features only | 0.150 | 0.196 |
| Pfam/InterPro + generic UniProt | 0.501 | 0.293 |
| All inputs combined | 0.500 | 0.289 |

AP summarizes precision–recall ranking and is not percentage accuracy. Its prevalence reference varies with each target. Accordingly, the audit exports each target's prevalence and AP/prevalence ratio. [scikit-learn precision–recall documentation](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_recall_curve.html).

Annotations alone improved AP over the physicochemical baseline for 9/10 families and 21/24 drug classes. The combined input improved AP for 7/10 and 19/24, respectively. Median paired AP changes were +0.196 and +0.095 for annotations, and +0.076 and +0.026 for all inputs. A median paired change is not the difference between the two marginal medians in the table. These observed improvements do not have reported paired uncertainty intervals and should not be described as statistically established gains.

Some target-specific results are much weaker than the overall comparison suggests:

| Target | Physicochemical AP | Combined AP | Combined F1 | Interpretation |
|---|---:|---:|---:|---|
| Erm family | 0.5205 | 0.9794 | 0.9895 | Strong discrimination within this cohort |
| MFS family | 0.7631 | 0.8012 | 0.8571 | Modest AP gain with useful classification |
| AAC(6′) family | 0.6713 | 0.4935 | 0.5854 | Adding all features reduces AP |
| Carbapenem | 0.3643 | 0.3643 | 0.0860 | Poor generalization under the grouped split |
| Cephalosporin | 0.5069 | 0.5684 | 0.0597 | Weak thresholded prediction despite higher AP |
| Macrolide | 0.1589 | 0.3317 | 0.3302 | Improved ranking with substantial remaining errors |

Carbapenem prevalence is 0.4227, exceeding the combined model AP of 0.3643; combined AUROC is 0.4097. Cephalosporin prevalence is 0.5598, close to combined AP 0.5684; combined AUROC is 0.5102. These results do not justify dependable unseen-sequence predictions for these classes. Inspect both fold-level and pooled metrics: scores from different fitted folds can have different scales.

The generic UniProt block contains seven annotated site/topology measures. It does not include all UniProt text, GO terms or EC labels as classifier inputs. Nevertheless, annotation density, reviewed status, missingness and homology-based curation can correlate with CARD labels. Exact matching also restricts the availability of this block for novel proteins.

The shuffled-label median normalized MCC was approximately 0.499, close to its reference of 0.5. That is a reassuring diagnostic. Only one joint label permutation was used per cohort, shared across input blocks; it does not produce a permutation p-value or prove the absence of leakage. Some shuffled targets were excluded separately.

**4. Statistical and reporting issues requiring attention**

1. **The 369 groups are not 369 demonstrated independent evolutionary lineages.** They are connected components of detected MMseqs2 alignments at 40% identity and 80% bidirectional coverage. Similarity can be transitive, and residual dependence between components is possible. The three largest components contain 1,541, 1,447 and 1,180 proteins: 68.8% of the cohort. Group-aware validation is a strength, but the highly uneven structure limits precision and applicability.

2. **Very small q-values need calibrated interpretation.** The cluster p-values use a conventional sandwich variance and t reference with total-group degrees of freedom. Some annotations have only five or six positive groups. Few positive groups and very unequal sizes can make conventional cluster inference unreliable. This is a reason to investigate calibration, not proof that every reported association is false. FDR correction cannot repair miscalibrated underlying p-values. [Methodological guide](https://arxiv.org/abs/2205.03285).

3. **The 17,182 stable pairs are not 17,182 independent biological discoveries.** They comprise 155 annotation IDs, only 124 distinct observed annotation-presence vectors, and 812 descriptor names. Pfam/InterPro overlaps and correlated descriptors create redundancy. Of the stable pairs, 10,805 (62.9%) have absolute length-adjusted rho below 0.1, and 7,053 (41.0%) have at most ten positive groups. Effect magnitude and support should lead interpretation; a small rank effect may still be relevant for a rare annotation, but it must be described accurately.

4. **The bootstrap measures partial rank correlation.** It re-ranks and removes linear effects of log length in each cluster resample. It is not an interval for Cliff's delta. The screened pairs were selected on the same data, so their intervals are not independent confirmation or selection-adjusted confidence statements. Thirty-two stable pairs have opposite signs for unadjusted Cliff's delta and adjusted rho; their direction needs explicit reporting. Numerical p-values stored as zero reflect finite-precision arithmetic, not certainty.

5. **The conditional-domain step has a separate support issue.** This step correlates domain score/count/coverage with descriptor values among proteins carrying that domain, with optional length adjustment and cluster inference. Of 101,764 comparisons, 9,008 have cluster q < 0.05; 1,294 of those use fewer than five groups. This stage does not apply the binary screen's five-groups-per-state rule. Those low-group results need separate flags and should not be merged uncritically into the main evidence set. Domain HMM score is sequence-match strength, not enzyme activity.

6. **Within-family test counts are not discovery counts.** There were 164,679 family-stratum tests and 7,400 screened pairs across five families; 703,367 drug-stratum tests and 54,878 screened pairs; and 292,282 mechanism-stratum tests and 26,007 screened pairs. These panels have separate FDR families and were not separately bootstrapped. In this implementation, zero tests arise from annotation counts, missingness or constant descriptors; insufficient cluster support affects the candidate flag after a test is recorded. For example, an almost universal domain within Erm leaves no adequate no-hit comparison group.

7. **The SHAP context table requires target-aware interpretation.** It merges global annotation associations onto target-specific SHAP features by feature name. Of 35,822 rows, 27,933 have no recorded annotation occurrence in the named target. These are not evidence that that domain explains that target. The audit exports the 7,889 rows with a recorded target occurrence. Of those, 6,658 have higher annotation prevalence inside the target than outside, while 1,231 do not. These are descriptive context filters, not a new enrichment test. Annotation absence or depletion may itself be informative, but must be framed as such. A SHAP magnitude cutoff, including the earlier ≤2 rule, is not biological validation.

8. **Database agreement and family recognition are not independent mechanistic validation.** Descriptors and domain calls derive from the same sequences; Pfam and InterPro are linked; UniProt annotations may be inferred from homology. Global associations can reflect family, taxonomy, amino-acid composition and database curation. This dataset has no representative non-AMR background or measured susceptibility outcome. Cross-resistance, mobility, selection and cryptic ARG claims need their own evidence.

**5. Prioritized work needed for a defensible biological paper**

1. **Build the results around a few evidence chains.** Start with MFS/transmembrane composition and Erm domain recognition. For each, report ARO IDs, domain coordinates, whole-protein and domain-local descriptor values, target/background prevalence, positive group counts, adjusted effect and interval, within-family result, SHAP rank, and target-specific held-out metrics. Retain contrary results and distinguish known functional recovery from new hypotheses.
2. **Audit cluster influence and inference calibration.** Extend the seven documented sensitivity checks to a predeclared shortlist using all leave-one-group-out estimates, group-size balance diagnostics, and suitable small-sample cluster inference. If using resampling or null simulations, specify the exchangeability assumptions; merely increasing bootstrap repeats does not fix few positive groups. Predeclare how biological redundancy and multiple tests will be handled.
3. **Validate domain-local explanations.** Map informative dipeptides or grouped triads onto domain boundaries and known functional sites. Compare against suitable matched homologues and domain-free segments. For hydrophobicity, inspect membrane topology and segment alignment. A whole-protein frequency alone cannot identify a catalytic motif.
4. **Separate deployment questions.** A sequence within a familiar family, a remote homologue, and an arbitrary non-AMR protein are different tests. Use external proteins withheld by sequence group and, where possible, taxonomy or collection date. Add non-AMR homologues and evaluate an unknown/reject outcome. Do not reduce grouping strictness solely to improve performance; report additional split designs as distinct generalization settings.
5. **Quantify predictive uncertainty.** Report AP relative to prevalence, per-class PR curves, F1/precision/recall at training-selected thresholds, both outer folds, and paired uncertainty for input-block differences. Use repeated group splits where feasible, avoiding an interpretation of overlapping folds or labels as independent replicates. Compare domain-only, annotation-only and combined models without assuming combined must win. Evaluate calibration if probabilities will be used as confidence estimates.
6. **Address annotation and reference bias.** Preserve reviewed/unreviewed and evidence-code tiers, compare shared targets across cohorts, and inspect generic UniProt annotation density as a potential proxy. Include diverse non-resistance homologues for broadly conserved domains such as KsgA/Erm-related methyltransferases. Freeze database releases and use current web descriptions only for interpretation, not to silently replace the analysed label set.
7. **Reserve causal claims for appropriate validation.** Independent sequence replication supports generalizability. Biochemical characterization, relevant susceptibility experiments or other direct functional evidence are needed to establish an uncharacterized resistance mechanism. Domain/SHAP agreement alone cannot convert a CARD-unlinked family–drug pair into validated cross-resistance.

**Suggested results/conclusion wording**

> Across 6,059 CARD reference proteins, physicochemical descriptors were associated with sequence-derived protein annotations. Of 253,361 tested pairs, 22,682 passed screening and 17,182 retained length-adjusted cluster-bootstrap intervals excluding zero; these were treated as exploratory associations. Membrane-associated composition provided a coherent interpretation for MFS efflux proteins, supported by global and within-family analyses. Domain annotations also improved recognition of selected functional groups, particularly Erm methyltransferases. Under the implemented grouped evaluation, only ten gene families and 24 drug classes were evaluable; predictive performance and the benefit of combining feature types varied markedly across targets. These results support biological interpretation of selected classifier signals and prioritization of hypotheses, while external validation is required for novel-sequence and resistance-mechanism claims.

**Audit artifacts and provenance**

- [Numerical audit summary](Protein_Annotation_Audit_2026-09-22/audit_summary.json)
- [Seven biological-case sensitivity checks](Protein_Annotation_Audit_2026-09-22/biological_case_sensitivity.csv)
- [Model metrics with label prevalence](Protein_Annotation_Audit_2026-09-22/model_metrics_with_prevalence.csv)
- [SHAP context with recorded target annotations](Protein_Annotation_Audit_2026-09-22/SHAP_context_with_recorded_target_annotation.csv)
- [Selected within-family screens](Protein_Annotation_Audit_2026-09-22/selected_within_family_screens.csv)
- [Audit code](Protein_Annotation_Audit_2026-09-22/audit_saved_results.py)

The scientific-critical-thinking and statistical-analysis skills contributed procedural guidance to this audit, not biological evidence. Skill reference: Kassis, T., Agarwal, V., He, Y., Patel, D., & Brueckner, A. M. (2026). *Scientific Agent Skills: A Library of Procedural Knowledge for Research Agents*. [arXiv:2609.00065](https://doi.org/10.48550/arXiv.2609.00065). Author list verified against the current arXiv record.
