# Variant Interpretation in Bacterial Genomics: Literature Screen and Gap Analysis

**Date:** 17 September 2026
**Method:** Europe PMC search and title/abstract screening, PRISMA-style funnel
**Status:** Scoping review. Title/abstract screening only — no full-text pass was performed.
**Relationship to other documents:** Standalone. Complements `Cryptic_AMR_Classification_Roadmap.md` but does not modify it. Relevant to the SNP-blindness limitation recorded in that document's §13.9.

---

## 1. Motivation and scope

This screen was prompted by a simple question — has anyone done bacterial genomics on variant interpretation? — and by the observation that the project's own paper collection contains essentially nothing on the topic. The nearest items in `Papers/` and `Downloads/` were a *P. aeruginosa* comparative-genomics paper contrasting cystic fibrosis sputum with bloodstream isolates, a Neisseria AMR-mutations paper, and a long-read structural-variant caller. None is variant interpretation in the sense meant here.

"Variant interpretation" is used here in the sense borrowed from clinical genetics: **determining whether a specific point mutation in a bacterial genome confers antimicrobial resistance**, and with what confidence.

### Inclusion criteria

- Resistance-mutation catalogues and their grading or confidence frameworks
- Point-mutation detection and variant-to-phenotype prediction tools
- Genotype–phenotype association and concordance studies for resistance mutations
- Machine-learning or protein-language-model approaches to predicting bacterial variant effect
- Bacterial GWAS methodology (genetic variation to phenotype)
- Deep mutational scanning of bacterial resistance genes

### Exclusion criteria

- Human or clinical-genetics variant interpretation
- Viral variants
- Structural variant calling
- Gene presence/absence AMR prediction with no mutation component
- Local isolate surveillance and characterisation that reports mutations without interpretive methodology
- Diagnostic assay performance evaluations (as distinct from evaluations of interpretation rules)

---

## 2. Search 1 — broad

Query combined resistance terms, variation terms, and interpretation terms across bacteria including *Mycobacterium*. Open-access filter applied.

| Funnel stage | Count |
|---|---|
| Raw hits | **3,860** |
| Retrieved for screening | 150 |
| Included on title/abstract | **30** |

The 120 exclusions were overwhelmingly local genomic surveillance and isolate characterisation, diagnostic platform evaluations, and resistance-mechanism reviews.

---

## 3. Search 2 — organism-split comparison

To test an initial impression that this field is dominated by tuberculosis, an identical concept clause was run against two organism clauses.

| Arm | Organism clause | Raw hits |
|---|---|---|
| A | tuberculosis OR Mycobacterium | **1,522** |
| B | six ESKAPE organisms, NOT tuberculosis/Mycobacterium | **1,528** |

Arm B: 150 retrieved → **21 included**.

---

## 4. Correction to an initial impression

After the first search it appeared that bacterial variant interpretation was "overwhelmingly a tuberculosis field." **That impression was wrong on volume and is corrected here.** Publication counts under identical concept terms are essentially equal: 1,522 versus 1,528.

The impression arose because the *included* papers from search 1 skewed heavily toward tuberculosis, which is a different observation from total publication volume. What actually differs between the two literatures is **not how much is published, but how mature the interpretation infrastructure is.**

This correction is recorded rather than quietly replaced with the right answer, because the distinction — equal volume, unequal infrastructure — is itself the substantive finding and is more defensible than the original claim.

---

## 5. The actual gap: infrastructure maturity, not publication volume

### Tuberculosis has a complete interpretation stack

- **Graded catalogues with formal confidence tiers.** The WHO mutation catalogue (editions 1 and 2, 2021 and 2023) assigns variants to named categories including *group 3 — uncertain significance* and *group 4 — not associated with resistance, interim*. Uncertainty is a first-class, labelled state.
- **Reproducible catalogue-construction methodology.** `catomatic` automates catalogue building using a binomial test (`10.1101/2025.10.02.679941`), explicitly motivated by the WHO catalogue's underlying data not being fully released. A separate regression-based approach trained on over 52,000 MTBC isolates recovers 450/457 (98%) of WHO-catalogue variants and improves sensitive grading (`10.1038/s41467-025-57174-1`).
- **A benchmarked tool ecosystem with its own meta-analysis.** A systematic review and bivariate meta-analysis covers six maintained tools — TBProfiler, Mykrobe, PhyResSE, MTBseq, GenTB and SAM-TB — across 39 studies (`10.1128/jcm.00321-26`).
- **A literature on disputed and borderline variants**, treating diagnostic ambiguity as a research object in its own right (`10.3390/pathogens15010016`, `10.1128/spectrum.00327-26`).
- **Shared reference benchmarks**, notably CRyPTIC, used for independent validation across studies.

### ESKAPE has almost none of it

No graded, confidence-tiered resistance-mutation catalogue exists for *Enterococcus faecium*, *Staphylococcus aureus*, *Klebsiella pneumoniae*, *Acinetobacter baumannii*, *Pseudomonas aeruginosa* or *Enterobacter* species.

What exists instead falls into three groups: genotype–phenotype concordance and discordance observations, general-purpose GWAS and resistome pipelines, and machine-learning phenotype prediction. None of these constitutes an interpretation framework for individual variants.

### The two infrastructure papers, both from the last twelve months

**AmrProfiler** (Nucleic Acids Research, 2025 — `10.1093/nar/gkaf400`). Three modules covering acquired AMR genes, resistance-associated mutations, and rRNA gene mutations, across nearly 18,000 bacterial species. Curated database of 7,600 unique AMR gene entries and **over 4,300 resistance-related mutations**. Reported as the first tool to systematically handle rRNA gene mutations including copy-number analysis. This is a curated cross-species resource — but it is a detection tool, not a graded catalogue; there are no confidence tiers.

**PaVarDB** (*Database*, 2026 — `10.1093/database/baag014`). A missense-variant database and web platform for *P. aeruginosa*, generated by an end-to-end Nextflow/Docker pipeline named BVbase. It states the gap directly in its own abstract:

> "Genomic variant data are useful in detecting and treating antibiotic-resistant bacteria. However, **there are no bacterial genomic variant databases that catalogue the variations in the different genes across strains.** In this work a Nextflow- and Docker-based end-to-end pipeline, BVbase, that can automate the creation of databases from raw high-throughput sequences has been created to fill this lacuna with *Pseudomonas aeruginosa* as a case study."

A database journal publishing that statement in 2026 is substantially stronger evidence of the gap than any assertion made here. That both attempts in this direction appeared within roughly a year suggests an actively opening area rather than a settled absence.

---

## 6. Included papers — Search 1 (broad), n = 30

### Catalogues, grading and confidence frameworks

| Paper | Year | DOI |
|---|---|---|
| Rapidly and reproducibly building a comprehensive catalogue of resistance-associated variants for *M. tuberculosis* (catomatic) | 2025 | `10.1101/2025.10.02.679941` |
| Multivariable regression models improve accuracy and sensitive grading of antibiotic resistance mutations in *M. tuberculosis* | 2025 | `10.1038/s41467-025-57174-1` |
| Performance of the WHO mutation catalog in diagnosis of drug resistance in rifampicin-resistant strains | 2026 | `10.1128/spectrum.02481-25` |
| No phenotypic resistance observed for most group-3 and -4 variants (bedaquiline, clofazimine, delamanid, pretomanid) | 2026 | `10.1128/spectrum.02005-26` |
| Beyond hotspot mutations: high-frequency, low-frequency and disputed *rpoB* variants | 2025 | `10.3390/pathogens15010016` |

### Prediction tools and platforms

| Paper | Year | DOI |
|---|---|---|
| gnomonicus performance on 2,663 *M. tuberculosis* samples (WHO catalogue v2 implementation) | 2025 | `10.1099/mgen.0.001592` |
| Open-access genomic drug resistance prediction tools for *M. tuberculosis*: systematic review and meta-analysis | 2026 | `10.1128/jcm.00321-26` |
| Rapid, accurate and reproducible *de novo* prediction of resistance to antituberculars | 2025 | `10.1128/msphere.00571-25` |
| Pathogenwatch: public health platform for rapid interpretation of pathogen genomics | 2026 | `10.64898/2026.03.18.26348693` |
| Mycobacteria Amplicon Sequencing Tool: automated resistance prediction and lineage classification | 2026 | `10.1128/mra.00032-26` |
| GeneScanner: profiling genetic variation across bacterial populations | 2026 | `10.1099/mgen.0.001714` |

### Machine learning for variant discovery and prediction

| Paper | Year | DOI |
|---|---|---|
| **Gene copy-number features generalize better than SNPs for AMR prediction in *S. aureus*** | 2025 | `10.1038/s44259-025-00172-6` |
| ML-based identification of new resistance-associated mutations from WGS of *M. tuberculosis* | 2025 | `10.1093/bioadv/vbaf050` |
| Explainable AI framework reveals mutations associated with drug resistance in *M. tuberculosis* | 2025 | `10.1016/j.bsheal.2025.11.001` |
| CNNs quantify antibiotic resistance in *M. tuberculosis* with diagnostic-grade accuracy | 2026 | `10.1038/s41467-026-72225-x` |
| AMR-GNN: multi-representation graph neural network for genomic AMR prediction | 2026 | `10.1038/s41467-026-69934-8` |
| ML prediction and identification of AMR-related SNPs in *M. tuberculosis* | 2025 | `10.1186/s12863-025-01338-x` |
| WGS reveals lineage-associated drug resistance and enables ML-based prediction | 2026 | `10.3389/fmicb.2026.1828991` |
| Decoding positive selection in *M. tuberculosis* with phylogeny-guided graph attention models | 2026 | `10.21203/rs.3.rs-8920054/v1` |

### Genotype–phenotype concordance and discordance

| Paper | Year | DOI |
|---|---|---|
| Uncovering species- and drug-class-specific AMR mechanisms using discordance analysis and ML | 2026 | `10.1093/bib/bbag451` |
| **Antibiotic-specific genotype–phenotype concordance and cross-database interoperability (NCBI Pathogen Detection / BV-BRC)** | 2026 | `10.3390/ijms27167157` |
| Genotypic diversity and genotype–phenotype concordance in methicillin-resistant staphylococci | 2026 | `10.1016/j.vas.2026.100722` |
| CABBAGE: comprehensive AMR genotype–phenotype database | 2026 | `10.1093/nar/gkag780` |

### GWAS methodology

| Paper | Year | DOI |
|---|---|---|
| Bacterial genome-wide association studies: exploring genetic variation underlying bacterial phenotypes | 2025 | `10.1128/aem.02512-24` |
| BaGPipe: automated, reproducible, flexible pipeline for bacterial GWAS | 2026 | `10.1186/s12866-026-04909-9` |
| High-precision binary trait association on phylogenetic trees | 2026 | `10.1099/mgen.0.001791` |

### Variant effect and evolution

| Paper | Year | DOI |
|---|---|---|
| Genomic insights into low-level rifampicin resistance mediated by borderline *rpoB* mutations | 2026 | `10.1128/spectrum.00327-26` |
| Frameshift mutation in methyltransferase *rlmN* associated with increased linezolid resistance | 2026 | `10.34133/csbj.0090` |
| Polymorphisms in a bacterial signalling pathway alter evolutionary routes of antibiotic resistance in *E. coli* | 2026 | `10.1371/journal.pbio.3003977` |

### Reviews

| Paper | Year | DOI |
|---|---|---|
| Computational paradigms for AMR prediction: multi-omics, structural modeling, foundation AI (PRISMA, 156 records → 93 studies) | 2026 | `10.1093/bib/bbag219` |

---

## 7. Included papers — Search 2 (ESKAPE, TB excluded), n = 21

### Variant-level infrastructure

| Paper | Year | DOI |
|---|---|---|
| **PaVarDB: missense variant database and web platform for *P. aeruginosa* (BVbase pipeline)** | 2026 | `10.1093/database/baag014` |
| **AmrProfiler: identifying AMR genes and mutations across species** | 2025 | `10.1093/nar/gkaf400` |

### Genotype–phenotype concordance and discordance

| Paper | Year | DOI |
|---|---|---|
| Phenotype–genotype discordance in Gram-negative uropathogens from catheter-associated UTI | 2025 | `10.1093/jac/dkaf352` |
| Antibiotic-specific genotype–phenotype concordance and cross-database interoperability | 2026 | `10.3390/ijms27167157` |
| Concordance between phenotypic screening tests and β-lactamase gene profile in Gram-negative bacteria | 2025 | `10.3390/antibiotics14121275` |
| Genotypic diversity and genotype–phenotype concordance in methicillin-resistant staphylococci | 2026 | `10.1016/j.vas.2026.100722` |

### Pipelines and platforms

| Paper | Year | DOI |
|---|---|---|
| PaREx: automated analysis of *P. aeruginosa* resistomes from WGS | 2026 | `10.1128/aac.01326-25` |
| BaGPipe: bacterial GWAS pipeline | 2026 | `10.1186/s12866-026-04909-9` |
| GeneScanner: profiling genetic variation across bacterial populations | 2026 | `10.1099/mgen.0.001714` |
| ReGAIN: probabilistic co-occurrence between resistance genes | 2026 | `10.1093/bioinformatics/btag505` |
| Global epidemiological insights on *E. faecium* AMR and a genomic analysis framework | 2025 | `10.1099/mgen.0.001573` |

### Machine learning prediction

| Paper | Year | DOI |
|---|---|---|
| Integrating WGS and ML for predicting AMR in critical pathogens: systematic review | 2024 | `10.7717/peerj.18213` |
| KEGG orthology-based ML reveals functional determinants of AMR in *A. baumannii* | 2026 | `10.1128/spectrum.02592-25` |
| Rapid prediction of antibiotic resistance in *Enterobacter cloacae* complex | 2025 | `10.1128/msystems.00584-25` |
| Novel bioinformatics pipeline and ML approach for AMR phenotypic prediction | 2026 | `10.1177/11779322261453756` |
| Balancing complexity and clarity — towards clinician-ready antibiotic resistance prediction models | 2025 | `10.1093/bioinformatics/btaf556` |
| Comprehensive in silico genomics of aminoglycoside resistance emergence in *S. aureus* | 2026 | `10.1186/s12866-026-05542-2` |

### Variant effect and within-host evolution

| Paper | Year | DOI |
|---|---|---|
| Suboptimal ceftazidime-avibactam exposure drives sequential *bla*<sub>KPC</sub> mutations | 2026 | `10.1128/spectrum.03308-25` |
| OmpK36 GD insertion induces RpoS-dependent *bla*<sub>KPC</sub> overexpression | 2026 | `10.1128/aac.01824-25` |
| Plasticity in a bacterial global regulatory switch driving resistance and virulence shift | 2026 | `10.1093/femsmc/xtag044` |
| Genomic variants distinguish *P. aeruginosa* from CF sputum and bloodstream infection | 2026 | `10.1186/s12879-025-12501-1` |

*Note: four records appear in both searches (GeneScanner, BaGPipe, the staphylococci concordance study, and the cross-database interoperability study). The last of these is already held locally in `Pseudomonas_project_18_06/`.*

---

## 8. Relevance to this project

Three existing threads connect directly to this gap.

**The CARD variant model is on disk and unused.** `CARD_DATA/card-data/` contains `protein_fasta_protein_variant_model.fasta` (154 KB), `nucleotide_fasta_protein_variant_model.fasta` (429 KB), `nucleotide_fasta_rRNA_gene_variant_model.fasta` (186 KB), the knockout and overexpression models, and `snps.txt` (648 KB). The classification pipeline in `ARG_family_drug_class_SHAP_comparison.ipynb` uses `protein_fasta_protein_homolog_model.fasta` exclusively. The variant-model data covers precisely the resistance mechanisms the homolog model cannot represent.

**The SNP-blindness gap is shared and documented.** Ghosh et al. (see `Cryptic_AMR_Classification_Roadmap.md` §13.9) state plainly that presence/absence features cannot capture SNP-driven resistance — *gyrA*/*gyrB* for fluoroquinolones, *rpoB* for rifampin, *dfrB* for trimethoprim — and their rifampin *S. aureus* model sat at random baseline as a result. This project's pipeline has the same limitation for the same reason.

**A phenotype anchor already exists.** The 336 AST-phenotyped isolates in `ALL_ESKAPE_Clinical_metadata.xlsx` (roadmap §3.3) are drawn from NCBI Pathogen Detection — the same source used by `10.3390/ijms27167157` for its genotype–phenotype concordance analysis, making that paper a directly transferable methods template.

### Two papers to read first

**`10.1038/s44259-025-00172-6`** — gene copy-number versus SNP features for AMR prediction in *S. aureus*. XGBoost on pan-genome gene copy number against SNP-based models, six antibiotics, 4,255 isolates: macro-averaged F1 0.925–0.988 versus 0.838–0.935, framed explicitly around generalisation to novel lineages. This is the closest published empirical test of the feature-design question underlying this project's pipeline, and it favours the approach already being taken.

**`10.3390/ijms27167157`** — genotype–phenotype concordance and cross-database interoperability in NCBI Pathogen Detection and BV-BRC metadata. Same data source as this project's clinical metadata, and a worked method for the validation panel described in roadmap §3.3.

---

## 9. Limitations

- **Open-access filter applied**, so closed-access work is absent by construction. This may systematically exclude older or society-journal literature.
- **150 records retrieved per search**, not exhaustive retrieval of 3,860 / 1,522 / 1,528 hits. Records were taken in Europe PMC relevance order, which was not independently validated.
- **Title and abstract screening only.** No full-text pass, so inclusion decisions rest on abstract content alone and some included papers may prove less relevant on reading.
- **Single screener**, no second reviewer and no inter-rater agreement measure.
- **Search terms in English only**, and concept clauses were hand-constructed rather than derived from a controlled vocabulary.
- The claim that **no graded ESKAPE catalogue exists** is supported by absence in this screen plus PaVarDB's own published statement of the lacuna — but absence of evidence within a 150-record sample is weaker than an exhaustive search would provide.

Raw 150-record screening logs for both searches were generated in scratch space and are not retained. The included-paper lists above are complete as screened; the full logs can be regenerated or persisted on request.
