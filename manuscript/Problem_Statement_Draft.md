# Problem statement — working draft (v1)

*Prepared from the project documentation (README, project.md, project summary, evaluation/roadmap, Cryptic AMR roadmap, dN/dS results). Numbers quoted are from those documents and have not been re-derived from the raw data, which is not mounted.*

## 1. Problem statement

Antimicrobial-resistance genes (ARGs) in ESKAPE pathogens are catalogued almost entirely by sequence similarity to curated references (RGI/CARD). This has two consequences. First, it returns only the recognisable resistome, so ARGs diverged past the similarity threshold are excluded by design. Second, it says nothing about whether an ARG can move between cells. As a result there is no integrated account of (a) how ARG families are distributed and how their alleles diverge across Gram-positive and Gram-negative ESKAPE pathogens, (b) whether ARGs carried on mobile genetic elements differ from chromosomally fixed ARGs in selection regime and in sequence-intrinsic features, and (c) whether feature-based representations (physicochemical, structural, domain, and sequence language-model features) can recognise ARG-like proteins below the homology detection threshold.

## 2. Central question

> Do mobile ARGs differ systematically from chromosomally fixed ARGs in evolutionary divergence, selection regime and sequence-intrinsic features, and do those differences survive control for gene-family identity, host species and acquisition recency?

All claims are associational. The data are cross-sectional and do not observe transfer events.

## 3. Aims, predictions and required controls

| Aim | Question | Unit of analysis | Falsifiable prediction | Control that must be in the design |
|---|---|---|---|---|
| 1. Landscape | Which ARG families occur in both Gram-positive and Gram-negative pathogens, and how divergent are their alleles between pathogens? | Family (distribution); dereplicated allele or haplotype (divergence) | Families with a larger plasmid-associated share show weaker clustering by host pathogen (more cross-pathogen mixing) | Unequal genome sampling per pathogen; define "shared" at family level and at allele level separately |
| 2. Selection | Within recombination-screened homologous alleles, does branch-site selection differ between mobile and chromosomal lineages? | Orthologous alleles within a family | Foreground (mobile) branches show a different dN/dS regime than background branches | Recombination screen first; acquisition recency; at least about 15-20 haplotypes per stratum (floor stated in the project summary) |
| 3. Feature profile | Do physicochemical, domain, structural and language-model features differ between mobile and chromosomal alleles of the same family? | Allele, compared within family | A mobility classifier beats a baseline of family identity plus host species plus GC/codon features, under leave-family-out and leave-species-out CV | Family identity; host species; GC content and codon usage; near-duplicate leakage |
| 4. Detection beyond homology (contingent) | Do feature-based models recover ARG family or ARG status below the homology floor? | CARD reference protein, then ORF | Feature model overtakes BLAST and profile-HMM somewhere below about 40% identity in an identity-stratified holdout | Sequence-cluster CV groups; a non-ARG negative class; profile-HMM as comparator |

Aim 4 is gated by the identity-stratified holdout in the Cryptic AMR roadmap (section 5). If the curves do not cross, Aim 4 is dropped and Aims 1-3 stand alone.

## 4. What I would change in the current wording

| Current wording | Problem | Suggested wording |
|---|---|---|
| Evolutionary divergence of AMR genes across ESKAPE pathogens | Undefined measure | Inter-pathogen divergence of ARG alleles within families (identity, dS, host-clustering of the gene tree) |
| Role of natural selection on the emergence of AMR gene patterns | "Emergence" implies a temporal or causal claim the data cannot support | Differences in selection regime between mobile and chromosomal ARG lineages (branch-site dN/dS, recombination-screened) |
| Tackle the problem of ARG detection by homology | Reads as if homology were the solution | Test whether feature-based models recover ARGs below the homology detection threshold |
| Chromosomally fixed versus highly mobile (ICE, plasmids) | Chromosomal does not mean immobile, and ICEs are not annotated yet | Chromosomal, no MGE evidence versus MGE-associated (plasmid class from MOB-suite; ICE, IS, integron, prophage annotation added) |
| Evolutionary-context foundation models such as Evo 2 | Input and role unspecified | State the input (CDS only, or CDS plus flanking region) and the question the embedding answers |

## 5. Issues the current objective does not yet address

1. **Mobility class is confounded with species.** From the evaluation table, the plasmid-borne share of ARG records is about 0.2% in P. aeruginosa and 4.5% in A. baumannii, against 46.9% in E. faecium, 19.3% in Enterobacter and 18.6% in K. pneumoniae (12.8% overall). Pooled mobile-versus-chromosomal comparisons will largely compare species. Analyses need species stratification or within-species estimates.
2. **Mobility class is confounded with gene family.** Plasmid-borne copies are concentrated in particular families (for example the beta-lactamases), whereas chromosomal copies include efflux systems and regulators. A pooled feature comparison would mostly rediscover family identity. The within-family comparison in Aim 3 addresses this.
3. **"Chromosomal" is a mixed class.** MOB-suite reconstructs plasmids from assemblies. It does not annotate ICEs, transposons, integrons or prophages, so an ARG on a chromosomal ICE is currently labelled chromosomal. Your note on ICEs needs an added annotation step. Plasmid assignment from fragmented draft assemblies also carries error, which should be stated as a limitation.
4. **Label circularity in the detection aim.** The ARG labels come from homology. A "beyond homology" claim therefore needs sequence-cluster CV groups, an identity-stratified holdout and a negative class. The roadmap lists the first and last as blocking prerequisites, and the CV group file is still unpopulated according to the roadmap.
5. **Domain features reintroduce homology.** Pfam and InterPro matches are profile-HMM matches. For the beyond-homology claim, report results with and without domain features.
6. **Decouple the two questions.** "Do features differ by mobility class" (Aim 3) can be answered on ARGs already detected by RGI and needs no detection model. Keeping it separate from Aim 4 stops a weak detector from undermining a sound association analysis.
7. **Evo 2 embeddings will encode the host.** A DNA language model will carry taxonomy, GC content and codon usage. Any mobility signal must be shown to persist under leave-species-out evaluation, with GC and codon features as the baseline. A separate use worth testing: the per-gene likelihood or deviation from the host genome as a proxy for acquisition recency, which is the confound the project already names for Aim 2.
8. **Existing dN/dS output is exploratory.** The table of 19,071 genes compares each gene with one reference per family by pairwise PAML. It is not mobility-stratified and not branch-site. It does not yet answer Aim 2.

## 6. Decisions needed from the project lead

1. Is this one manuscript or two? The Cryptic roadmap states that the mobility, distribution and selection thread stays the primary manuscript and the detection work is an extension. The objective as written merges them.
2. What does "present in both Gram-positive and Gram-negative" mean: the same CARD family, or the same allele? The summary reports 7 families shared by all six pathogens. That is a strict subset of the "any Gram-positive and any Gram-negative" set, which I have not seen reported.
3. Will ICE, IS-element, integron and prophage annotation be added, or will the study be limited to plasmid class?
4. What is the input to Evo 2 (CDS or CDS plus flanking sequence), and is remote GPU compute available? This session has no GPU.
5. Are the Aim 3 features to be computed from predicted 3D structures or from sequence-based predictors? This decides how much structure prediction is needed.
