# Integrated Profiling of Antimicrobial-Resistance Gene Families Across ESKAPE Pathogens

## Project evaluation, current status, conclusion, and five-month work plan

**Draft date:** 7 September 2026  
**Project stage:** Advanced computational analysis with several completed descriptive components and remaining confirmatory analyses

## Executive summary

Antimicrobial-resistance genes differ not only in the drugs to which they confer resistance, but also in where they occur and how readily they may spread. An ARG carried on a chromosome has a different dissemination potential from one located on a conjugative plasmid. This project therefore investigates the distribution, genomic location, plasmid mobility, phylogenetic structure, selection pressure, and sequence characteristics of ARG families across six ESKAPE pathogen groups: *Acinetobacter baumannii*, *Enterobacter* spp., *Enterococcus faecium*, *Klebsiella pneumoniae*, *Pseudomonas aeruginosa*, and *Staphylococcus aureus*.

The project integrates RGI/CARD resistance annotations with MOB-suite chromosome/plasmid reconstruction and plasmid mobility classification. The current combined dataset contains 9,947 genomes and 381,491 mapped ARG records. Mobility mapping is complete for all six pathogen groups, with every mapped ARG assigned to chromosomal, conjugative, mobilizable, or non-mobilizable status. The project also contains resistance-family distribution analyses, beta-lactamase phylogenetic studies, a reusable CDS selection-pressure pipeline, pairwise PAML estimates, and an interpretable machine-learning analysis of CARD reference proteins.

The strongest unifying research question is:

> Is the genomic mobility profile of an ARG family associated with its distribution across ESKAPE pathogens and with the evolutionary patterns observed within that family?

The descriptive foundation needed to answer this question is largely complete. The next stage is to freeze and reconcile the analysis dataset, conduct the formal mobility–distribution association analysis, strengthen the phylogenetic evidence for horizontal transfer, apply recombination-aware selection analysis to suitable homologous gene groups, and integrate the results into a focused manuscript. The CARD protein-feature classifier is scientifically useful but currently answers a different question and should be developed as a separate or clearly bounded secondary study.

## 1. Background and rationale

ESKAPE pathogens are major causes of difficult-to-treat hospital-associated infections and are important reservoirs of antimicrobial resistance. Standard resistance surveys generally report which ARGs are present and how common they are. Presence alone, however, does not fully describe dissemination risk. An ARG's location on a chromosome or plasmid, and the transfer machinery of the plasmid carrying it, affect the biological opportunity for movement between bacterial lineages.

This project extends conventional ARG profiling by combining three related dimensions:

1. **Distribution:** which ARG families occur in each pathogen and how frequently they occur.
2. **Mobility:** whether ARG records are chromosomal or plasmid-associated and, for plasmids, whether they are conjugative, mobilizable, or non-mobilizable.
3. **Evolution:** whether phylogenetic mixing, sequence divergence, recombination, and selection patterns differ among gene families and mobility groups.

A fourth, complementary dimension uses alignment-free protein descriptors and interpretable machine learning to determine which sequence features distinguish CARD gene-family labels.

## 2. Overall aim

To characterize the distribution, mobility, and evolutionary patterns of antimicrobial-resistance gene families across six ESKAPE pathogen groups and determine whether ARG mobility is associated with broader cross-pathogen distribution.

## 3. Specific objectives

1. Construct a harmonized catalogue of ARG families detected across the six pathogen groups.
2. Quantify ARG-family presence and abundance within each pathogen.
3. Assign mapped ARG records to chromosome or plasmid locations.
4. Classify plasmid-associated ARG records as conjugative, mobilizable, or non-mobilizable.
5. Calculate family-specific mobility profiles within and across pathogens.
6. Test whether ARG families with greater plasmid mobility occur across more ESKAPE pathogen groups.
7. Evaluate whether gene phylogenies show pathogen-specific clustering or cross-pathogen mixing consistent with horizontal transfer.
8. Assess selection pressure in suitable homologous gene groups using codon-aware and recombination-aware methods.
9. Evaluate whether protein sequence descriptors can classify CARD gene families and identify the features contributing to classification.

## 4. Research questions and working hypotheses

### 4.1 Distribution and mobility

**Research question:** Do ARG families dominated by conjugative or mobilizable plasmid carriage occur across more ESKAPE pathogen groups than predominantly chromosomal families?

**Working hypothesis:** Increasing mobility potential, from chromosome through non-mobilizable and mobilizable plasmids to conjugative plasmids, is associated with broader cross-pathogen distribution.

This is an associational hypothesis. The available cross-sectional genomic data do not directly observe transfer events and therefore cannot by themselves establish that plasmid mobility caused a family's distribution.

### 4.2 Phylogenetic structure

**Research question:** Do mobile ARG families show weaker clustering by host pathogen than predominantly chromosomal families?

**Working hypothesis:** ARG families with greater mobility will display more cross-pathogen phylogenetic mixing and greater discordance between gene and host relationships.

### 4.3 Selection pressure

**Research question:** Within sufficiently sampled homologous gene groups, do mobile and chromosomal lineages show different selection regimes?

**Working hypothesis:** Selection patterns differ between mobility strata, although acquisition recency, recombination, lineage structure, and ecological exposure may explain part of any observed association.

### 4.4 Protein-feature classification

**Research question:** Can alignment-free protein descriptors distinguish curated CARD gene-family labels?

**Working hypothesis:** Amino-acid composition, physicochemical summaries, sequence-order descriptors, and related features contain sufficient family-specific information for accurate classification of curated CARD reference proteins.

## 5. Data and analytical framework

### 5.1 Study material

The working dataset covers 9,947 genomes distributed across the six ESKAPE pathogen groups. Resistance annotations were produced using RGI with CARD terminology. MOB-suite outputs were used to reconstruct chromosome/plasmid assignments and classify plasmid mobility.

The unit of analysis differs by question and must remain explicit:

- an individual RGI hit for ARG-level location and mobility counts;
- a genome for prevalence and epidemiological comparisons;
- an ARG family within a pathogen for family-specific mobility profiles;
- a unique CDS or haplotype for phylogenetic and selection analyses;
- a curated CARD reference protein for sequence-feature classification.

### 5.2 ARG-family distribution

RGI results were parsed to extract AMR gene names, CARD gene-family annotations, drug classes, sample identifiers, and contig identifiers. Semicolon-separated annotations were handled to construct family-level tables, presence/absence matrices, abundance matrices, percentage matrices, and heatmaps.

### 5.3 Chromosome/plasmid and mobility assignment

RGI contig identifiers were matched to MOB-suite contig reports. Chromosomal records were labelled `n/a (chromosomal)`. Plasmid records were linked through sample and plasmid-cluster identifiers to MOB-suite mobility predictions and classified as conjugative, mobilizable, or non-mobilizable.

### 5.4 Phylogenetic analysis

Protein and nucleotide sequences were relabelled with unique, whitespace-free identifiers, deduplicated where appropriate, aligned, and used to construct gene trees. A beta-lactamase pilot examined nine families shared by the four Gram-negative pathogen groups. OXA beta-lactamases were subsequently examined at the CDS level because of their unusually complex family structure.

### 5.5 Selection-pressure analysis

The project contains a reusable CDS pipeline that performs strict CDS validation, translation, protein alignment, codon alignment, phylogenetic inference, recombination screening, HyPhy analyses, and PAML estimation. A family-batch driver has also been developed. An existing pairwise PAML table contains 14,459 reference-versus-query estimates across 73 input FASTA sets. These pairwise estimates are useful exploratory summaries but are distinct from the mobility-stratified branch/site tests required for the central selection hypothesis.

### 5.6 Protein-feature and machine-learning analysis

Pfeature and ProFET descriptors were combined to represent CARD reference proteins numerically. After family-size filtering and feature cleaning, the working classification dataset contained 5,479 sequences from 84 families and 1,035 retained features. One-versus-rest LightGBM classifiers were evaluated with grouped cross-validation, and SHAP and permutation importance were used to assess influential features. PCA notebooks separately explore resistance-mechanism structure and family-centroid structure.

## 6. Work completed

### 6.1 ARG catalogue and distribution analysis

The project contains family presence/absence, count, percentage, drug-class mapping, and heatmap outputs for all six pathogen groups. The working project summary reports seven families shared across all six pathogen groups and 30 families shared across the four Gram-negative groups, including nine beta-lactamase families.

The current mobility summary and plasmid-percentage tables contain 145 distinct non-missing family labels, whereas an earlier project summary refers to 141 families. This appears to reflect different analysis versions or annotation handling and should be reconciled during the planned data freeze.

### 6.2 Complete six-pathogen mobility profile

The chromosome/plasmid and plasmid-mobility mapping is complete for all six pathogen groups. The combined table contains 381,491 ARG records, with no missing mobility labels and no invalid molecule-type/mobility combinations.

| Pathogen group | Total ARG records | Chromosomal | Conjugative | Mobilizable | Non-mobilizable |
|---|---:|---:|---:|---:|---:|
| *Acinetobacter baumannii* | 31,488 | 30,066 | 710 | 272 | 440 |
| *Enterobacter* spp. | 25,667 | 20,722 | 3,900 | 437 | 608 |
| *Enterococcus faecium* | 12,419 | 6,599 | 1,303 | 1,910 | 2,607 |
| *Klebsiella pneumoniae* | 179,223 | 145,854 | 25,400 | 4,446 | 3,523 |
| *Pseudomonas aeruginosa* | 84,443 | 84,245 | 42 | 60 | 96 |
| *Staphylococcus aureus* | 48,251 | 45,038 | 350 | 2,650 | 213 |
| **Total** | **381,491** | **332,524** | **31,705** | **9,775** | **7,487** |

At the contig level, the six classification files contain 33,013 contigs: 13,109 chromosomal contigs and 19,904 plasmid contigs. The plasmid contigs comprise 7,724 conjugative, 6,528 mobilizable, and 5,652 non-mobilizable contigs.

These totals establish the descriptive mobility foundation. They should not be interpreted as direct comparisons of pathogen-level biological risk until differences in genome sample size, ARG burden, assembly quality, and repeated observations are taken into account.

### 6.3 Data-quality corrections

Several consequential problems were identified during analysis:

- blank CARD family annotations were converted into a `nan` label and could later be silently treated as missing by pandas;
- this affected representation of the MexAB-OprM system and related regulators in *P. aeruginosa*;
- early mobility joins did not correctly attach plasmid mobility for all rows and were replaced with validated contig-level joins;
- inconsistent organism-folder capitalization affected external sequence-processing steps;
- non-unique and whitespace-containing FASTA headers caused downstream alignment/tree problems;
- broad CARD mechanism labels were initially at risk of being treated as homologous gene families.

Identifying these issues is a major strength of the project. The final manuscript should specify which corrections are included in the frozen analysis and provide reproducible checks preventing their recurrence.

### 6.4 Phylogenetic findings

The nine-family beta-lactamase pilot contains 577 sequences from the four Gram-negative groups. The working analysis reports that Ambler class and CARD family explain the tree structure considerably better than host organism, indicating substantial cross-pathogen mixing within several enzyme families. This pattern is consistent with horizontal dissemination, although a gene tree alone does not directly demonstrate a transfer event.

The OXA CDS analysis contains 321 sequences and reveals a more complex pattern. Large pathogen-associated lineages occur in *A. baumannii* and *P. aeruginosa*, while *K. pneumoniae* and *Enterobacter* sequences are more dispersed. OXA therefore provides a useful counterexample to a simple universal-HGT narrative: its distribution appears to combine intrinsic lineage diversification with cross-pathogen movement.

The full beta-lactamase directory contains pooled CDS input, checked and aligned sequences, and an inferred tree, showing that the workflow has progressed beyond the initial nine-family pilot.

### 6.5 Selection-analysis infrastructure and preliminary output

The robust CDS selection pipeline includes safeguards that are appropriate for this project: coding-sequence validation, exact-haplotype collapsing, protein-guided codon alignment, codon-column quality control, IQ-TREE inference, GARD recombination screening, several HyPhy methods, PAML runs from multiple starting omega values, multiple-testing correction, and resumable content-aware stages.

The pairwise PAML output contains:

- 14,459 pairwise estimates across 73 input FASTA sets;
- 14,180 estimates with an `OK` estimate status;
- 71 comparisons with both dN and dS equal to zero;
- 208 comparisons with dS equal to zero;
- all 14,459 rows marked complete at the PAML execution level.

This is meaningful completed work. It does not yet test whether selection differs between mobility groups because pairwise reference comparisons do not substitute for a pre-specified phylogenetic branch/site analysis.

### 6.6 Protein-feature classification

The one-versus-rest classifier analysis is implemented for 84 CARD families, with saved cross-validated performance, confusion matrices, SHAP rankings, and permutation-importance results. Several families achieve perfect or near-perfect discrimination in the current evaluation. These results show strong sequence-composition differences among curated family labels.

The scientific claim should remain bounded: the current model classifies curated CARD reference proteins into existing family labels. External validation and sequence-identity-based grouping are required before claiming reliable classification of genuinely novel or evolutionarily distant ARG sequences.

## 7. Overall scientific evaluation

### 7.1 Strengths

- The study combines ARG identity, pathogen distribution, genomic location, plasmid transfer potential, phylogeny, and sequence characteristics.
- All six ESKAPE pathogen groups are included in one analytical framework.
- The mobility layer is complete and internally consistent at the record level.
- The analysis documents and corrects real data-processing errors.
- Preliminary phylogenetic results contain both broadly shared and pathogen-associated patterns, making the central hypothesis genuinely testable.
- The selection pipeline is considerably more rigorous than a simple pairwise dN/dS calculation.
- Saved outputs and notebooks provide a strong base for reproducibility and manuscript preparation.

### 7.2 Major limitations and risks

#### Annotation granularity

CARD's `AMR Gene Family` field can represent a resistance mechanism or functional grouping rather than a set of true homologues. Phylogenetic and selection analyses must operate on demonstrably homologous gene groups, not automatically on every CARD family label.

#### Unequal sampling and non-independence

The pathogen groups differ greatly in genome counts and ARG-record counts. Individual ARG hits from the same genome, plasmid, outbreak lineage, or near-identical assembly are not statistically independent. Formal models must reflect the genome and lineage hierarchy.

#### Assembly-dependent mobility classification

Plasmid reconstruction and transfer classification depend on assembly quality and MOB-suite reference coverage. Sensitivity analyses should test whether findings persist after restricting to higher-quality or less fragmented genomes.

#### Intrinsic versus acquired resistance

Highly prevalent intrinsic chromosomal systems and acquired plasmid-borne ARGs represent different biological processes. Combining them without stratification can obscure the relationship between mobility and dissemination.

#### Phylogenetic interpretation

Cross-pathogen mixing is consistent with HGT but is not proof by itself. Stronger inference requires gene-tree/host-tree comparison, genomic-location evidence, and attention to sampling balance and recombination.

#### Selection confounding

Recombination, recent acquisition, transient polymorphism, low sequence diversity, and incorrect orthology can inflate or destabilize dN/dS estimates. Mobility-associated selection should be tested only in gene groups meeting pre-specified quality and sample-size criteria.

#### Machine-learning generalization

Feature-space grouping is not equivalent to clustering by protein sequence identity. Very high cross-validation performance may partly reflect close sequence relationships between folds. A CD-HIT or MMseqs2 cluster-aware evaluation and an external holdout are needed.

## 8. Interpretation of the project at its current stage

The project has moved beyond pipeline construction and basic descriptive analysis. The six-pathogen mobility profile is complete, a large integrated ARG table exists, family-level matrices have been generated, phylogenetic case studies are available, and selection and machine-learning pipelines have produced outputs.

The project has not yet established the central inferential conclusion. The available results demonstrate where resistance genes occur and provide patterns consistent with differing dissemination histories. The next analyses must determine whether family mobility is statistically associated with cross-pathogen breadth after accounting for abundance, sampling, and pathogen structure. Selection results should then be used as focused mechanistic evidence in suitable gene groups rather than as an indiscriminate genome-wide claim.

The protein-feature classifier should be treated as a parallel study unless it is explicitly connected to the biological question—for example, by using validated models to classify unannotated homologues or testing whether mobility groups possess distinct sequence-feature signatures.

## 9. Five-month work plan

### Month 1: Data freeze, reconciliation, and reproducibility

**Objectives**

- Select the canonical versions of every organism-specific and combined table.
- Reconcile the 141-versus-145 family-count discrepancy.
- Identify the two additional entries present in the mobility matrix but absent from the 145-family summary tables.
- Confirm that the MexAB-OprM/blank-family correction is included in every final distribution and mobility output.
- Standardize organism names, family labels, missing-value handling, and denominators.
- Produce a data dictionary defining every table, column, unit of observation, and derived quantity.
- Convert the key notebook workflow into a reproducible ordered script or workflow with explicit input and output paths.

**Deliverables**

- Frozen analysis dataset with a version identifier and checksums.
- Data dictionary and provenance table.
- Automated QC report covering row counts, missingness, duplicate keys, category validity, and cross-table reconciliation.
- Final 145-family—or corrected alternative—master mapping table.

### Month 2: Formal mobility–distribution analysis

**Objectives**

- Define cross-pathogen breadth at the genome/prevalence level rather than from raw ARG-hit counts alone.
- Define family mobility using the complete proportion vector instead of only a dominant-category label where possible.
- Examine conjugative proportion, any-plasmid proportion, and ordered mobility score as alternative exposures.
- Account for family abundance, pathogen sample size, intrinsic/acquired status, and repeated lineage observations.
- Use a regression, hierarchical, or permutation framework appropriate to the final unit of analysis.
- Report effect sizes and confidence intervals in addition to p-values.
- Correct for multiple testing where several mobility definitions or subgroup analyses are evaluated.

**Deliverables**

- Locked statistical analysis plan.
- Main mobility-versus-distribution result table.
- Effect-size plot with uncertainty.
- Sensitivity analyses using alternative mobility and breadth definitions.
- Clear decision on whether the primary mobility hypothesis is supported, unsupported, or inconclusive.

### Month 3: Phylogenetic evidence for dissemination

**Objectives**

- Define homologous gene groups within the 30 Gram-negative-shared families.
- Apply sequence-identity clustering and balanced subsampling where highly unequal family/pathogen counts would dominate a tree.
- Construct or obtain an appropriate host/reference phylogeny.
- Compare gene-tree and host-tree structure using pre-specified metrics.
- Integrate mobility labels onto selected gene trees.
- Retain OXA as a contrasting case demonstrating the coexistence of vertical lineage structure and horizontal dissemination.

**Deliverables**

- Quality-controlled phylogenetic set for selected shared families.
- Gene-tree/host-tree discordance results.
- Annotated trees for two to four representative families.
- A figure relating phylogenetic mixing to family mobility.

### Month 4: Recombination-aware selection analysis

**Objectives**

- Select a small number of well-sampled, genuinely homologous gene groups with adequate representation across mobility strata.
- Apply strict CDS and codon-alignment QC.
- Screen for recombination before interpreting selection tests.
- Define foreground branches from the phylogeny and mobility data without choosing them based on the observed dN/dS result.
- Run appropriate HyPhy and/or PAML branch/site tests.
- Apply multiple-testing correction and report power limitations and failed/inconclusive analyses.
- Compare phylogenetic estimates with the existing pairwise PAML summaries without treating the pairwise estimates as equivalent evidence.

**Deliverables**

- Per-family QC and eligibility table.
- Recombination-screen results.
- Branch/site selection results with corrected significance values and effect estimates.
- Two or three defensible case studies, including null results where applicable.

### Month 5: Integration, manuscript, and presentation

**Objectives**

- Integrate family distribution, mobility, phylogenetic mixing, and selection results.
- Decide which conclusions are supported across families and which are family-specific.
- Prepare a focused biological manuscript centered on ARG mobility and dissemination.
- Move the CARD feature-classification analysis into a separate methods manuscript or a clearly labelled supplementary section.
- For the classifier study, add sequence-identity-clustered cross-validation and, if available, an external holdout set.
- Produce final figures, supplementary tables, a reproducible methods description, and a scientific presentation.

**Deliverables**

- Complete manuscript draft.
- Main figures and supplementary material.
- Reproducible analysis archive with software versions and commands.
- Presentation deck for laboratory, departmental, or conference review.
- Separate outline for the protein-feature classification study.

## 10. Recommended final manuscript structure

1. **Introduction:** ESKAPE resistance burden, importance of genomic context, and the mobility knowledge gap.
2. **Methods:** genome collection, RGI/CARD annotation, MOB-suite mapping, family harmonization, statistical analysis, phylogenetics, recombination, and selection testing.
3. **Results 1:** ARG-family distribution across the six pathogen groups.
4. **Results 2:** complete chromosome/plasmid and mobility profiles.
5. **Results 3:** association between mobility and cross-pathogen breadth.
6. **Results 4:** phylogenetic case studies supporting or qualifying the distribution result.
7. **Results 5:** focused recombination-aware selection analyses, if adequately powered.
8. **Discussion:** biological interpretation, intrinsic versus acquired resistance, exceptions such as OXA, limitations, and surveillance implications.
9. **Conclusion:** a bounded statement of what mobility predicts in this dataset, without claiming direct observation of transfer or causation.

## 11. Conclusion

This project provides a comprehensive framework for studying antimicrobial-resistance gene families across six ESKAPE pathogen groups. Its strongest completed contribution is the integration of 381,491 ARG records with chromosome/plasmid location and four-state mobility classification across 9,947 genomes. The distribution analyses and beta-lactamase phylogenies further show that resistance families differ markedly in both host range and evolutionary structure. Existing selection and machine-learning work add valuable analytical depth.

The project is now at the transition from descriptive discovery to confirmatory inference. The immediate priority is not to generate more disconnected analyses, but to freeze the dataset and test the central mobility–distribution hypothesis with a model that respects sampling, abundance, and biological hierarchy. Phylogenetic and selection analyses should then explain and qualify that result through carefully selected homologous gene groups.

If the formal analysis demonstrates that increasing plasmid mobility is associated with broader cross-pathogen distribution, the project will provide a strong ESKAPE-wide argument for incorporating plasmid transfer potential into ARG surveillance. If the association is weak or restricted to particular families, that result will still be informative: it would show that mobility risk is family- and lineage-dependent and cannot be inferred from plasmid location alone. Either outcome can produce a defensible scientific conclusion because the hypothesis is explicit, falsifiable, and supported by a complete descriptive dataset.

## 12. Internal evidence used for this draft

- `README.md`
- `Project_Summary_Hypothesis_Results_and_Next_Steps.md`
- `Results/Mobsuite/ARG_Chromosome_Plasmid_Mapping.csv`
- `Results/Mobsuite/ARG_Family_Chromosome_Plasmid_Summary.csv`
- `Results/Mobsuite/ESKAPE_ARG_Mobility_Matrix.csv`
- `Results/Mobsuite/ESKAPE_ARG_Plasmid_Pct_Matrix.csv`
- `Results/Mobsuite/Plasmid_classification/`
- `Results/Phylogenetics/`
- `Results/selection_analysis/`
- `Results/DRUG_class_family_mapping/Gene_Family_Alignment_and_Drug_Class_Discussion.md`
- `CARD_DATA/card-data/refined_details/Final_Results/ML_on_features_ARG/`
- `manuscript/methods.md`

## 13. Writing support

The structure and scientific-reporting checks used in preparing this draft were supported by:

Kassis, T., Agarwal, V., He, Y., Patel, D., & Brueckner, A. M. (2026). *Scientific Agent Skills: A Library of Procedural Knowledge for Research Agents*. arXiv:2609.00065. https://doi.org/10.48550/arXiv.2609.00065
