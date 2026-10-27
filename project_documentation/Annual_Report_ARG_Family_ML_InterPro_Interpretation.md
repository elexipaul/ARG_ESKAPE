# Family-specific sequence features and protein-domain context of CARD antimicrobial-resistance proteins

**Annual-report results and interpretation draft — 5 October 2026**

## 1. Scope and principal finding

The analysis identified family-specific combinations of amino-acid composition, short sequence patterns, residue-group composition and calculated biophysical properties among CARD reference proteins. Integrating the saved SHAP rankings with Pfam and InterPro annotations provided a biological context for several of these discriminating features. The clearest examples were the middle-segment aliphatic index in major facilitator superfamily (MFS) efflux proteins and charged-residue entropy in Erm-like rRNA methyltransferases. β-Lactamase families also exhibited distinct descriptor rankings, although limited sequence-group independence restricted the strength of their validation. These findings describe the features used to distinguish annotated protein families within the analyzed reference collection. [E01](#E01), [E05](#E05), [E08](#E08), [E09](#E09).

The saved classifiers used **LightGBM**, a gradient-boosted tree ensemble. Accordingly, this report attributes the results to LightGBM rather than Random Forest. The feature rankings below come from `ARG_family_drug_class_SHAP_comparison.ipynb` and its saved outputs; they are not pooled with the earlier family-classifier experiment. The existing project methods and biological audit were used to check terminology and interpretive boundaries. [E02](#E02), [E03](#E03), [E27](#E27), [E28](#E28).

The cohort contained **6,059 unique ARO protein records and sequence hashes**, with **6,206 protein–family memberships** because some proteins carried multiple family labels. The protein collection represented 396 families; the saved classifier retained **84 family targets** under its minimum-support and validation requirements. Each protein was represented by **996 descriptors: 956 Pfeature variables and 40 custom ProFET-style/Biopython variables**. These are CARD reference-protein results and should not be presented as family abundances in the ESKAPE genome collection. [E04](#E04), [E22](#E22), [E25](#E25), [E26](#E26).

## 2. What “dominating features” means in this report

For each binary family classifier, feature importance was the **mean absolute SHAP value across all outer-held-out proteins**, including proteins outside the target family. Fold means were weighted by the number of explained proteins. The reported values therefore quantify a descriptor's contribution to family discrimination on the model's raw-margin scale. They do not directly quantify resistance strength, explain the percentage of biological variance, or establish that larger descriptor values increase family membership. Direction is discussed only where it is supported by the separate descriptor–annotation association results. [E03](#E03).

The configured ensemble allowed up to 300 boosting rounds, with learning rate 0.05, 15 leaves per tree, minimum child support of 10, L2 regularization of 1.0 and binary imbalance weighting. The initial SHAP evaluation used descriptor-based similarity groups. The annotation analysis subsequently used **369 MMseqs2 sequence groups**, constructed from relationships meeting 40% identity and 80% coverage of both proteins. The two grouping schemes represent different validation designs; a high initial classifier score does not substitute for validation across the later sequence groups. [E02](#E02), [E10](#E10), [E11](#E11), [E27](#E27).

All 84 family profiles passed the configured **maximum mean absolute SHAP ≤ 2.0** reporting filter. This rule applies to the largest *mean absolute importance in a target profile*, rather than the largest individual-protein attribution. It is a study-specific comparison rule, not a significance test or an assurance of biological validity. The saved top-feature table contained **1,246 positive-importance family–feature entries**: up to 15 per family, with only one nonzero entry for ROB β-lactamase. [E01](#E01), [E23](#E23).

An adjacent-residue dipeptide descriptor ranked first in **51/84 families (60.7%)**, and a conjoint-triad descriptor ranked first in **14/84 (16.7%)**. Together, these short-sequence-pattern descriptors led 65/84 family profiles. This predominance is descriptive: the input schema itself included 400 dipeptide and 343 conjoint-triad variables, together 743/996 predictors. Their frequent selection consequently cannot be presented as statistical enrichment of sequence-order features. [E01](#E01), [E26](#E26).

## 3. How the annotation evidence supports interpretation

InterProScan 5.78-109.0 scanned all cohort proteins using Pfam, PROSITE Patterns, PROSITE Profiles, Gene3D and SUPERFAMILY. Pfam matches were recorded for **6,017/6,059 proteins**, and integrated InterPro entries for **6,054/6,059**. The family-specific interpretations use annotations actually recorded in the target family, with both target and background counts checked. Corresponding Pfam and InterPro terms often describe overlapping evidence and are not counted as independent confirmations. [E12](#E12), [E13](#E13), [E06](#E06).

The reported association coefficient, **ρ**, is the saved length-adjusted rank correlation: ranked descriptor and annotation values were residualized against log protein length before correlation. Global candidates also had to pass the notebook's multiple-testing, effect-size and sequence-group-support screens. Their reported 95% intervals were obtained by resampling whole sequence groups in 2,000 bootstrap attempts. These intervals describe stability within the analyzed cohort after candidate selection. They are exploratory evidence, not independent replication. Unless a paragraph explicitly identifies a within-family analysis, its association compares annotation-positive and annotation-negative proteins across the whole cohort. [E14](#E14).

## 4. Families with interpretable physicochemical–annotation agreement

### 4.1. MFS antibiotic efflux pumps: aliphatic composition provides the clearest connection

For the **119 MFS-labeled proteins**, the leading descriptors were the **aliphatic index of the middle third** (`M_aliphatic_index`, mean absolute SHAP **0.3335**), **tryptophan–glutamate dipeptide composition** (`DPC1_WE`, **0.2802**) and **binary entropy of the buried-accessibility residue group** (`SEP_SA_BU`, **0.1798**). The aliphatic index is calculated from alanine, valine, isoleucine and leucine percentages; it is a composition index rather than a measured membrane property. `SEP_SA_BU` is calculated from a fixed residue group and does not establish actual solvent burial in a structure. [E01](#E01), [E17](#E17), [E18](#E18), [E19](#E19).

The MFS_1 domain **PF07690 occurred in 93/119 MFS proteins and only 3/5,940 proteins outside the family**. Across the cohort, its association with the middle-third aliphatic index was positive (**ρ = 0.215; 95% cluster-bootstrap interval 0.140–0.345**), with median descriptor values of **123.29** in domain-positive and **80.71** in domain-negative proteins. The association involved 40 domain-positive sequence groups; excluding the largest positive group retained a positive coefficient of **0.194**. A separate within-MFS screen also retained this relationship (**ρ = 0.382; panel-adjusted q = 0.0189**), although that within-family result had no additional bootstrap confirmation. [E05](#E05), [E06](#E06), [E15](#E15), [E16](#E16).

This agreement supports a report-level interpretation that the MFS classifier used aliphatic composition associated with the observed transporter-domain architecture. It is consistent with the membrane-transporter annotation, while remaining a sequence-composition observation. The arbitrary middle-third segment cannot be equated with a particular transmembrane helix or domain. The association also does not identify which antibiotic an individual pump transports. In the stricter sequence-grouped experiment, average precision increased from **0.763** with descriptors alone to **0.801** with combined descriptors and annotations, supporting complementary information for this target in the saved comparison. [E05](#E05), [E08](#E08), [E17](#E17).

### 4.2. Erm-like 23S rRNA methyltransferases: charge-related composition accompanies methyltransferase domains

Among **48 Erm-like proteins**, the leading features were **binary Shannon entropy of positively charged residues** (`SEP_PC`, **0.2283**), **isoleucine–alanine dipeptide composition** (`DPC1_IA`, **0.2245**) and the **predicted isoelectric point of the C-terminal third** (`C_isoelectric_point`, **0.2045**). In the installed Pfeature implementation, `SEP_PC` measures the entropy of the histidine/lysine/arginine fraction versus the remaining residues. It summarizes composition, rather than residue positions or conformational disorder. [E01](#E01), [E17](#E17), [E18](#E18), [E19](#E19).

The RrnaAD domain **PF00398 was recorded in 47/48 Erm proteins and 0/6,011 proteins outside this family**. Its global association with `SEP_PC` was positive (**ρ = 0.143; interval 0.071–0.284**), with median entropy **0.697** in domain-positive proteins and **0.532** in domain-negative proteins. Twelve positive sequence groups contributed to this relationship. The broader S-adenosylmethionine-dependent methyltransferase superfamily **IPR029063 occurred in all 48 Erm proteins**, but also in 20 proteins outside the family. [E05](#E05), [E06](#E06).

The results support describing Erm recognition as a combination of charge-related sequence composition and methyltransferase-domain architecture. They do not demonstrate that the SHAP-selected residues contact rRNA or determine a methylation site. In the sequence-grouped ablation, AP was **0.521** for descriptors and **0.979** for the combined input. The annotation block therefore supplied strong discrimination in this CARD cohort. Domain specificity within CARD should not be generalized to all bacterial proteins without testing non-ARG methyltransferases. [E05](#E05), [E08](#E08).

### 4.3. AAC(6′) acetyltransferases: charge and dipeptide composition agree with GNAT annotation, but adding inputs did not improve AP

For **68 AAC(6′) proteins**, the leading features were the **Pfeature double-bond lookup count** (`BTC_D`, **0.2335**), **leucine–arginine dipeptide composition** (`DPC1_LR`, **0.2214**) and **whole-protein predicted isoelectric point** (`isoelectric_point`, **0.2148**). `BTC_D` sums residue-table values and is not normalized by sequence length; it must not be interpreted as measured covalent-bond architecture or catalytic activity. [E01](#E01), [E18](#E18).

The GNAT domain **IPR000182 occurred in all 68 AAC(6′) proteins and 21/5,991 proteins outside the family**. Across the cohort, GNAT annotation was associated with leucine–arginine composition (**ρ = 0.132; interval 0.036–0.283**) and lower predicted isoelectric point (**ρ = −0.147; interval −0.273 to −0.070**). The median isoelectric point was **5.003** in the GNAT-positive group and **8.494** in the negative group. These findings connect two highly ranked descriptors with the observed acetyltransferase-domain context; they do not locate a substrate-binding site. [E05](#E05), [E06](#E06).

The combination of all input blocks reduced sequence-grouped AP from **0.671** to **0.494**. Therefore, the annotation agreement supports interpretation of the existing model but does not establish that the combined classifier improved AAC(6′) prediction. The result argues for comparing input blocks family by family rather than assuming that adding more features improves every target. [E08](#E08).

### 4.4. RND efflux-associated proteins: heterogeneous domain architecture limits a single family-wide explanation

For **109 RND-family proteins**, the highest-ranked descriptors were **tryptophan–proline dipeptide composition** (`DPC1_WP`, **0.5158**), the **Schneider–Wrede quasi-sequence-order histidine term** (`QSO1_SC_H`, **0.3704**) and the **distance distribution of cysteine residues** (`DDR_C`, **0.3690**). The latter measures residue spacing, including terminal gaps, rather than cysteine abundance. The quasi-sequence-order descriptor combines residue count and adjacent-residue physicochemical distances. [E01](#E01), [E18](#E18).

The MdtA-like β-sandwich annotation **IPR058625 occurred in 31/109 RND-labeled proteins and 2/5,950 proteins outside the family**. Its global association with the leading tryptophan–proline descriptor was small (**ρ = −0.070; interval −0.116 to −0.018**). Because this domain covers a subset of the RND label, it cannot explain the feature as a universal signature of all RND proteins. The family label spans multiple annotation architectures in the saved data. Sequence-grouped AP increased from **0.130** for descriptors alone to **0.549** for annotations and **0.507** for the combined input. This pattern suggests that domain architecture helped distinguish the heterogeneous target more effectively than descriptors alone. [E05](#E05), [E06](#E06), [E08](#E08).

### 4.5. 16S rRNA methyltransferases targeting G1405: clear domain correspondence in a small target

The **12 G1405 methyltransferase proteins** were distinguished principally by the **conjoint triad `[C]–[A/G/V]–[I/L/F/P]`** (`CTC_712`, **0.3697**), **asparagine–proline dipeptide composition** (`DPC1_NP`, **0.3185**) and the **Pfeature nitrogen atomic-composition descriptor** (`ATC_N`, **0.3151**). The conjoint-triad value is a normalized reduced-alphabet triplet count, not the percentage of all triplet positions. [E01](#E01), [E18](#E18).

The FmrO Pfam domain **PF07091 occurred in all 12 target proteins and no proteins outside the target**. Its global relationship with `CTC_712` was positive (**ρ = 0.112; interval 0.039–0.258**) and involved five positive sequence groups. This supports a sequence-pattern/domain correspondence in the reference collection. The small positive set and exclusion from the later ablation because valid inner grouped folds could not be formed prevent a strong claim of generalization. No functional interpretation of the nitrogen descriptor beyond its calculated composition is warranted. [E05](#E05), [E06](#E06), [E09](#E09).

### 4.6. Trimethoprim-resistant dihydrofolate reductases: domain information is useful, although the dominant triad remains descriptive

Among **59 dfr-family proteins**, the leading descriptors were the **conjoint triad `[A/G/V]–[R/K]–[R/K]`** (`CTC_155`, **0.2896**), **protein sequence length** (**0.2283**) and **leucine–proline dipeptide composition** (`DPC1_LP`, **0.2252**). The DHFR_1 domain **PF00186 occurred in 49/59 target proteins and no proteins outside the family**. The third-ranked leucine–proline descriptor had a positive global domain association (**ρ = 0.118; interval 0.043–0.236**). The matched stable association supports this third-ranked feature; it should not be substituted as evidence for the top-ranked triad or for length. [E01](#E01), [E05](#E05), [E06](#E06).

Sequence-grouped AP was **0.694** for descriptors, **0.833** for the complete annotation block and **0.681** for the combined input. The saved experiment therefore supports useful annotation discrimination while showing that combining blocks did not improve AP over descriptors alone. The domain correspondence describes DHFR architecture among known ARGs; it does not by itself distinguish resistance-conferring DHFRs from susceptible housekeeping homologues. [E08](#E08).

### 4.7. Chloramphenicol acetyltransferases: annotation context is present, but the top-feature association bridge is absent

For **31 CAT-family proteins**, the dominant features were **glutamine–valine dipeptide composition** (`DPC1_QV`, **0.3977**), **intermediate-accessibility residue-group composition** (`PCP_SA_IN`, **0.1646**) and **aromatic-residue fraction** (`aromaticity`, **0.1592**). The CAT domain **PF00302 occurred in 22/31 target proteins**, while nine target proteins carried the hexapeptide annotation **PF00132**. These different signatures document annotation heterogeneity within the family. [E01](#E01), [E06](#E06), [E18](#E18).

The saved SHAP–domain context table contained no matched stable Pfam/InterPro association for CAT's selected features. Consequently, the report can state the feature rankings and the recorded domain architectures, but cannot claim that the domain analysis statistically explains those rankings. The later sequence-grouped ablation also excluded this target because valid inner grouped folds could not be formed. CAT is retained here as an example of a biologically recognizable family for which the available feature-to-domain link remains incomplete. [E05](#E05), [E09](#E09).

## 5. β-Lactamases: distinct family classifiers within overlapping domain architectures

The β-lactamase results do not support a single physicochemical signature common to every family. Different classifiers prioritized different dipeptides, reduced-alphabet triads or segment-level properties. Domain annotations provide shared structural context, while the family-specific rankings describe how the models separated members of that broader collection. [E01](#E01), [E06](#E06).

| Family | n proteins | Three leading descriptors, in rank order | Recorded domain context |
|---|---:|---|---|
| CTX-M | 267 | N-terminal-third aromaticity (0.2836); carbon atomic composition (0.2343); methionine–cysteine dipeptide composition (0.2242) | PF13354 and IPR045155 in 267/267; class-A active-site entry IPR023650 in 266/267 |
| SHV | 216 | C-terminal-third isoelectric point (0.3121); tyrosine residue-distance distribution (0.2157); aromatic-group binary entropy (0.1905) | PF13354 and IPR045155 in 216/216; IPR023650 in 213/216 |
| TEM | 216 | Glutamate–leucine (0.2919), threonine–methionine (0.1962) and leucine–threonine (0.1883) dipeptide composition | PF13354, IPR045155 and IPR023650 in 216/216 |
| KPC | 231 | Alanine–valine (0.4474), proline–isoleucine (0.3087) and tryptophan–proline (0.2498) dipeptide composition | PF13354 and IPR045155 in 231/231 |
| NDM | 71 | Phenylalanine–glycine dipeptide composition (0.1271); `[H/N/Q/W]–[H/N/Q/W]–[Y/M/T/S]` conjoint triad (0.1165); methionine–glutamate dipeptide composition (0.1015) | Metallo-β-lactamase PF00753 and IPR001279 in 71/71 |
| PDC | 639 | Arginine–leucine (0.8552), glycine–tyrosine (0.6508) and glycine–leucine (0.5964) dipeptide composition | PF00144 and IPR001466 in 639/639; class-C active-site entry IPR001586 in 637/639 |
| OXA without subfamily | 80 | Glycine–tryptophan (0.5116), leucine–glycine (0.2503) and aspartate–asparagine (0.2178) dipeptide composition | PF00905 and IPR001460 in 80/80; class-D active-site entry IPR002137 in 54/80 |

Numbers in parentheses are mean absolute SHAP, not feature values or biological effect sizes. Counts and names follow the saved labels; the OXA target refers specifically to **“OXA beta-lactamase without subfamily.”** [E01](#E01), [E06](#E06), [E18](#E18).

### 5.1. CTX-M and SHV: aromatic and charge-related descriptors have broad-domain support

For CTX-M, the top-ranked N-terminal-third aromaticity was negatively associated with the class-A active-site annotation **IPR023650** across the cohort (**ρ = −0.374; interval −0.527 to −0.014**). Median aromaticity in annotated proteins was **0.0526**, compared with **0.0866** in unannotated proteins. This links a leading CTX-M descriptor with a broader class-A annotation pattern, rather than demonstrating an effect specific to CTX-M. The terminal third is a fixed sequence partition and cannot be equated with an active-site region. [E05](#E05), [E17](#E17).

For SHV, the third-ranked aromatic-group entropy was negatively associated with **PF13354** (**ρ = −0.669; interval −0.773 to −0.059**). All 216 SHV proteins carried this domain, but so did 1,328 proteins outside the SHV family. The relationship thus supports a broad-domain compositional pattern. It does not establish that aromatic entropy distinguishes SHV from other class-A families. A related global PF13354–aromaticity sensitivity analysis found that **93.7% of annotated proteins belonged to one sequence group**; removing that group reduced the coefficient from **−0.671 to −0.185**. This concentration limits any general claim about aromatic depletion across β-lactamases. [E05](#E05), [E06](#E06), [E15](#E15).

### 5.2. TEM, KPC and PDC: leading dipeptides are discriminating patterns, not identified catalytic motifs

The highest-ranked TEM, KPC and PDC features were different adjacent-residue pairs. Their annotations support β-lactamase-family context, but the saved stable associations principally involved lower-ranked descriptors: **tyrosine–serine composition for TEM** at rank 9 with IPR045155 (**ρ = 0.468; interval 0.047–0.551**), **threonine–glycine composition for KPC** at rank 4 with IPR012338 (**ρ = 0.344; interval 0.148–0.516**) and **glutamine–proline composition for PDC** at rank 5 with IPR012338 (**ρ = 0.379; interval 0.158–0.695**). The broad IPR012338 annotation is shared with many other proteins in the cohort. [E01](#E01), [E05](#E05), [E06](#E06).

The warranted interpretation is that short sequence composition helped separate these family labels within a shared structural background. A dipeptide count does not identify its position, exposure, catalytic role or contribution to antibiotic specificity. The lower-ranked feature associations should not be used to give the three leading dipeptides an unsupported mechanism. [E03](#E03), [E18](#E18).

### 5.3. NDM: metallo-β-lactamase domains are confirmed, but a familiar global association is not a dominant NDM feature

Every NDM protein carried the metallo-β-lactamase domain **PF00753**. Across the broader cohort, PF00753 was associated with **glycine–histidine dipeptide composition** (`DPC1_GH`; **ρ = 0.562; interval 0.149–0.701**), involving 492 annotated proteins in eight positive sequence groups. Removing the largest positive group retained **ρ = 0.401**. However, **`DPC1_GH` was not among NDM's selected top 15 SHAP features**. It can be reported as a general domain-associated compositional observation, but should not be described as a feature dominating the NDM classifier. The actual leading NDM features are shown in the table above. [E01](#E01), [E06](#E06), [E07](#E07), [E15](#E15).

### 5.4. OXA without subfamily: the top dipeptide agrees with a broad structural annotation

The leading **glycine–tryptophan composition** was positively associated with **PF00905**, recorded as Transpeptidase (**ρ = 0.628; interval 0.008–0.782**). This annotation occurred in all 80 target proteins but also in 1,213 proteins outside the target; only eight annotation-positive sequence groups contributed to the global association. It provides broad structural context and does not imply that these proteins have experimentally demonstrated transpeptidase activity. The wide interval and extensive annotation sharing warrant restraint in interpreting the relatively large point estimate. No additional antibiotic phenotype is established by this correspondence. [E05](#E05), [E06](#E06).

### 5.5. Validation limits for these β-lactamase families

CTX-M, SHV, TEM, KPC, NDM and PDC were excluded from the all-protein sequence-grouped ablation because each target's positive proteins occupied **only one sequence group** under the saved MMseqs2 grouping. OXA without subfamily was excluded because valid inner grouped folds could not be formed. The SHAP rankings and observed domain matches remain reportable descriptive results; the available ablation cannot demonstrate generalization of these individual targets to independent homologous groups. [E09](#E09), [E10](#E10).

## 6. What the combined-model comparison actually shows

The table below reports the saved all-protein, observed-label **sequence-grouped** results for five discussed families with completed comparisons. AP is average precision. R2 contains Pfam presence, count and quantitative domain variables plus InterPro presence; R4 adds generic UniProt annotation measurements; R5 combines annotations with physicochemical descriptors. These results used two outer folds and are distinct from the earlier SHAP-ranking validation. [E08](#E08), [E14](#E14), [E24](#E24).

| Family | Physicochemical AP (R1) | Pfam/InterPro AP (R2) | All annotations AP (R4) | Combined AP (R5) |
|---|---:|---:|---:|---:|
| major facilitator superfamily (MFS) antibiotic efflux pump | 0.763 | 0.735 | 0.766 | 0.801 |
| Erm-like 23S rRNA methyltransferase | 0.521 | 0.979 | 0.979 | 0.979 |
| AAC(6') | 0.671 | 0.385 | 0.366 | 0.494 |
| resistance-nodulation-cell division (RND) antibiotic efflux pump | 0.130 | 0.306 | 0.549 | 0.507 |
| trimethoprim resistant dihydrofolate reductase dfr | 0.694 | 0.833 | 0.833 | 0.681 |

Annotation information markedly improved discrimination for Erm and RND targets, while MFS showed a smaller improvement with the combined input. AAC(6′) and dfr illustrate that the combined model could have lower AP than a simpler input block. These are matched results from the saved comparison, without a claim of statistical significance for the performance differences. They support selecting and interpreting representations at the family level rather than treating annotation addition as uniformly beneficial. [E08](#E08).

## 7. Annual-report conclusion and guide for the next stage

The completed analysis shows that CARD family discrimination depends on multiple levels of sequence information. Short sequence-pattern descriptors frequently ranked highest, while interpretable biophysical descriptors supplied particularly coherent examples for MFS and Erm. Domain annotations linked several important descriptors to recorded family architectures and helped explain why some models recognized their targets. Agreement between SHAP ranking and annotation association was strongest when the feature was highly ranked, the annotation was common in the target and uncommon in the background, and the relationship persisted across multiple sequence groups. [E01](#E01), [E05](#E05), [E06](#E06), [E15](#E15).

The biological conclusion remains **family-associated sequence and domain signatures within known CARD proteins**. Global domain associations, structural annotation matches and classifier importance do not establish causal residue functions, novel resistance phenotypes or clinical susceptibility. The negative class in each family model comprised other reference proteins rather than a validated collection of non-ARG proteins. The same-cohort annotation interpretation and restricted sequence-group support remain material limits of the study. [E03](#E03), [E14](#E14), [E22](#E22), [E25](#E25).

The supplied lantibiotics paper offers a useful precedent for interpreting selected sequence descriptors in relation to biological labels and for choosing an appropriate comparator set. Poorinmohammad and colleagues analyzed lantibiotics against non-lantibiotic antimicrobial peptides; its dataset and task differ from this ARG-family study. It is used here as conceptual guidance, without importing its selected features, performance or biological conclusions into the present results. [E21](#E21).

The next stage should prioritize the following evidence gaps:

1. **Test family signatures against appropriate non-ARG homologues.** Include non-resistance methyltransferases, housekeeping DHFRs and related transport proteins so that shared architecture is distinguished from resistance-associated membership.
2. **Expand independent sequence-group support.** Reassess β-lactamase targets on independently held-out or external proteins with explicit relatedness checks. The existing exclusions should remain visible in the annual report.
3. **Examine signed, within-family effects.** Use signed local SHAP and appropriate within-family analyses before asserting that a particular descriptor increases a prediction. Do not equate global importance with enrichment.
4. **Localize candidate sequence patterns before proposing mechanisms.** Map implicated residue pairs or regions onto alignments and domain coordinates, and inspect relevant structural evidence. Functional claims would require additional validation.
5. **Retain family-specific representation comparisons.** The saved AAC(6′) and dfr results show why additional features should be evaluated against simpler models rather than assumed to improve prediction.

These are proposed follow-up analyses, not results already completed in the project.

## Appendix A. Complete family catalogue

The following table lists all **84 modeled families**, their positive-protein counts and their three highest **nonzero** SHAP importances from the current paired classifier. The complete export, including up to 15 features per family and their expanded calculation-level definitions, is available in [family_top_features_expanded.csv](Annual_Report_ARG_Family_ML_InterPro_evidence/family_top_features_expanded.csv). The initial model flag uses the notebook's AP and F1 thresholds of 0.50; it does not assert sequence-grouped or external validation. [E01](#E01), [E22](#E22).

| CARD family label | n proteins | Rank 1: feature (mean absolute SHAP) | Rank 2 | Rank 3 | Initial model flag |
|---|---:|---|---|---|---|
| 16S rRNA methyltransferase (G1405) | 12 | `CTC_712` (0.3697) | `DPC1_NP` (0.3185) | `ATC_N` (0.3151) | AP and F1 ≥ 0.50 |
| AAC(3) | 20 | `net_charge_pH_7` (0.2501) | `DPC1_VM` (0.2420) | `CTC_223` (0.1796) | AP and F1 ≥ 0.50 |
| AAC(6') | 68 | `BTC_D` (0.2335) | `DPC1_LR` (0.2214) | `isoelectric_point` (0.2148) | AP and F1 ≥ 0.50 |
| AAC(6')-Ib-cr | 10 | `DPC1_PY` (0.0908) | `DPC1_DP` (0.0363) | `DPC1_RW` (0.0151) | AP and F1 ≥ 0.50 |
| ACC beta-lactamase | 11 | `DPC1_NY` (0.1706) | `CTC_113` (0.0319) | `DPC1_TH` (0.0268) | AP and F1 ≥ 0.50 |
| ACT beta-lactamase | 171 | `DPC1_RM` (0.2749) | `CTC_516` (0.2172) | `CTC_771` (0.2158) | AP and F1 ≥ 0.50 |
| ADC beta-lactamase without carbapenemase activity | 51 | `DPC1_QN` (0.5232) | `DPC1_AI` (0.1723) | `DDR_D` (0.1618) | AP or F1 < 0.50 |
| ADC beta-lactamases pending classification for carbapenemase activity | 274 | `DPC1_KY` (0.2945) | `DPC1_WK` (0.2780) | `CTC_226` (0.1975) | AP and F1 ≥ 0.50 |
| ANT(3'') | 29 | `aliphatic_index` (0.3164) | `DPC1_EW` (0.2708) | `AAC_W` (0.2571) | AP and F1 ≥ 0.50 |
| APH(3') | 17 | `DPC1_WL` (0.1663) | `AAC_D` (0.1646) | `SER_D` (0.1184) | AP and F1 ≥ 0.50 |
| ATP-binding cassette (ABC) antibiotic efflux pump | 29 | `DPC1_TI` (0.3318) | `DPC1_TP` (0.1961) | `CTC_615` (0.1640) | AP or F1 < 0.50 |
| BlaB beta-lactamase | 37 | `CTC_564` (0.1311) | `DPC1_GK` (0.0622) | `DPC1_KF` (0.0550) | AP and F1 ≥ 0.50 |
| CARB beta-lactamase | 54 | `DPC1_LK` (0.2022) | `SEP_HX` (0.1679) | `CTC_546` (0.1573) | AP and F1 ≥ 0.50 |
| CMH beta-lactamase | 32 | `CTC_412` (0.1625) | `DPC1_SW` (0.1489) | `CTC_311` (0.1211) | AP and F1 ≥ 0.50 |
| CMY beta-lactamase | 193 | `CTC_344` (0.2941) | `CTC_214` (0.2672) | `DPC1_TW` (0.1778) | AP and F1 ≥ 0.50 |
| CTX-M beta-lactamase | 267 | `N_aromaticity` (0.2836) | `ATC_C` (0.2343) | `DPC1_MC` (0.2242) | AP and F1 ≥ 0.50 |
| CfiA beta-lactamase | 21 | `DPC1_NQ` (0.0900) | `DPC1_CM` (0.0666) | `DPC1_GG` (0.0190) | AP and F1 ≥ 0.50 |
| Cfr-like 23S rRNA methyltransferase | 11 | `CTC_714` (0.3244) | `CTC_117` (0.1025) | `DPC1_FM` (0.0825) | AP and F1 ≥ 0.50 |
| DHA beta-lactamase | 32 | `N_isoelectric_point` (0.3669) | `DPC1_MT` (0.2150) | `CTC_115` (0.1707) | AP and F1 ≥ 0.50 |
| Erm-like 23S rRNA methyltransferase | 48 | `SEP_PC` (0.2283) | `DPC1_IA` (0.2245) | `C_isoelectric_point` (0.2045) | AP and F1 ≥ 0.50 |
| FONA beta-lactamase | 13 | `CTC_362` (0.1371) | `CTC_215` (0.0403) | `DPC1_AK` (0.0350) | AP and F1 ≥ 0.50 |
| FOX beta-lactamase | 19 | `DPC1_TY` (0.1930) | `DPC1_YP` (0.1228) | `CTC_313` (0.1187) | AP and F1 ≥ 0.50 |
| FRI beta-lactamase | 13 | `CTC_113` (0.1734) | `DPC1_NS` (0.0862) | `DPC1_AI` (0.0777) | AP and F1 ≥ 0.50 |
| GES beta-lactamase | 64 | `CTC_265` (0.1583) | `DPC1_ER` (0.1455) | `DPC1_RF` (0.1133) | AP and F1 ≥ 0.50 |
| GOB beta-lactamase | 53 | `DPC1_TG` (0.2467) | `SER_Y` (0.1600) | `DPC1_MP` (0.1528) | AP and F1 ≥ 0.50 |
| IMI beta-lactamase | 28 | `DPC1_LN` (0.1655) | `CTC_111` (0.1492) | `DPC1_SQ` (0.0904) | AP and F1 ≥ 0.50 |
| IMP beta-lactamase | 100 | `CTC_151` (0.2045) | `DPC1_KL` (0.1522) | `CTC_661` (0.1348) | AP and F1 ≥ 0.50 |
| IND beta-lactamase | 19 | `DPC1_AT` (0.1979) | `CTC_362` (0.0578) | `QSO1_SC_A` (0.0570) | AP and F1 ≥ 0.50 |
| KPC beta-lactamase | 231 | `DPC1_AV` (0.4474) | `DPC1_PI` (0.3087) | `DPC1_WP` (0.2498) | AP and F1 ≥ 0.50 |
| LEN beta-lactamase | 44 | `DPC1_GM` (0.1668) | `DPC1_GP` (0.1404) | `DPC1_MV` (0.1227) | AP and F1 ≥ 0.50 |
| MCR phosphoethanolamine transferase | 105 | `DPC1_HD` (0.1973) | `DPC1_CM` (0.1784) | `C_net_charge_pH_9` (0.1593) | AP and F1 ≥ 0.50 |
| MIR beta-lactamase | 32 | `DPC1_KP` (0.2894) | `DDR_C` (0.2326) | `CTC_371` (0.1728) | AP and F1 ≥ 0.50 |
| MOX beta-lactamase | 25 | `N_molecular_weight_da` (0.2121) | `DPC1_AY` (0.1820) | `AAC_T` (0.1606) | AP and F1 ≥ 0.50 |
| Miscellaneous ABC-F subfamily ATP-binding cassette ribosomal protection proteins | 12 | `QSO1_SC1` (0.3503) | `DPC1_LD` (0.2342) | `CTC_171` (0.1086) | AP and F1 ≥ 0.50 |
| NDM beta-lactamase | 71 | `DPC1_FG` (0.1271) | `CTC_443` (0.1165) | `DPC1_ME` (0.1015) | AP and F1 ≥ 0.50 |
| OKP beta-lactamase | 47 | `DPC1_QQ` (0.3390) | `DPC1_PL` (0.1471) | `CTC_212` (0.1346) | AP and F1 ≥ 0.50 |
| OXA beta-lactamase without subfamily | 80 | `DPC1_GW` (0.5116) | `DPC1_LG` (0.2503) | `DPC1_DN` (0.2178) | AP and F1 ≥ 0.50 |
| OXA-1-like beta-lactamase | 12 | `DPC1_ST` (0.1129) | `DPC1_TI` (0.0185) | `CTC_432` (0.0179) | AP and F1 ≥ 0.50 |
| OXA-10-like beta-lactamase | 65 | `DPC1_TF` (0.1605) | `CTC_753` (0.1302) | `CTC_353` (0.1129) | AP and F1 ≥ 0.50 |
| OXA-114-like beta-lactamase | 26 | `DPC1_WR` (0.1042) | `CTC_114` (0.0300) | `CTC_145` (0.0273) | AP and F1 ≥ 0.50 |
| OXA-12-like beta-lactamase | 11 | `DPC1_WF` (0.1023) | `DPC1_QY` (0.0215) | `CTC_456` (0.0167) | AP and F1 ≥ 0.50 |
| OXA-134-like beta-lactamase | 28 | `DPC1_QE` (0.1033) | `AAC_Q` (0.0606) | `CTC_415` (0.0598) | AP and F1 ≥ 0.50 |
| OXA-143-like beta-lactamase | 11 | `DPC1_PI` (0.3346) | `CTC_413` (0.3015) | `DPC1_KS` (0.1974) | AP and F1 ≥ 0.50 |
| OXA-184-like beta-lactamase | 39 | `DPC1_WK` (0.1369) | `DPC1_DN` (0.1110) | `SEP_SC` (0.0980) | AP and F1 ≥ 0.50 |
| OXA-2-like beta-lactamase | 31 | `DPC1_SD` (0.1642) | `DPC1_FA` (0.1358) | `DPC1_NP` (0.0928) | AP and F1 ≥ 0.50 |
| OXA-211-like beta-lactamase | 18 | `DPC1_TG` (0.1617) | `DPC1_QQ` (0.0895) | `CTC_565` (0.0762) | AP and F1 ≥ 0.50 |
| OXA-213-like beta-lactamase | 103 | `DPC1_KR` (0.2564) | `CTC_264` (0.1493) | `DPC1_DW` (0.1475) | AP and F1 ≥ 0.50 |
| OXA-229-like beta-lactamase | 22 | `CTC_416` (0.1458) | `DPC1_MK` (0.0373) | `CTC_134` (0.0372) | AP and F1 ≥ 0.50 |
| OXA-23-like beta-lactamase | 51 | `aliphatic_index` (0.2517) | `CTC_141` (0.1399) | `PCP_Z3` (0.1304) | AP and F1 ≥ 0.50 |
| OXA-24-like beta-lactamase | 14 | `DPC1_NF` (0.0813) | `CTC_561` (0.0688) | `DPC1_TY` (0.0572) | AP and F1 ≥ 0.50 |
| OXA-286-like beta-lactamase | 17 | `DPC1_QQ` (0.1970) | `CTC_551` (0.0900) | `DPC1_MS` (0.0877) | AP and F1 ≥ 0.50 |
| OXA-294-like beta-lactamase | 13 | `CTC_744` (0.1744) | `DDR_Q` (0.0860) | `CTC_131` (0.0812) | AP and F1 ≥ 0.50 |
| OXA-48-like beta-lactamase | 67 | `DPC1_WN` (0.3268) | `AAC_W` (0.0300) | `DPC1_MP` (0.0288) | AP and F1 ≥ 0.50 |
| OXA-50-like beta-lactamase | 104 | `DPC1_WE` (0.1298) | `DPC1_WW` (0.0934) | `DPC1_WC` (0.0923) | AP and F1 ≥ 0.50 |
| OXA-51-like beta-lactamase | 383 | `DPC1_GN` (0.7826) | `CTC_461` (0.4437) | `DPC1_TT` (0.2097) | AP and F1 ≥ 0.50 |
| OXA-61-like beta-lactamase | 49 | `DPC1_TF` (0.1269) | `DPC1_ME` (0.1250) | `DPC1_FV` (0.1189) | AP and F1 ≥ 0.50 |
| OXA-62-like beta-lactamase | 15 | `DPC1_PC` (0.1678) | `PAAC1_lam1` (0.1141) | `DPC1_TY` (0.0913) | AP and F1 ≥ 0.50 |
| OXA-63-like beta-lactamase | 23 | `DPC1_NS` (0.1169) | `CTC_426` (0.0470) | `DPC1_DN` (0.0409) | AP and F1 ≥ 0.50 |
| OXY beta-lactamase | 73 | `DPC1_QS` (0.1539) | `APAAC1_HB_lam1` (0.1481) | `DPC1_WS` (0.1275) | AP and F1 ≥ 0.50 |
| PDC beta-lactamase | 639 | `DPC1_RL` (0.8552) | `DPC1_GY` (0.6508) | `DPC1_GL` (0.5964) | AP and F1 ≥ 0.50 |
| PEN-A beta-lactamase | 35 | `DPC1_CA` (0.1001) | `DPC1_RI` (0.0901) | `DPC1_FC` (0.0664) | AP and F1 ≥ 0.50 |
| PER beta-lactamase | 18 | `DPC1_FE` (0.1770) | `DPC1_AQ` (0.1021) | `DPC1_LL` (0.0575) | AP and F1 ≥ 0.50 |
| ROB beta-lactamase | 12 | `CTC_345` (0.0976) | No further nonzero feature | No further nonzero feature | AP and F1 ≥ 0.50 |
| SHV beta-lactamase | 216 | `C_isoelectric_point` (0.3121) | `DDR_Y` (0.2157) | `SEP_AR` (0.1905) | AP and F1 ≥ 0.50 |
| TEM beta-lactamase | 216 | `DPC1_EL` (0.2919) | `DPC1_TM` (0.1962) | `DPC1_LT` (0.1883) | AP and F1 ≥ 0.50 |
| VEB beta-lactamase | 39 | `QSO1_SC_N` (0.1367) | `C_instability_index` (0.0790) | `DDR_R` (0.0648) | AP and F1 ≥ 0.50 |
| VIM beta-lactamase | 90 | `DPC1_YP` (0.1493) | `CTC_161` (0.1317) | `DPC1_TH` (0.1048) | AP and F1 ≥ 0.50 |
| Van ligase | 13 | `DPC1_GC` (0.4003) | `DDR_W` (0.1934) | `DPC1_EV` (0.1881) | AP and F1 ≥ 0.50 |
| antibiotic efflux regulatory protein | 38 | `PCP_NE` (0.2442) | `aliphatic_index` (0.2046) | `SEP_NE` (0.2002) | AP or F1 < 0.50 |
| chloramphenicol acetyltransferase (CAT) | 31 | `DPC1_QV` (0.3977) | `PCP_SA_IN` (0.1646) | `aromaticity` (0.1592) | AP and F1 ≥ 0.50 |
| fosfomycin thiol transferase | 30 | `M_sequence_length` (0.1803) | `SEP_AR` (0.1761) | `CTC_334` (0.1201) | AP and F1 ≥ 0.50 |
| glycopeptide resistance gene cluster | 78 | `DPC1_NT` (0.2871) | `DDR_Y` (0.2344) | `DDR_C` (0.2267) | AP and F1 ≥ 0.50 |
| lincosamide nucleotidyltransferase (LNU) | 11 | `PCP_NC` (0.1439) | `DPC1_FH` (0.1363) | `CTC_344` (0.1043) | AP and F1 ≥ 0.50 |
| macrolide phosphotransferase (MPH) | 15 | `BTC_D` (0.2025) | `AAC_W` (0.1418) | `SEP_NC` (0.1270) | AP and F1 ≥ 0.50 |
| major facilitator superfamily (MFS) antibiotic efflux pump | 119 | `M_aliphatic_index` (0.3335) | `DPC1_WE` (0.2802) | `SEP_SA_BU` (0.1798) | AP and F1 ≥ 0.50 |
| nitroimidazole reductase | 12 | `DPC1_FH` (0.1210) | `CTC_311` (0.0874) | `DPC1_YP` (0.0363) | AP and F1 ≥ 0.50 |
| quinolone resistance protein (qnr) | 108 | `CTC_762` (0.2165) | `DPC1_DL` (0.1019) | `N_aliphatic_index` (0.0800) | AP and F1 ≥ 0.50 |
| resistance-nodulation-cell division (RND) antibiotic efflux pump | 109 | `DPC1_WP` (0.5158) | `QSO1_SC_H` (0.3704) | `DDR_C` (0.3690) | AP and F1 ≥ 0.50 |
| small multidrug resistance (SMR) antibiotic efflux pump | 12 | `DPC1_VL` (0.1417) | `QSO1_SC1` (0.1185) | `BTC_D` (0.0646) | AP and F1 ≥ 0.50 |
| tetracycline inactivation enzyme | 17 | `ATC_N` (0.2045) | `QSO1_SC1` (0.1737) | `CTC_211` (0.1715) | AP and F1 ≥ 0.50 |
| tetracycline-resistant ribosomal protection protein | 19 | `QSO1_SC1` (0.3050) | `DPC1_EV` (0.2634) | `APAAC1_HL_lam1` (0.1963) | AP and F1 ≥ 0.50 |
| trimethoprim resistant dihydrofolate reductase dfr | 59 | `CTC_155` (0.2896) | `sequence_length` (0.2283) | `DPC1_LP` (0.2252) | AP and F1 ≥ 0.50 |
| vanR | 13 | `DPC1_DD` (0.2315) | `DPC1_TV` (0.0924) | `DDR_W` (0.0620) | AP and F1 ≥ 0.50 |
| vanS | 12 | `N_aromaticity` (0.3335) | `aliphatic_index` (0.3108) | `DPC1_HD` (0.1696) | AP and F1 ≥ 0.50 |

## Appendix B. Reading descriptor names without overinterpreting them

| Descriptor | Meaning and interpretation boundary |
|---|---|
| `DPC1_XY` | Percentage of adjacent X–Y residue pairs. It contains no positional or structural localization. |
| `CTC_ijk` | Conjoint triad after reducing residues to seven groups: 1 = A/G/V; 2 = I/L/F/P; 3 = Y/M/T/S; 4 = H/N/Q/W; 5 = R/K; 6 = D/E; 7 = C. The installed normalization is `(count − minimum count)/maximum count`. |
| `SEP_*` | Binary Shannon entropy of the specified residue-group fraction versus its complement. It measures composition, not spatial disorder or residue arrangement. |
| `SEP` | Whole-protein Shannon entropy across amino-acid composition, `−Σ pᵢ log₂ pᵢ`. This differs from the binary property-group entropy descriptors. |
| `PCP_*` | Mean lookup property or fraction in a fixed physicochemical residue group. Helix/accessibility labels are residue classifications, not measured secondary structure or solvent exposure. |
| `DDR_X` | Distance distribution of residue X, including terminal gaps; it is not X's abundance. |
| `QSO1_*` | Lag-1 quasi-sequence-order descriptor. Residue terms combine counts with weighted physicochemical sequence-distance normalization. |
| `SER_X` | Residue entropy term; installed Pfeature uses `p log₂ p` without the usual leading minus sign. |
| `ATC_*` / `BTC_*` | Atom-composition or bond-lookup descriptors calculated from package tables. Bond counts are not normalized by length or measured from a structure. |
| `N_`, `M_`, `C_` | N-terminal, middle and C-terminal thirds in the custom ProFET-style extraction. These boundaries are sequence partitions, not domain boundaries. |
| `aromaticity` | Fraction of F/W/Y residues in the specified sequence region. |
| `aliphatic_index` | `A% + 2.9V% + 3.9(I% + L%)` in the specified region. |
| `isoelectric_point`, `net_charge_pH_*`, `gravy` | Calculated isoelectric point, net charge at the specified pH, and grand average of hydropathicity; none is an experimental measurement. |

Definitions were checked against the extraction notebook, installed implementation and local manual. The companion expanded-feature CSV preserves the exact feature codes alongside these meanings. [E17](#E17), [E18](#E18), [E19](#E19), [E20](#E20).

## Appendix C. Evidence files and source locators

This report was prepared by reading and aggregating existing saved results. No classifier fitting, protein annotation or inferential testing was rerun. The source checksums, numerical consistency checks and source-linked passage register are recorded in [evidence_manifest.json](Annual_Report_ARG_Family_ML_InterPro_evidence/evidence_manifest.json). The document is an author-review draft; source traceability does not replace independent biological verification.

| Companion file | Purpose |
|---|---|
| [Selected feature–annotation evidence](Annual_Report_ARG_Family_ML_InterPro_evidence/selected_feature_annotation_evidence.csv) | Exact joined rows used in the detailed cases, including SHAP rank, target prevalence, coefficients and bootstrap intervals |
| [Selected family annotation prevalence](Annual_Report_ARG_Family_ML_InterPro_evidence/selected_family_annotation_prevalence.csv) | Recorded target and background annotation counts for the discussed families |
| [Sequence-grouped validation](Annual_Report_ARG_Family_ML_InterPro_evidence/selected_sequence_grouped_validation.csv) | Saved representation-comparison metrics for the discussed targets |
| [Sequence-grouped exclusions](Annual_Report_ARG_Family_ML_InterPro_evidence/selected_sequence_grouped_exclusions.csv) | Recorded reasons preventing evaluation of other discussed targets |

| ID | Primary source and exact locator |
|---|---|
| <a id="E01"></a>E01 | [all_targets_top_features_shap.csv](<../CARD_DATA/card-data/refined_details/Final_Results/ML_ARG_family_drug_class_comparison/all_targets_top_features_shap.csv>) — target_type=family; label and rank identify each reported feature. |
| <a id="E02"></a>E02 | [run_config.json](<../CARD_DATA/card-data/refined_details/Final_Results/ML_ARG_family_drug_class_comparison/run_config.json>) — params, grouping, inner_folds and package_versions. |
| <a id="E03"></a>E03 | [ARG_family_drug_class_SHAP_comparison.ipynb](<../ARG_family_drug_class_SHAP_comparison.ipynb>) — 1-based cells 11 (fit_target/checked_explanation) and 17 (profile filter/export). |
| <a id="E04"></a>E04 | [data_audit.json](<../CARD_DATA/card-data/refined_details/Final_Results/ML_ARG_family_drug_class_comparison/data_audit.json>) — unique_aro_records, retained_features and represented_families. |
| <a id="E05"></a>E05 | [SHAP_feature_domain_context.csv](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/SHAP_feature_domain_context.csv>) — Exact family label/feature/annotation; n_target_annotated>0. Selected rows exported as A01–A13. |
| <a id="E06"></a>E06 | [target_annotation_prevalence.csv](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/target_annotation_prevalence.csv>) — Exact family label/annotation; retain numerator and denominator. |
| <a id="E07"></a>E07 | [stable_exploratory_associations.csv](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/stable_exploratory_associations.csv>) — Global annotation/feature rows; includes PF00753/DPC1_GH and PF13354/aromaticity. |
| <a id="E08"></a>E08 | [ablation_metrics.csv](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/ablation_metrics.csv>) — cohort=all_proteins, mode=observed, target_type=family; exact label/run. |
| <a id="E09"></a>E09 | [ablation_exclusions.csv](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/ablation_exclusions.csv>) — cohort=all_proteins and target_type=family; exact label/reason. |
| <a id="E10"></a>E10 | [sequence_cluster_provenance.json](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/sequence_cluster_provenance.json>) — MMseqs2 identity, coverage, method and version. |
| <a id="E11"></a>E11 | [sequence_clusters.csv](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/sequence_clusters.csv>) — Number of saved sequence groups. |
| <a id="E12"></a>E12 | [interpro_provenance.json](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/interpro_provenance.json>) — release, applications and library_releases. |
| <a id="E13"></a>E13 | [annotation_coverage.csv](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/annotation_coverage.csv>) — pfam_present/interpro_present counts over 6059 records. |
| <a id="E14"></a>E14 | [protein_annotation_physicochemical_integration.ipynb](<../protein_annotation_physicochemical_integration.ipynb>) — 1-based cells 14–15: association_screen, partial_rank and bootstrap_pair; ablation code defines input blocks. |
| <a id="E15"></a>E15 | [biological_case_sensitivity.csv](<Protein_Annotation_Audit_2026-09-22/biological_case_sensitivity.csv>) — Exact annotation/feature; recorded leave-largest-positive-cluster sensitivity. |
| <a id="E16"></a>E16 | [selected_within_family_screens.csv](<Protein_Annotation_Audit_2026-09-22/selected_within_family_screens.csv>) — family:MFS panel, PFAM:PF07690 and M_aliphatic_index; use full original family label. |
| <a id="E17"></a>E17 | [arg_profet_batch_extraction.ipynb](<../arg_profet_batch_extraction.ipynb>) — 1-based cell 5: protein_features, aliphatic_index and segment_features. |
| <a id="E18"></a>E18 | [pfeature_comp.py](<../../../miniforge3/envs/jupyter/lib/python3.12/site-packages/pfeature/python_scripts/pfeature_comp.py>) — Descriptor functions and residue-group/triad definitions. |
| <a id="E19"></a>E19 | [PhysicoChemical.csv](<../../../miniforge3/envs/jupyter/lib/python3.12/site-packages/pfeature/Data/PhysicoChemical.csv>) — Row order from code headers; residue order ACDEFGHIKLMNPQRSTVWY. |
| <a id="E20"></a>E20 | [Pfeature_Manual.pdf](<../Papers/CARD_WORK/Pfeature_Manual.pdf>) — PDF pp. 54–55 and 61, cross-checked against implementation. |
| <a id="E21"></a>E21 | [Moghaddam_Sequence-based analysis and prediction of lantibiotics: A machine learning.pdf](<../Papers/CARD_WORK/Moghaddam_Sequence-based analysis and prediction of lantibiotics: A machine learning.pdf>) — Poorinmohammad et al. 2018; DOI 10.1016/j.compbiolchem.2018.10.004; PDF pp. 2–4 (journal pp. 200–202). Conceptual context only. |
| <a id="E22"></a>E22 | [target_validation_metrics.csv](<../CARD_DATA/card-data/refined_details/Final_Results/ML_ARG_family_drug_class_comparison/target_validation_metrics.csv>) — Family n_positive, ap, f1 and weak_model. |
| <a id="E23"></a>E23 | [shap_safety_summary.csv](<../CARD_DATA/card-data/refined_details/Final_Results/ML_ARG_family_drug_class_comparison/shap_safety_summary.csv>) — Family passes_shap_filter and nonzero_features. |
| <a id="E24"></a>E24 | [ablation_fold_metrics.csv](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/ablation_fold_metrics.csv>) — all_proteins/observed: folds 0 and 1. |
| <a id="E25"></a>E25 | [protein_manifest.csv](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/protein_manifest.csv>) — 6059 unique ARO rows/hashes; JSON family memberships total 6206. |
| <a id="E26"></a>E26 | [descriptor_audit.csv](<../CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/descriptor_audit.csv>) — Source and descriptor-block counts. |
| <a id="E27"></a>E27 | [ML_InterPro_Manuscript_Methods.md](<ML_InterPro_Manuscript_Methods.md>) — Prior project methods; primary result tables take precedence. |
| <a id="E28"></a>E28 | [Protein_Annotation_Biological_Statistical_Audit_2026-09-22.md](<Protein_Annotation_Biological_Statistical_Audit_2026-09-22.md>) — Prior biological/statistical audit and sensitivity discussion. |
