# Thesis problem statement and first DAC preliminary-data map — working draft (v2)

*Built from the project documentation (README, project.md, project summary, evaluation and five-month roadmap, Cryptic AMR roadmap, dN/dS results, annual-report draft). Numbers are quoted from those documents. They have not been re-derived from the raw data, which is not mounted in this session. The timeline in section 4 is a proposal to discuss with your supervisor.*

## 1. Problem statement

Antimicrobial-resistance genes (ARGs) in ESKAPE pathogens are catalogued almost entirely by sequence similarity to curated references (RGI/CARD). This has two consequences. First, it returns only the recognisable resistome, so ARGs that have diverged past the similarity threshold are excluded by design. Second, it says nothing about whether an ARG can move between cells. There is no integrated account of (a) how ARG families are distributed and how their alleles diverge across Gram-positive and Gram-negative ESKAPE pathogens, (b) whether ARGs on mobile genetic elements (MGEs) differ from chromosomally fixed ARGs in selection regime and in sequence-intrinsic and genomic-context features, and (c) whether feature-based representations can recognise ARG-like proteins below the homology detection threshold.

## 2. Central question

> Do mobile ARGs differ systematically from chromosomally fixed ARGs in evolutionary divergence, selection regime and sequence features, and do those differences survive control for gene-family identity, host species and acquisition recency?

All claims are associational. The genomic data are cross-sectional and do not observe transfer events.

## 3. Aims, thesis chapters and manuscripts

| Aim | Question | Unit of analysis | Falsifiable prediction | Required control | Output |
|---|---|---|---|---|---|
| 1. Landscape and mobility | Which CARD families occur in both Gram-positive and Gram-negative pathogens, and is family mobility associated with cross-pathogen breadth? | Family; genome-level prevalence | Families with more conjugative or mobilizable carriage occur in more pathogens | Genome sample size per pathogen, family abundance, intrinsic versus acquired status, lineage non-independence | Chapter 1, manuscript M1 (the primary paper per the project documents) |
| 2. Divergence and selection | Do alleles show host-specific or cross-pathogen structure, and does branch-site selection differ between mobile and chromosomal lineages? | Dereplicated orthologous alleles within a family | Foreground (mobile) branches show a different dN/dS regime | Recombination screen first; acquisition recency; at least about 15-20 haplotypes per stratum | Chapter 2, manuscript M2 |
| 3. Feature profile by mobility | Do physicochemical, domain, structural, language-model and genomic-context features differ between mobile and chromosomal alleles of the same family? | Allele, compared within family | A mobility classifier beats a baseline of family plus host species plus GC/codon features, under leave-family-out and leave-species-out CV | Family identity, host species, GC and codon usage, near-duplicate leakage | Chapter 3, manuscript M3 |
| 4. Detection beyond homology (contingent) | Do feature-based models recover ARG family or ARG status below the homology floor? | CARD reference protein, then ORF | Feature model overtakes BLAST and profile-HMM somewhere below about 40% identity in an identity-stratified holdout | Sequence-cluster CV groups; a non-ARG negative class; profile-HMM comparator | Chapter 4, manuscript M4, only if the gate is passed |
| Enabling: MGE annotation | Which chromosomal ARGs sit on ICEs, IS elements, integrons or prophages? | ARG with flanking region | Splits the current "chromosomal" class into chromosomal-no-MGE and chromosomal-MGE | Tool sensitivity and assembly fragmentation | Feeds Aims 1-3; planned after the first DAC |

The gate for Aim 4 is the identity-stratified holdout in the Cryptic AMR roadmap (section 5). It needs no new features, no GPU and no structure prediction, so it can be run in year 1 and the committee can see early whether Aim 4 stands.

## 4. Proposed five-year sequence (for discussion)

| Year | Work |
|---|---|
| 1 | Data freeze and reconciliation; formal mobility-versus-breadth test (M1); identity-stratified holdout gate for Aim 4; first DAC |
| 2 | ICE, IS, integron and prophage annotation; phylogenetic evidence; recombination-aware branch-site selection (M2) |
| 3 | Within-family feature comparison with Evo 2 (CDS and CDS plus flank) and protein features (M3) |
| 4 | Detection beyond homology if the gate was passed (M4); otherwise extend Aims 2-3 |
| 5 | Integration across aims; thesis writing |

## 5. Design decisions recorded from you

1. "Present in both Gram-positive and Gram-negative" means the same CARD family. Gram-positive: *E. faecium*, *S. aureus*. Gram-negative: *A. baumannii*, *Enterobacter*, *K. pneumoniae*, *P. aeruginosa*. The 7 families shared by all six pathogens are a strict subset of the answer. The full set (any Gram-positive and any Gram-negative) has not been reported in the documents I read. Define a minimum-evidence rule (for example RGI Perfect/Strict hits, or a minimum number of genomes) and test sensitivity to it.
2. ICE, IS-element, integron and prophage annotation is deferred until after the first DAC. State this as planned work, and state the current limitation: MOB-suite classifies plasmids only, so the "chromosomal" class is mixed.
3. Evo 2 input is CDS plus flanking sequence. Design points that follow:
   - Treat CDS-only and CDS-plus-flank as separate feature sets with an ablation. Flanking context (transposases, relaxases, IS elements) carries mobility information almost by construction, so it measures genomic context and not intrinsic sequence properties.
   - Flank length is a free parameter that needs justification. Genes near contig ends have truncated flanks, and plasmid contigs in draft assemblies are often short, so report flank completeness.
   - CARD reference proteins have no genomic context, so flank-based features apply to Aims 2-3 and to a genome-scale screen. They do not apply to the Aim 4 gate.
   - Embeddings will encode host taxonomy, GC and codon usage. Any mobility signal must persist under leave-species-out evaluation against GC and codon baselines.
   - This session has no GPU, so Evo 2 would run on remote compute.

## 6. Preliminary data available for the first DAC

| Aim | What exists | Source | Caveat to state on the slide |
|---|---|---|---|
| 1 | 381,491 ARG records from 9,947 genomes and six pathogens, all with a mobility label: 332,524 chromosomal, 31,705 conjugative, 9,775 mobilizable, 7,487 non-mobilizable | Evaluation draft, section 6.2 | Descriptive. Pathogens differ in genome count and ARG burden, so no pathogen-level risk claim |
| 1 | Plasmid-borne share by pathogen, computed from that table: *P. aeruginosa* 0.2%, *A. baumannii* 4.5%, *S. aureus* 6.7%, *K. pneumoniae* 18.6%, *Enterobacter* 19.3%, *E. faecium* 46.9% (12.8% overall) | Derived from the same table | Mobility class is strongly species-dependent |
| 1 | 7 families shared by all six pathogens; 30 shared by the four Gram-negatives, including 9 beta-lactamase families | Project summary, section 4.1 | Family count is 141 in one document and 145 in another |
| 1/2 | Nine-family beta-lactamase tree of 577 sequences. Clades needed to explain the tree: Ambler class 7 (4 categories), CARD family 30 (9 categories), organism 248 (4 categories) | Project summary, section 4.3 | Pilot; Gram-negatives only; a tree is consistent with, but does not prove, horizontal transfer |
| 2 | OXA at CDS level (321 sequences): 114 of 116 *A. baumannii* sequences in one clade; 125 of 163 *P. aeruginosa* sequences in a *P. aeruginosa*-dominated lineage; about 93% of sequences in organism-pure clusters | Project summary, section 4.4 | Shows intrinsic lineage diversification alongside cross-pathogen movement |
| 2 | Pairwise PAML: 407 of 19,071 genes (2.1%) in 24 of 92 families have dN/dS above 1 | dN/dS results summary | Exploratory: one reference per family, not mobility-stratified, not branch-site. Do not present as a test of Aim 2 |
| 3 | Classifiers for 84 CARD families (LightGBM, one-versus-rest, SHAP). Examples: qnr led by CTC_762; MFS by middle-third aliphatic index with PF07690 in 93 of 119 proteins; Erm-like by charged-residue entropy | project.md; annual-report draft | Classifies CARD family labels, not mobility. Aim 3 itself has not been started |
| 4 | None. The roadmap states that no experiments from it have been executed | Cryptic roadmap, header | The negative class and sequence-cluster CV groups are still missing |

## 7. Inconsistencies to reconcile before the meeting

A committee member who adds up two slides will find these.

1. ARG families: 141 versus 145 (the evaluation draft says two entries are in the mobility matrix but absent from the summaries).
2. CARD families in the ML dataset: 395 versus 396.
3. Descriptors: 1,035 versus 996. Sequences: 5,479 in 84 families versus 6,206 memberships from 6,059 unique proteins.
4. Pairwise PAML: 14,459 estimates across 73 FASTA sets (evaluation draft) versus 19,071 genes in 92 families (dN/dS results). These look like different analysis versions.
5. Median genes per family: 20 versus 8, depending on the file (README).
6. `Negative_dataset.txt` holds Random Forest hyperparameter notes, not a negative set.

## 8. Questions the committee is likely to ask

| Question | Honest current answer |
|---|---|
| Is a CARD family a set of homologues? | No. It is a mechanism label, so alignment and dN/dS are restricted to demonstrably homologous alleles |
| Does mobility cause wider distribution? | Not testable from cross-sectional data. The claim is association, after control for abundance and sampling |
| Is *K. pneumoniae* sampling driving the results? | It holds 179,223 of 381,491 records. Genome-level prevalence and per-pathogen stratification are planned |
| Are MOB-suite plasmid calls reliable on draft assemblies? | Sensitivity analysis on higher-quality assemblies is planned; ICEs and other MGEs are not yet annotated |
| Why Evo 2 instead of a protein language model? | It must be justified by a comparison. Include a protein-model baseline and GC/codon baselines |
| How will novel ARGs be validated? | There is no ground truth for undiscovered ARGs. Validation is identity-stratified cross-validation, and the limitation is stated up front |
