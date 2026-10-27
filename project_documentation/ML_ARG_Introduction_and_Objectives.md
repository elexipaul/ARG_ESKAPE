**Sequence-derived physicochemical signatures of ARG families and drug classes: introduction and objectives**

*Draft prepared 30 September 2026 for the protein-level ML–SHAP–annotation component of the project. Author review is pending. This framing does not imply that the CARD training cohort is restricted to ESKAPE organisms.*

**How the lantibiotic paper informs this study.** The supplied paper is Poorinmohammad, Hamedi and Moghaddam (2018), *Sequence-based analysis and prediction of lantibiotics: A machine learning approach*. Its rationale connects a biological knowledge gap to two related aims: classifying sequences and identifying properties that distinguish the target group. It represents sequences numerically, compares classifiers, selects informative descriptors, and interprets selected properties using biological knowledge. The paper uses information-gain feature selection, not SHAP. Its choice of other bacterial antimicrobial peptides as the comparison group makes its classification question more specific than comparison against arbitrary proteins. These elements provide a useful precedent for our rationale; its lantibiotic-specific biological explanations and performance estimates do not transfer to ARGs. [Paper](https://doi.org/10.1016/j.compbiolchem.2018.10.004).

The corresponding question for this project is: **Which sequence-derived physicochemical properties distinguish CARD-defined ARG families and drug classes, and how far can protein annotations explain those properties and improve their predictive use?** This connects classification to biological interpretation without presupposing that every important feature has a mechanistic explanation.

**Proposed introduction.** Antimicrobial resistance research requires both reliable identification of resistance determinants and an understanding of the molecular diversity represented by those determinants. Curated resources such as the Comprehensive Antibiotic Resistance Database (CARD) organize resistance-associated sequences and connect them to antimicrobial resistance gene (ARG) families, resistance mechanisms, and drug classes. These relationships provide a reference for supervised analyses of protein sequence variation. Alongside assigning database labels, an informative computational analysis can ask which measurable sequence properties distinguish the labelled groups and whether those properties have a coherent biological context. [Alcock et al., CARD 2023](https://academic.oup.com/nar/article/51/D1/D690/6764414).

<!-- [claim:C001] [evidence:E02] -->

Numerical sequence descriptors provide one approach to examining this question. Amino-acid composition, local residue combinations, sequence-order measures, and physicochemical summaries describe complementary aspects of a protein sequence. Poorinmohammad and colleagues demonstrated the value of combining sequence-based classification with analysis of distinguishing descriptors in their study of lantibiotics and other bacterial antimicrobial peptides. Their work provides a precedent for asking both whether a protein group can be discriminated and which sequence properties contribute to that distinction. Applied to ARGs, this approach motivates an examination of family-associated and drug-class-associated signatures rather than assuming that predictive accuracy alone explains resistance biology. [Poorinmohammad et al., 2018](https://doi.org/10.1016/j.compbiolchem.2018.10.004).

<!-- [claim:C002] [evidence:E01] -->

The present study considers two levels of CARD annotation: ARG family membership and drug-class membership. These labels answer different questions, and their relationships need not be one-to-one. This makes it useful to investigate whether a family's important descriptors are shared with those of its associated drug classes, and whether similar predictive signatures occur in pairs that are not linked in the reference annotation. Shared descriptors can identify relationships for examination, but similarity between predictive profiles does not by itself establish shared substrate specificity or an additional resistance phenotype. [CARD 2023](https://academic.oup.com/nar/article/51/D1/D690/6764414).

<!-- [claim:C003] [evidence:E02,E05] -->

Model explanations offer a way to identify the descriptors underlying these predictions. Cen and colleagues used SHapley Additive exPlanations (SHAP) to investigate mutation contributions in models of drug resistance in Mycobacterium tuberculosis, illustrating how test-set attribution can connect prediction with interpretable candidate features. In the present protein-level setting, SHAP can rank descriptors contributing to family and drug-class classification. However, attribution measures a feature's contribution to a fitted model; it does not directly demonstrate the biochemical role of that feature. A compositional descriptor may reflect several correlated properties, making additional annotation context valuable for interpretation. [Cen et al., 2025](https://doi.org/10.1016/j.bsheal.2025.11.001).

<!-- [claim:C004] [evidence:E03,E05] -->

Protein annotations provide a complementary representation of sequence information. Pfam and integrated InterPro entries describe matched protein families, domains, and other signatures, while UniProt can supply functional and positional context. Ghosh and colleagues incorporated domain presence and count representations into a multiscale framework for predicting isolate-level antimicrobial resistance, providing a precedent for comparing molecular feature representations. Here, the unit of analysis is an individual CARD protein. Linking its physicochemical measurements with its annotations enables us to test whether model-important descriptors are associated with identifiable protein characteristics, and whether annotation features add predictive information beyond the physicochemical baseline. Because both descriptors and many annotations originate from sequence, such agreement supplies biological context rather than independent experimental confirmation. [InterProScan 5](https://pmc.ncbi.nlm.nih.gov/articles/PMC3998142/); [Ghosh et al., supplied 2025 preprint](https://doi.org/10.1101/2025.07.03.663053).

<!-- [claim:C005] [evidence:E04,E05,E06] -->

Accordingly, this study integrates sequence-derived physicochemical descriptors, explainable machine learning, and protein annotations to characterize and predict CARD-defined ARG family and drug-class memberships. We assess the predictive information in physicochemical descriptors, identify important features through held-out SHAP analysis, compare family and drug-class importance profiles, and examine their associations with Pfam, InterPro, and UniProt annotations. Matched predictor comparisons under sequence-grouped validation assess the additional value of annotation information. The intended contribution is an interpretable account of sequence signatures and their annotation context, together with an assessment of the conditions under which classification is supported by the available data.

<!-- [claim:C006] [evidence:E05,E06] -->

**Overall aim.** To evaluate the predictive and interpretive value of sequence-derived physicochemical descriptors for CARD-defined ARG family and drug-class classification by integrating explainable machine learning with protein-domain and functional annotations.

**Specific objectives.** These objectives define questions and evaluation criteria; they do not assume that every family or drug class will yield a reliable model or an interpretable association.

| Objective | Defined question | Evidence or deliverable |
|---|---|---|
| 1. Evaluate physicochemical classification | To what extent do the retained Pfeature and custom ProFET-style descriptors discriminate supported ARG families and drug classes within the CARD protein cohort? | Per-target held-out AP, AUROC, F1, precision, recall, and confusion matrices; target support and exclusions; grouping method reported with each analysis. |
| 2. Identify informative sequence properties | Which descriptors contribute most strongly to each family and drug-class classifier? | Held-out mean absolute SHAP profiles, ranked descriptors, and fold-specific importance summaries. Biological interpretation requires translating feature codes into their actual measurements. |
| 3. Compare family and drug-class signatures | Which important descriptors are shared across family–drug-class pairs, and how does this overlap relate to CARD linkage? | Top-feature Jaccard overlap, complete-profile Spearman correlation, shared-feature lists, and model-quality flags for linked and unlinked pairs. Similarity is not itself a resistance call. |
| 4. Test annotation context | Are important physicochemical descriptors associated with Pfam/InterPro signatures or UniProt annotations that occur in the relevant target proteins? | Descriptor–annotation effect sizes, length-adjusted associations, cluster-aware statistics and bootstrap intervals, target annotation prevalence, and eligible within-group screens. |
| 5. Assess the contribution of annotations | Do domain and generic UniProt features improve membership prediction over physicochemical descriptors on the same held-out sequence groups? | Matched R1–R5 ablations, per-target performance differences, mapping-coverage sensitivity analyses, and shuffled-label controls. Comparisons are made within the same cohort and folds. |
| 6. Establish a reproducible query workflow | How can compatible query proteins be scored while identifying weak models and inputs outside the represented feature space? | Saved model bundle, consistent feature extraction, training-identity checks, applicability flags, and explicit limits on interpretation. Independent novel-sequence validation remains a separate requirement. |

**Working hypotheses.** First, some supported ARG families and drug classes will have discriminative physicochemical profiles within the CARD cohort. Second, a subset of model-important descriptors will show reproducible associations with annotations present in the corresponding proteins. Third, the predictive contribution of annotations will vary by target and may overlap substantially with information already captured by sequence descriptors. These hypotheses allow null or target-dependent outcomes; neither an annotation gain nor a unique biological explanation is presumed.

**Scope within the wider project.** This introduction addresses the ML–SHAP–annotation component. The existing ESKAPE host-distribution, mobility, and selection analyses can provide a subsequent genomic and evolutionary context once family identifiers, datasets, and inclusion criteria are explicitly reconciled. They should not be described as predictors or validation evidence for these models unless that integration is actually performed. A later objective could test whether families with particular sequence signatures differ in their observed host distribution or genomic location, with relatedness and sampling accounted for.

The present classifiers distinguish memberships among CARD proteins. Their non-target class consists of other proteins in that cohort, not a curated collection of non-ARG bacterial proteins. Consequently, this study should not yet be framed as validated discovery of cryptic ARGs, recognition of completely unseen families, demonstration of new drug resistance, or proof of superiority over sequence-homology methods. Those questions require additional controls and appropriate independent benchmarks. Likewise, the earlier descriptor-grouped SHAP results and later MMseqs2-grouped ablations must remain distinguishable; annotation support does not retroactively change the validation of the SHAP models.

**Suggested title.** *Interpretable classification of antimicrobial resistance gene families and drug classes using sequence-derived physicochemical descriptors and protein annotations.*

**Reference details.** Poorinmohammad N, Hamedi J, Abbaspour Motlagh Moghaddam MH. *Sequence-based analysis and prediction of lantibiotics: A machine learning approach.* Computational Biology and Chemistry. 2018;77:199–206. DOI: [10.1016/j.compbiolchem.2018.10.004](https://doi.org/10.1016/j.compbiolchem.2018.10.004). Locally read sections: Introduction, 2.1–2.4, Discussion, and Conclusion. The first author is Poorinmohammad, so the manuscript citation should use Poorinmohammad et al., rather than Moghaddam et al.

The other linked references support the particular precedents identified in the introduction. The Ghosh reference refers to the supplied October 2025 preprint version. No claim of being the first study to combine these approaches is made; establishing priority would require a dedicated literature review.

**Draft provenance.** Prepared using the scientific-writing skill and local PDF extraction; no scientific analysis was rerun. Drafting assistance reference: Kassis T et al. (2026), *Scientific Agent Skills: A Library of Procedural Knowledge for Research Agents*, [doi:10.48550/arXiv.2609.00065](https://doi.org/10.48550/arXiv.2609.00065). This attribution concerns document preparation and is separate from the biological rationale.

**Edited by Claude (30 September 2026): additions from a full reading of Poorinmohammad et al. (2018).** 
Everything above this heading is unchanged from the earlier draft. This section only adds material and, like the rest of the draft, awaits author review. It supersedes the statement above that only some sections of the paper were read: the whole paper, including Results, Tables 1–7 and Figs 1–2, has now been read. The labels R1–R5 below follow the annotation-ablation design in the methodology draft (section 12); the Cryptic AMR roadmap (section 6) uses the same labels for a different, planned design.

**Design lessons from the lantibiotic precedent.** The full paper adds four practical points and several cautions. The precedent informs design; its performance figures and lantibiotic-specific explanations do not transfer to ARGs.

| Paper practice | What the paper shows | Consequence for this study |
|---|---|---|
| Negative class was other bacterial antimicrobial peptides (§2.1) | The authors accepted lower accuracy so that both classes shared antibacterial activity and the remaining differences were more specific | Our non-target class is other CARD proteins, which serves the same interpretive aim. It is not a non-ARG class and cannot support genome-screen claims |
| Composition-only representation (Table 2) | 20 amino-acid-composition features gave 90.4% SVM accuracy (5-fold), similar to the best multi-descriptor modes (90.4–90.6%). The 8,000-feature 3-mer set did not exceed it (best 86.2%) | Test a composition-only baseline (called R0 here, below the physicochemical-only R1) before crediting the full descriptor set (Objective 1) |
| Class-mean ratios for selected features (§4.2) | Glycine about half, leucine 2.2-fold and glutamate 3-fold relative to the comparison peptides. Most selected features were higher in the *negative* class | Report direction of effect beside mean absolute SHAP (Objective 2) |
| Declined to interpret features that depend on post-translational modification (§4.2) | Values computed on the precursor sequence do not describe the mature peptide | Whole-protein composition of CARD proteins includes signal and transmembrane segments. Use the UniProt signal/transmembrane coverage block (R3 in the annotation-ablation design) as a sensitivity check |

<!-- [claim:C007] [evidence:E01] -->

**Cautions about the precedent.** Feature selection was run once on the full data and the classifiers were then re-scored by cross-validation on the selected features, so the gains reported in its Table 4 are likely optimistic; the paper does not say whether the external sequences were excluded from selection. The external set held 27 sequences (16 lantibiotics, 11 comparison peptides). The reported SMO sensitivity, specificity and accuracy (93.7%, 81.8%, 88.8%) correspond to 15/16, 9/11 and 24/27, with approximate 95% Wilson intervals of 72–99%, 52–95% and 72–96%. That is too small to support the statement that overfitting is excluded. The abstract gives SMO "specificity" as 94%, but Table 5 gives sensitivity 94% and specificity 80%, and the sample counts agree with Table 5, so the abstract should not be quoted for this figure. The text also says nine descriptor modes were selected, but Table 1 lists eight, whose descriptor counts sum to the stated 9,604. Near-duplicates were removed before modelling at different thresholds for the two classes (more than 64% and 90% similarity for lantibiotics and comparison peptides, respectively), and no homology-grouped cross-validation is described. The later MMseqs2-grouped ablations in this project address that concern; the earlier descriptor-grouped SHAP models do so only approximately.

<!-- [claim:C008] [evidence:E01] -->

**Proposed additions to Objectives 1, 2 and 6 (not yet run), and what the notebooks already contain.**
- *Objective 1:* matched composition-only baselines on the same folds as R1, using the Pfeature amino-acid-composition (AAC) block alone and AAC with dipeptide composition (DPC). None of the six ML notebooks searched compares descriptor groups; the annotation ablations vary annotation blocks on top of the full descriptor set. Matthews correlation coefficient is reported in those ablations but not among the paired SHAP classifiers' metrics, so adding it there is also proposed.
- *Objective 2:* per-target, per-descriptor direction of effect (target versus rest, as a median ratio or Cliff's delta) beside the mean absolute SHAP tables used for the family–drug-class comparison and the annotation join, which carry absolute importance only. Mean signed SHAP is already computed in `drug_class_classifier_importance.ipynb`, and signed local attributions are written for query proteins by `ARG_family_drug_class_SHAP_comparison.ipynb`.
- *Objective 6:* any reduced-feature model built from SHAP-selected descriptors must select features inside the training folds. In the notebooks searched, the top-15 lists are used for reporting and overlap comparisons and no model is refitted on them, so no selection leak was found; this is a prospective requirement.
- *Search scope:* cell sources, not saved outputs, of `protein_annotation_physicochemical_integration`, `ARG_family_drug_class_SHAP_comparison`, `family_classifier_importance`, `family_classifier_importance_3`, `SHAP_FEATURE_IMPORTANCE` and `drug_class_classifier_importance`.
Distinguish family-specific signatures from shared resistance-mechanism signatures.
Ask whether a feature distinguishes one family from other families performing a similar function. Comparing a β-lactamase only with all other ARGs may capture broad differences between enzymes and transporters. Comparison with other β-lactamase families tests a more specific distinction.

Suggested objective:

Determine whether informative physicochemical descriptors distinguish ARG families within shared resistance mechanisms.

Measure the stability of the important features.
Highly correlated descriptors can substitute for one another across models. Examine how consistently features appear among the top-ranked predictors across cross-validation folds. Consistent importance would strengthen their interpretation; interchangeable features may be better discussed as a group of related properties.

Suggested objective:

Assess the reproducibility of SHAP-selected descriptors across validation folds and identify groups of correlated predictors.

Test whether annotation explains the feature association.
A descriptor may be associated with a domain globally because different families have different compositions. Within-family comparisons, where annotation variation and sample support permit, can help determine whether the association persists beyond that broad distinction.

Suggested objective:

Evaluate whether descriptor–annotation associations persist within supported ARG families or resistance mechanisms.

The most useful working hypothesis would be:

ARG families exhibit both shared and family-specific physicochemical signatures; protein annotations can explain a subset of these signatures, while sequence-grouped validation determines their predictive usefulness.
<!-- [claim:C009] [evidence:E05,E06,E07] -->
