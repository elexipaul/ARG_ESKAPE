# Protein annotations and physicochemical associations

## Analysis population and annotation coverage

The analysis included 6,059 unique CARD ARO proteins with 996 Pfeature and ProFET-style descriptors. Repeated drug-class rows were collapsed after numerical consistency checks. This is a protein-level adaptation of Ghosh et al.; outcomes are CARD family and drug-class memberships, not AST phenotypes.

InterProScan 5.78-109.0 completed scans for all 6,059 proteins. Pfam signatures were found in 6,017 proteins and integrated InterPro annotations in 6,054. Selected applications were Pfam, ProSitePatterns, ProSiteProfiles, Gene3D, SUPERFAMILY.

Exact-sequence UniProtKB mappings were obtained for 4,060 proteins; 272 representatives were reviewed. Unmapped records were retained as missing. Unannotated mapped records were not interpreted as experimentally established absence. Annotation-specific evidence was retained.

## Physicochemical–annotation associations

The global analysis tested 253,361 descriptor–annotation pairs. 22,682 met screening criteria (both BH q values < 0.05, |Cliff's delta| >= 0.33, and at least 5 contributing clusters per state). 22,682 candidates were checked with 2,000 whole-cluster bootstrap replicates; 17,182 had length-adjusted intervals excluding zero.

Mann–Whitney tests supplied descriptive effect screens. Cluster-robust ranked regression and cluster resampling addressed detected sequence dependence. These post-selection intervals are exploratory stability checks, not independent confirmation.

- **IPR:IPR009045 (Zn_M74/Hedgehog-like) — DPC1_WH:** annotated proteins had higher values (n=18 versus 6041; Cliff's delta=0.989; cluster-adjusted q=5.86e-224). Length-adjusted partial Spearman correlation was 0.331 with exploratory 95% cluster-bootstrap interval [0.143, 0.489].
- **IPR:IPR003709 (VanY-like_core_dom) — DPC1_WH:** annotated proteins had higher values (n=10 versus 6049; Cliff's delta=0.984; cluster-adjusted q=7.89e-220). Length-adjusted partial Spearman correlation was 0.246 with exploratory 95% cluster-bootstrap interval [0.111, 0.371].
- **PFAM:PF02557 (VanY) — DPC1_WH:** annotated proteins had higher values (n=10 versus 6049; Cliff's delta=0.984; cluster-adjusted q=7.89e-220). Length-adjusted partial Spearman correlation was 0.246 with exploratory 95% cluster-bootstrap interval [0.115, 0.379].
- **PFAM:PF07091 (FmrO) — CTC_171:** annotated proteins had higher values (n=12 versus 6047; Cliff's delta=0.961; cluster-adjusted q=1.75e-137). Length-adjusted partial Spearman correlation was 0.154 with exploratory 95% cluster-bootstrap interval [0.059, 0.283].
- **IPR:IPR025981 (rRNA_MeTrfase) — CTC_171:** annotated proteins had higher values (n=12 versus 6047; Cliff's delta=0.961; cluster-adjusted q=1.75e-137). Length-adjusted partial Spearman correlation was 0.154 with exploratory 95% cluster-bootstrap interval [0.057, 0.283].

## Matched family and drug-class ablations

All blocks used the same eligible targets, outer test proteins and sequence groups within each cohort. Thresholds and preprocessing were fitted in training folds. UniProt identifiers, names, drug terms, EC/GO labels and resistance descriptions were excluded from predictors.

| Cohort | Target | Run | Targets | Median AP | Median F1 | Median nMCC | Median paired AP change |
|---|---|---|---:|---:|---:|---:|---:|
| all_proteins | drug_class | R1_physicochemical | 24 | 0.185 | 0.126 | 0.570 | +0.000 |
| all_proteins | drug_class | R2_Pfam_InterPro | 24 | 0.267 | 0.240 | 0.624 | +0.072 |
| all_proteins | drug_class | R3_UniProt_generic | 24 | 0.196 | 0.172 | 0.564 | -0.016 |
| all_proteins | drug_class | R4_annotations | 24 | 0.293 | 0.232 | 0.606 | +0.095 |
| all_proteins | drug_class | R5_combined | 24 | 0.289 | 0.203 | 0.606 | +0.026 |
| all_proteins | family | R1_physicochemical | 10 | 0.242 | 0.207 | 0.624 | +0.000 |
| all_proteins | family | R2_Pfam_InterPro | 10 | 0.440 | 0.220 | 0.650 | +0.134 |
| all_proteins | family | R3_UniProt_generic | 10 | 0.150 | 0.138 | 0.598 | -0.021 |
| all_proteins | family | R4_annotations | 10 | 0.501 | 0.529 | 0.775 | +0.196 |
| all_proteins | family | R5_combined | 10 | 0.500 | 0.509 | 0.760 | +0.076 |
| uniprot_exact_mapped | drug_class | R1_physicochemical | 21 | 0.245 | 0.142 | 0.567 | +0.000 |
| uniprot_exact_mapped | drug_class | R2_Pfam_InterPro | 21 | 0.351 | 0.300 | 0.649 | +0.104 |
| uniprot_exact_mapped | drug_class | R3_UniProt_generic | 21 | 0.265 | 0.178 | 0.600 | -0.006 |
| uniprot_exact_mapped | drug_class | R4_annotations | 21 | 0.442 | 0.278 | 0.630 | +0.154 |
| uniprot_exact_mapped | drug_class | R5_combined | 21 | 0.391 | 0.283 | 0.640 | +0.040 |
| uniprot_exact_mapped | family | R1_physicochemical | 9 | 0.218 | 0.174 | 0.587 | +0.000 |
| uniprot_exact_mapped | family | R2_Pfam_InterPro | 9 | 0.523 | 0.186 | 0.638 | +0.112 |
| uniprot_exact_mapped | family | R3_UniProt_generic | 9 | 0.260 | 0.383 | 0.693 | -0.037 |
| uniprot_exact_mapped | family | R4_annotations | 9 | 0.733 | 0.361 | 0.696 | +0.229 |
| uniprot_exact_mapped | family | R5_combined | 9 | 0.553 | 0.519 | 0.768 | +0.165 |
| uniprot_reviewed | drug_class | R1_physicochemical | 16 | 0.246 | 0.219 | 0.567 | +0.000 |
| uniprot_reviewed | drug_class | R2_Pfam_InterPro | 16 | 0.176 | 0.207 | 0.540 | -0.081 |
| uniprot_reviewed | drug_class | R3_UniProt_generic | 16 | 0.266 | 0.336 | 0.616 | +0.044 |
| uniprot_reviewed | drug_class | R4_annotations | 16 | 0.376 | 0.359 | 0.633 | +0.087 |
| uniprot_reviewed | drug_class | R5_combined | 16 | 0.285 | 0.267 | 0.586 | +0.015 |
| uniprot_reviewed | family | R1_physicochemical | 5 | 0.285 | 0.400 | 0.675 | +0.000 |
| uniprot_reviewed | family | R2_Pfam_InterPro | 5 | 0.107 | 0.178 | 0.540 | -0.083 |
| uniprot_reviewed | family | R3_UniProt_generic | 5 | 0.370 | 0.471 | 0.728 | -0.006 |
| uniprot_reviewed | family | R4_annotations | 5 | 0.533 | 0.509 | 0.728 | +0.005 |
| uniprot_reviewed | family | R5_combined | 5 | 0.336 | 0.321 | 0.623 | +0.068 |

## Controls and limitations

Shuffled-label median nMCC was 0.499 (chance reference 0.5). Target-specific results and excluded null targets are listed separately. One shuffle does not provide a permutation p-value.

Pfam and corresponding integrated InterPro terms are not independent evidence. Domain annotations are derived from the same sequence whose descriptors are tested. Associations establish compositional relationships, not catalytic mechanisms or causal resistance. Family, phylogeny and curation depth remain potential confounders.

Stratified panels and links to earlier SHAP importance are post-hoc analyses on the same protein collection. Cross-database agreement is not independent experimental validation. Exact UniProt matching limits applicability to novel sequences.

## Reproducibility

- Cohort hash: 498d6c9b78e06f6b13c6ce3b9bae48310a10f982ca5e615e1a3a089f0ee2bc20
- Association hash: 576b2ca3b588475085489c5f3594657aa312143fe3f04cf624c6666eb8fc0b28
- Ablation hash: 16d86ae9803f670a74010c052998fb5e0982ac9064ddd5619d2d74034eacbb5c
- Tables: /home/user/Pranjal-11-12-2025/ESKAPE_ARG_Family/CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables
- Figures: /home/user/Pranjal-11-12-2025/ESKAPE_ARG_Family/CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/figures
- Releases, retrieval times, mapping failures, versions, exclusions and folds are recorded in the accompanying manifests.

## References

- Ghosh et al. From sequence to signature: Machine learning uncovers multiscale feature landscapes that predict AMR across ESKAPE pathogens. bioRxiv, version 20 October 2025. https://doi.org/10.1101/2025.07.03.663053 (preprint).
- InterProScan: https://www.ebi.ac.uk/interpro/download/InterProScan/
- UniProt API: https://www.uniprot.org/help/id_mapping_prog

*Draft generated from completed analyses; review biological interpretation before publication.*
