# ESKAPE AMR Mobility & Selection-Pressure Project: Hypothesis, Preliminary Results, and Path to Completion

Synthesized from the project's working notes files:
- `Results/Mobsuite/AB_Contig_Plasmid_Metrics_Notes.md`
- `Results/DRUG_class_family_mapping/Gene_Family_Alignment_and_Drug_Class_Discussion.md`
- `Results/Phylogenetics/Beta_Lactamase_Pilot/Tree_Terminology_Glossary.md`

---

## 1. Background

Antimicrobial resistance (AMR) genes don't all pose the same threat. A gene's location in the genome -- chromosome vs. plasmid -- and, if on a plasmid, whether that plasmid can conjugate into other cells, determines whether it can spread between strains and species. Knowing *which* resistance genes an ESKAPE pathogen carries is only half the picture; knowing *how mobile* those genes are is what predicts future spread.

This project characterizes AMR genes across the six ESKAPE pathogens (*Acinetobacter baumannii*, *Enterococcus faecium*, *Enterobacter* spp., *Klebsiella pneumoniae*, *Pseudomonas aeruginosa*, *Staphylococcus aureus*) across ~9,947 genomes, using RGI/CARD for resistance-gene detection and MOB-suite (`mob_recon` + `mob_typer`) for chromosome/plasmid location and three-way mobility classification (conjugative / mobilizable / non-mobilizable).

## 2. Literature gap

A six-paper literature review (full detail and comparison table in `AB_Contig_Plasmid_Metrics_Notes.md`, "Research Gap Analysis and Study Design") found two gaps:

1. **Tool-completeness gap**: studies using this project's exact toolchain (RGI/CARD + MOB-suite) stop at `mob_recon` and never use `mob_typer`'s conjugative/mobilizable/non-mobilizable classification.
2. **Scope gap**: no existing study analyzes all six ESKAPE pathogens together with the full pipeline -- every paper reviewed is single-organism, single-family, or broad-and-ESKAPE-agnostic.

## 3. Central objective and hypotheses

> **Does an AMR gene family's genomic mobility classification predict its cross-pathogen distribution and the selection pressure acting on it, across the six ESKAPE pathogens?**

Deliberately phrased as *predicts*, not *causes* -- an associational question a cross-sectional genomic dataset can actually answer, not a causal claim it can support.

- **Layer A (foundational, not a hypothesis test)**: a systematic mobility-risk profile -- % chromosomal / non-mobilizable / mobilizable / conjugative per gene family, per pathogen. What B and C are tested against.
- **Layer B (associational hypothesis, H_B)**: gene families predominantly carried on conjugative plasmids show significantly greater cross-ESKAPE-genus presence than families that are predominantly chromosomal or non-mobilizable. A direct, ESKAPE-scoped extension of Che et al. (PNAS, 2021), which showed ARG enrichment on conjugative plasmids broadly, not within a defined ESKAPE panel. Falsifiable via Kruskal-Wallis (or similar) across the four mobility classes.
- **Layer C (associational, mechanistic hypothesis, H_C)**: within a gene family, lineages associated with conjugative plasmids show a different selection regime (branch-site dN/dS) than chromosomal/non-mobilizable lineages of the same gene. Falsifiable via the branch-site likelihood-ratio test. **Confound disclosed up front**: mobility class correlates with recency of acquisition, and recency alone can elevate dN/dS independent of mobility itself -- the defensible claim is "selection regime differs by mobility class," not "mobility causes the difference," unless recency is controlled for.

## 4. Preliminary results

### 4.1 Gene-family distribution across all six pathogens (Layer A foundation)

- A comprehensive AMR gene family catalog was built: **141 distinct families** across the full dataset (`AMR_Family_ID_Mapping.csv`), spanning all six pathogens.
- A gene-family distribution heatmap (family x pathogen, cell = unique gene count) was built and saved (`Results/AMR_GeneDistribution/`).
- Shared-family analysis: **7 gene families are shared across all six ESKAPE pathogens**; **30 gene families are shared across the four gram-negative pathogens** (AB, ENT, Kp, Pa) specifically, of which **9 are beta-lactamases** (CARB, CMY, CTX-M, GES, IMP, NDM, OXA, OXA-2-like, TEM).
- **Data-quality finding**: one family -- the MexAB-OprM efflux system and its regulators (MexA, MexB, MexG, MexR, ArmR, nalC, nalD) -- was silently invisible to every percentage/heatmap/shared-family calculation. Root cause: CARD leaves the `AMR Gene Family` field genuinely blank for these entries, which pandas read as `NaN`, producing a family folder literally named `nan.fasta`; the string `"nan"` is then treated as a missing value by `pandas.read_csv` on any later re-read, silently dropping the row from percentage calculations even though the raw count (7 unique genes, 8,065 records -- found in essentially every Pa genome) was correct. This is arguably the single most clinically important intrinsic resistance mechanism in *P. aeruginosa* (baseline multidrug efflux, independent of any acquired gene), and the bug is now documented and fixable in `Gene_Family_Alignment_and_Drug_Class_Discussion.md`.

### 4.2 Mobility mapping infrastructure (MOB-suite integration)

- Full RGI/CARD + MOB-suite (`mob_recon` + `mob_typer`) chromosome/plasmid/mobility mapping pipeline built and validated (`ARG_Chromosome_Plasmid_Mapping.ipynb`, `Mobsuite_Chromosome_Plasmid_Mobility.ipynb`).
- Validated in full detail for AB (967 genomes): 2,714 contigs (1,313 chromosome + 1,401 plasmid) resolving to 1,234 assembled plasmids, split **430 conjugative / 388 mobilizable / 416 non-mobilizable**. Every number cross-checked directly against the raw MOB-suite output, not just notebook printouts (full derivation in `AB_Contig_Plasmid_Metrics_Notes.md`).
- Classification logic verified empirically, not assumed: `molecule_type` is driven almost entirely by Mash similarity to MOB-suite's reference plasmid database (100% of plasmid contigs hit a reference plasmid vs. ~0% of chromosome contigs); `predicted_mobility` is driven exactly by presence/absence of relaxase and MPF/T4SS genes, matching the textbook definition precisely (100% concordance checked against AB's 1,234 plasmids).

### 4.3 Phylogenetic pilot: does clustering track gene family or host organism? (Layer B plausibility check)

A 577-sequence protein tree was built from the 9 gram-negative-shared beta-lactamase families (all 4 organisms pooled), and the tree's branching structure was tested quantitatively -- not by eye -- for whether it's explained by enzyme identity or by host organism, using a monophyletic-clade-count metric (fewer clades needed = that label explains the tree better; best case = number of categories, worst case = 577).

| Labeling scheme | Categories | Clades needed | Distance from best |
|---|---|---|---|
| Ambler class (enzyme mechanism) | 4 | 7 | +3 -- essentially as clean as possible |
| CARD family | 9 | 30 | +21 -- still very clean |
| Organism | 4 | 248 | +244 -- barely better than total fragmentation |

**Result: tree structure is explained by which enzyme a sequence is, not which bacterium it was found in** -- strong, quantitative evidence consistent with pervasive horizontal gene transfer across the ESKAPE panel, and a direct plausibility check supporting H_B before the formal statistical test is run.

### 4.4 The OXA exception: a deeper, gene-level look

OXA beta-lactamase was the one family that fragmented substantially in the pooled tree (14 of the 30 "excess" clades), so it was pulled out and re-examined alone at the CDS/nucleotide level (321 sequences, its own dedicated tree).

- **AB**: 114 of 116 sequences (98%) form a single organism-pure clade.
- **Pa**: 125 of 163 sequences form a Pa-dominated lineage -- but 3 Kp sequences sit nested inside it, more closely related to Pa's lineage than to typical Kp OXA genes.
- **Kp and ENT**: scattered, showing essentially no self-clustering.
- Overall: ~93% of OXA sequences sit in organism-pure clusters (mostly the two large AB/Pa clades); ~7% sit in small clusters mixing 2-3 organisms.

**Interpretation**: OXA's unusually high diversity (173 distinct genes -- by far the most of any beta-lactamase family) is driven substantially by AB's and Pa's own *intrinsic*, vertically-inherited allelic radiation, not purely by horizontal transfer -- the opposite pattern from TEM/CTX-M/NDM, which each formed one clean, organism-mixed clade. This is scientifically valuable precisely because it complicates the simple story: it shows H_B is a real, falsifiable test (OXA could have broken the general pattern, and it did, partially) rather than a foregone conclusion.

### 4.5 Full beta-lactam pipeline infrastructure (57 families)

Beyond the 9-family pilot, the complete pipeline needed to scale this analysis to all 57 gram-negative-relevant beta-lactam families has been built and debugged:

- A comprehensive family-to-short-code mapping (57 families) and a universal family-ID mapping (`AMR_Family_ID_Mapping.csv`, 141 families, `f1`-`f141`).
- A full relabel -> CD-HIT dedup -> pool -> frame-check -> align -> tree pipeline, built as notebook cells and verified end-to-end on the 9-family/OXA-only pilots.
- **Real bugs found and fixed during this process** (documented in `Gene_Family_Alignment_and_Drug_Class_Discussion.md`): wrong dictionary used in a relabeling loop (silently processing 30 families instead of 57); folder-name casing mismatches after external CD-HIT runs (`KP`/`PA` vs `Kp`/`Pa`); leftover variable names from copy-pasting the OXA-only version into the all-beta-lactam version (would have silently mixed data from two different files, or crashed with `NameError`); and a FastTree "Non-unique name" crash traced to CARD gene names that are full free-text descriptions (e.g. `Escherichia coli soxS with mutation conferring antibiotic resistance`) rather than short identifiers -- FASTA/FastTree treat everything before the first whitespace as the unique name, so these collapsed 784 sequences onto 5 truncated names. Fixed by sanitizing headers (spaces -> underscores) either post-hoc or, in the newer ID-based relabeling scheme, at the source.

## 5. Methodological lessons learned (carried forward into future steps)

- **CARD "AMR Gene Family" is a mechanism label, not an orthology group.** It can bundle true alleles, paralogs (different genes in the same operon), xenologs (independently-acquired, non-homologous genes converging on the same function), and even chromosomal point-mutation calls that aren't acquired genes at all. Alignment for dN/dS is only valid at the level of true alleles -- confirmed necessary by direct inspection (e.g. the RND family bundling `adeA`/`adeB`/`adeC`/`CRP`/`soxS`).
- **FASTA headers need two properties for downstream tools to work**: uniqueness (a running per-record index, since the same gene name can repeat hundreds of times) and whitespace-freedom (multi-word CARD gene names break any tool that parses "name" as "everything before the first space," as FastTree does).
- **A string that spells "nan" is a landmine in any pandas pipeline** -- gets silently read back as a missing value regardless of column meaning, even though it was written correctly.
- **dN/dS results will need a recombination screen** before being trusted for beta-lactamase families specifically (blaTEM/CTX-M/OXA lineages are known for mosaicism, which can manufacture false positive-selection signals) -- not yet built into the pipeline.

## 6. Future steps to reach a logical conclusion

1. **Complete Layer A at full scale**: extend the validated mobility-mapping pipeline (currently detailed for AB) to compute the full mobility risk profile -- % chromosomal/non-mobilizable/mobilizable/conjugative per gene family -- across all six pathogens.
2. **Run the formal Layer B statistical test**: Kruskal-Wallis (or equivalent) testing whether cross-ESKAPE-genus presence differs significantly across the four mobility classes, with effect size reported alongside significance.
3. **Scale the phylogenetic approach beyond the 9-family beta-lactamase pilot**: run the same monophyletic-clade-count method systematically across all 30 gram-negative-shared families, and correlate the *degree* of species-clustering breakdown against the graded mobility class (chromosomal -> non-mobilizable -> mobilizable -> conjugative) -- turning "conjugative genes are shared across species" into a dose-response phylogenetic test, not just a presence/absence claim.
4. **Finish the full 57-family beta-lactam CDS tree**: the relabel/dedup/pool/align/tree pipeline is built and debugged -- run it to completion once CD-HIT has been run on the full ID-relabeled family set.
5. **Add a recombination screen** before interpreting any dN/dS result for beta-lactamase families specifically.
6. **Build Layer C**: branch-site dN/dS analysis (PAML `codeml` or HyPhy aBSREL/FUBAR) per gene family, with conjugative-plasmid branches as the foreground, background = chromosomal/non-mobilizable. Requires per-gene alignments (not per-family) per the methodological lesson above, and adequate haplotype diversity per family (a floor of roughly 15-20 copies per mobility-class subgroup).
7. **Three-way synthesis**: combine phylogenetic signal (step 3) x mobility class (step 1) x selection pressure (step 6) into one integrated result -- does loss of species-clustering coincide with elevated dN/dS right at the inferred host-jump branch? This is the strongest, most defensible version of the project's central claim if it holds.
8. **Write up with limitations stated upfront, not conceded under review**: cross-sectional data (no direct observation of transfer events), the recency-of-acquisition vs. mobility-class confound for H_C, and statistical power limits for low-diversity gene families.

## 7. Source documents

- `Results/Mobsuite/AB_Contig_Plasmid_Metrics_Notes.md` -- MOB-suite metrics validation, literature gap analysis, study design, objective/hypothesis framing.
- `Results/DRUG_class_family_mapping/Gene_Family_Alignment_and_Drug_Class_Discussion.md` -- alignment methodology, gene-tree HGT discussion, the `nan`/MexAB-OprM finding.
- `Results/Phylogenetics/Beta_Lactamase_Pilot/Tree_Terminology_Glossary.md` -- tree interpretation methodology, full beta-lactam family catalog, pipeline bug fixes.
- `AMR_Gene_Family_Distribution.ipynb`, `ARG_Chromosome_Plasmid_Mapping.ipynb`, `Mobsuite_Chromosome_Plasmid_Mobility.ipynb` -- the underlying analysis notebooks.
