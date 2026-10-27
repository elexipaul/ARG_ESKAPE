# Cryptic AMR Classification: Scientific Rationale and Forward Roadmap

**Draft date:** 16 September 2026
**Last updated:** 17 September 2026 — added §13 (domain/HMM annotation methodology extracted from Ghosh et al.) and corrected the competitive positioning in §10
**Status:** Strategic direction document — no experiments from this roadmap have been executed yet
**Relationship to existing work:** Extends the protein-feature machine-learning track described in `Project_Evaluation_Current_Status_and_Five_Month_Roadmap.md` (§4.4, §5.6, §6). Does **not** replace the mobility–distribution–selection thread, which remains the primary manuscript.

---

## 1. Scientific thesis

The proposed direction rests on three claims, stacked. Each must hold independently.

### 1.1 Homology-based detection has a sensitivity floor, and the floor is structural rather than accidental

Every standard resistance-gene caller — RGI/CARD, AMRFinderPlus, ResFinder — identifies genes by sequence similarity to a curated reference set. A query protein is called a resistance gene because it resembles one already known to be a resistance gene, above some identity or bit-score threshold.

Those thresholds are not a defect. Below a certain similarity, a genuinely related enzyme cannot be distinguished from coincidental resemblance, so conservative cutoffs are the correct engineering decision for controlling false positives.

The consequence is nonetheless unavoidable: any resistance determinant that has diverged past the threshold is not occasionally missed, it is **systematically excluded by the design of the method**. What these tools return is the *recognisable* resistome, and it functions as a lower bound on the actual resistome.

This matters because such divergence is expected rather than hypothetical. Many resistance enzymes descend from ordinary housekeeping proteins — β-lactamases share ancestry with penicillin-binding transpeptidases, aminoglycoside-modifying enzymes with kinases and acetyltransferases. Environmental bacteria carry deep reservoirs of distant homologues. Sequences occupying the space between "clearly a known ARG" and "clearly unrelated" genuinely exist.

### 1.2 Structure is conserved across greater evolutionary distance than sequence

This is the mechanistic premise on which the entire approach depends.

Protein function is determined by three-dimensional architecture — the fold, active-site geometry, the spatial arrangement of catalytic residues. Sequence is one lossy encoding of that architecture: many sequences fold to the same shape and perform the same chemistry. Over evolutionary time, two enzymes can fall below 20% sequence identity while retaining the same fold and mechanism.

The two signals therefore erode at very different rates. Alignment-based detection tracks the fast-eroding one; a classifier built on structural and physicochemical descriptors tracks the slow-eroding one.

This is a hypothesis to be tested on this dataset, not an established result for it. §5 describes the experiment that converts it into a demonstrated claim or refutes it.

### 1.3 Existence is not the interesting claim — transmissibility is

Recovering divergent resistance-gene candidates is, alone, a modest finding. Many resistance-like genes presumably sit inertly in chromosomes indefinitely.

What makes a resistance gene a clinical and epidemiological concern is that it can *move* — into a new strain, lineage, or species. For these same genomes, this project has already reconstructed via MOB-suite whether each ARG-bearing contig is chromosomal or plasmid-borne, and for plasmids, whether conjugative, mobilizable, or non-mobilizable. Every cryptic candidate can therefore be placed on a dissemination-risk axis rather than merely reported.

The existing literature splits cleanly either side of this and does not cross it. Machine-learning work predicts novel resistance genes without asking whether they can move; mobilome work characterises the mobility of resistance genes that are *already known*. The intersection — **how mobile is the portion of the resistome we cannot currently detect?** — is unclaimed, and it is the differentiator that makes this a surveillance paper rather than another methods paper.

### 1.4 Thesis in one sentence

> Homology-based ARG detection has a sensitivity floor; structure-aware feature-based classification recovers family assignment below that floor; and in ESKAPE genomes a fraction of these cryptic candidates sit on mobile elements, representing latent transmissible resistance potential invisible to current surveillance.

---

## 2. Definition discipline: which "cryptic AMR" this project can evidence

The term carries at least three distinct meanings, routinely conflated in the literature and by reviewers. The framing must be explicit in the title and abstract.

| Meaning | Definition | Project can evidence? |
|---|---|---|
| **(A) Divergent / hidden resistome** | Real resistance determinants too far from curated references for homology tools to call | **Yes — this is the target** |
| **(B) Silent / unexpressed** | ARGs present in the genome but not transcribed until activated (IS insertion, promoter mutation) | Partially; parked as follow-up |
| **(C) Phenotypically cryptic** | Resistance missed by standard susceptibility testing (heteroresistance, inducible resistance) | No as a primary claim; usable as a validation anchor |

**(A) is the target.** The classifier, the pairwise identity matrix, the structural feature work and the ESKAPE genome set all serve it directly.

**(B) is a viable but smaller follow-up.** A "pseudogenised resistome" analysis is achievable from assemblies alone — ARGs disrupted by IS insertions, frameshifts, premature stops, truncations. Its attraction is synergy with the project's existing PAML/HyPhy pipeline: genuinely inactivated genes should show relaxed selection, a testable prediction using tooling already built. Its weakness is that silence inferred from sequence, without expression data, is indirect.

**(C) cannot carry a paper at the available sample size** (§3.3) but is valuable as an orthogonal validation panel, and combining A with C produces the strongest overall package: structure-aware detection of divergent determinants, anchored by isolates whose resistance phenotype has no known genetic explanation.

---

## 3. Evidence base already in hand

### 3.1 Machine-learning track

- **6,059 unique CARD representative proteins**, one row per ARO accession in the current pipeline, with multi-family and multi-drug-class membership handled as multi-label target matrices rather than exploded rows.
- **996 retained features** from Pfeature + ProFET after prefix exclusions (`PRI_`, `RRI_`).
- **Three classifier notebooks at the project root, in increasing maturity.** Use the newest; the other two are effectively superseded:
  1. `family_classifier_importance_3.ipynb` — oldest, ARG-family only; its relative input paths no longer resolve from the project root.
  2. `drug_class_classifier_importance.ipynb` — drug-class only, but introduces `canonical_aro()`, generalised feature exclusion, and the `SEQUENCE_CLUSTER_CSV` hook.
  3. `ARG_family_drug_class_SHAP_comparison.ipynb` — **current**. Family and drug-class targets jointly, inner-CV threshold selection, out-of-distribution/applicability checking (PCA reconstruction residual plus nearest-neighbour distance), resumable per-target caching, and a working raw-FASTA → prediction path with an `evidence_verdict` reason-code system.
- **84 families modelled** at a minimum support of 10 members.
- The existing OOD/applicability machinery becomes, without modification, the "when should a cryptic call be trusted" component — a requirement for credibility that is already built.

### 3.2 Genomic and mobility data

- Approximately 10,000 ESKAPE genomes with RGI/CARD annotation and complete MOB-suite chromosome/plasmid and mobility classification across all six pathogen groups.
- *Note a discrepancy to reconcile:* project documentation reports 9,947 genomes; `ALL_ESKAPE_Clinical_metadata.xlsx` contains 10,009 rows. Resolve during the data freeze.
- `Pairwise_Alignment/card_protein_pairwise_identity_matrix.csv` and `pairwise_identity.npy` — full pairwise identity for the representative protein set. **This is what makes the central experiment possible.**

### 3.3 Phenotype data (newly identified)

`ALL_ESKAPE_Clinical_metadata.xlsx` is NCBI Pathogen Detection metadata and contains an `AST phenotypes` column alongside AMRFinderPlus genotype calls. Coverage is sparse but the data is clean where present (per-drug strings of the form `cefepime=R,ceftazidime=R,meropenem=S`).

| Organism group | Isolates | With AST | % |
|---|---|---|---|
| *Staphylococcus aureus* | 2,237 | 210 | 9.4 |
| *Klebsiella pneumoniae* | 4,023 | 72 | 1.8 |
| *Pseudomonas aeruginosa* | 1,379 | 28 | 2.0 |
| *Acinetobacter baumannii* | 997 | 13 | 1.3 |
| *Enterobacter* spp. (all) | ~630 | 8 | ~1.3 |
| *Enterococcus faecium* | 762 | 5 | 0.7 |
| **Total** | **10,009** | **336** | **3.4** |

At n = 336, with 63% of it *S. aureus*, this cannot be a discovery cohort — slicing by isolate × drug × organism exhausts it immediately, and any "across ESKAPE" claim would really be a claim about one organism. It is, however, a legitimate **orthogonal validation panel**: are predicted cryptic candidates enriched among isolates that are phenotypically resistant with no known genetic explanation? Even modest enrichment is meaningful support, and its absence is something to discover privately rather than at review.

### 3.4 Other assets

- Prior molecular-docking experience (`Autodocking/` in the workspace root), enabling a computational validation chain without wet-lab access.
- `Papers/New_idea_paper/` already contains *"Identification of Novel Antimicrobial Resistance Genes Using Machine Learning, Homology Modeling, and Molecular Docking"* — essentially the template for this direction.

### 3.5 Compute environment

No local GPU; no `torch` or `esm` installed. However: `mmseqs2` and `HMMER` are already available, a pressed Pfam-A.hmm (release 35.0) exists on disk inside the `antismash` environment, and structure prediction can be done through Biohub's hosted `esmfold2-fast-2026-05` endpoint (`SequenceStructureForgeInferenceClient`, `ESM_API_KEY`), which requires no local GPU. Protein lengths are favourable: mean 320 aa, median 290, maximum 1,452, with only 38 of 6,059 exceeding 1,000 aa.

**Outstanding prerequisite:** no Biohub API key exists yet; one must be created before any folding can run.

---

## 4. Prerequisites that block the thesis

These are not refinements. Each one, left unaddressed, invalidates a central claim.

### 4.1 Cross-validation grouping (blocking)

All three notebooks derive CV groups from a feature-space proximity proxy (`radius_neighbors_graph`, radius 1.0) calibrated to the ~1,000-dimensional physicochemical space. The correct fix is scaffolded — an optional `SEQUENCE_CLUSTER_CSV` of `aro_accession, cluster_id` — but is unpopulated (`= None`) everywhere.

Two consequences:

1. Adding any new feature block of different dimensionality silently degrades the proxy, allowing near-duplicate CARD entries to leak across folds and inflating apparent accuracy.
2. **More seriously for this direction:** the entire thesis is a claim about performance at low sequence identity. If near-duplicates leak, that claim is worthless and a reviewer will find it. The CV fix is load-bearing for the paper, not methodological hygiene.

*Calibration caveat:* the existing identity matrix uses global Needleman–Wunsch alignment with full alignment length as the denominator (EMBOSS `needle` convention). The conventional "40–50% identity = remote homolog" cutoff is calibrated against coverage-normalised *local* alignment and **does not transfer to this scale**. Determine the threshold empirically by sweep, cross-checked against a coverage-aware `mmseqs2` clustering.

### 4.2 The negative class (blocking for any genome screen)

Every model in this project is one-vs-rest *within* ARGs and has never seen a non-ARG protein. Applied to unannotated ORFs across ~10,000 genomes, it will confidently assign families to ribosomal proteins, transporters and anything else presented to it.

This is aggravated by the generic-fold problem (§7): a random bacterial GNAT-fold acetyltransferase genuinely resembles an AAC, because it genuinely is one, architecturally.

Required: a two-stage design — "is this an ARG at all?" followed by "which family?" — with a negative class drawn from non-ARG proteins in the same genomes, backed by the existing OOD rejection. Both, not either.

### 4.3 Benchmarking against the right comparator

The homology floor is tiered, not a single line:

| Method | Approximate useful limit |
|---|---|
| Pairwise BLAST | degrades below ~25–30% identity |
| Profile methods (PSI-BLAST, `hmmscan` vs Pfam-A) | ~20–25% |
| Profile–profile (HHsearch/HHpred) | ~10–15% |
| Structure comparison (TM-align, Foldseek, DALI) | no effective identity floor |

Benchmarking only against BLAST is a straw man and reviewers will say so. **The honest comparator is profile-HMM**, which the planned Pfam track already provides. Figures in this table are the commonly cited ranges and should be pinned to primary sources before publication.

---

## 5. The de-risking experiment — run this first

**Identity-stratified holdout.** Bin held-out CARD proteins by maximum sequence identity to the training set (<40%, 40–60%, 60–80%, >80%) and plot family-assignment accuracy for: BLAST best-hit, Pfam profile-HMM, and the existing Pfeature + ProFET model.

**If the curves do not cross — if the feature-based model does not overtake homology somewhere below ~40% identity — there is no paper**, and that should be known before any investment in folding 6,059 proteins or obtaining API access.

This experiment requires no new features, no GPU, no API key, and no structure prediction. It uses data already on disk. It is the cheapest possible test of the entire direction and should precede everything else in this document.

If a crossover does exist, structural features are likely to widen it, and the remaining investment is justified.

---

## 6. Feature roadmap

Decisions taken (16 September 2026):

**Structural descriptors — Tier 1 and Tier 3 only.**

- *Tier 1 (global shape):* helix/sheet/loop fractions, length-normalised radius of gyration, contact order, mean relative solvent accessibility and fraction buried, shape anisotropy, mean/min folding confidence, fraction of low-confidence residues.
- *Tier 3 (active-site geometry):* catalytic-constellation detection in 3D (e.g. serine-hydrolase S-x-x-K with Ser Oγ and Lys Nζ at catalytic distance in a solvent-exposed position); metal-binding site detection with coordinating-residue identity; cofactor-binding geometry (SAM-binding Rossmann, Walker A/P-loop, ATP-grasp).
- *Tier 2 (surface electrostatics, pocket descriptors, membrane propensity) was considered and declined*, as were genomic-context features. Consequences are recorded in §7.
- All-atom structures are required — side chains are needed for solvent accessibility and all catalytic geometry. Confidence weighting is mandatory: low-confidence regions produce meaningless geometry, and predicted side chains are less reliable than backbones, so Tier 3 carries more noise than Tier 1 by construction.

**Domain features (Pfam/InterPro).** Presence/absence and bit-score-weighted matrices via `hmmscan` against Pfam-A with `--cut_ga`. Used for accuracy assessment and as the sequence-profile homology comparator — **excluded from SHAP interpretability output**, because CARD family labels are frequently themselves domain-defined and including them would recover a tautology rather than a finding.

**Structural-alignment features.** Foldseek (with TM-align verification on top hits) against a reference panel of resistance-enzyme structures. **The panel must be external to the training data** — preferably experimentally solved PDB structures — since building it from the 6,059 CARD proteins reintroduces exactly the leakage §4.1 exists to remove. Where PDB coverage is too thin for a family, that family's representative must be built per CV fold from training-fold members only, never once globally.

**Ablation design.** Each block is added to the same base rather than stacked cumulatively, so contributions are isolated rather than confounded:

| Run | Feature set | Purpose |
|---|---|---|
| R1 | Base (Pfeature + ProFET) | Honest baseline under fixed CV |
| R2 | Base + structural descriptors | Interpretability model; the only one whose SHAP is published |
| R3 | Base + Pfam domains | Sequence-profile homology comparator |
| R4 | Base + structural alignment | Structure-based homology |
| R5 | All blocks | Maximum accuracy |

**R3 versus R4, stratified by identity to the training set, is the central experiment.** It asks the thesis question directly: does structural comparison retain discriminative power at identities where profile-HMM detection has already failed? Pooled averages will conceal the effect entirely, since most of the dataset sits at comfortable identity where every method works.

---

## 7. Biological boundary: what structure can and cannot reach

Resistance enzymes overwhelmingly do not use resistance-specific folds. They are recruited from ordinary cellular chemistry:

| Resistance family | Fold | Also used by |
|---|---|---|
| AAC acetyltransferases | GNAT | Histone acetyltransferases, hundreds of housekeeping enzymes |
| APH phosphotransferases | Bilobal kinase | Eukaryotic protein kinases |
| ANT nucleotidyltransferases | Polymerase β-like | Broad superfamily |
| VanA/VanB ligases | ATP-grasp | Housekeeping D-Ala-D-Ala ligase |
| 16S rRNA methyltransferases | Rossmann-like SAM-binding | Among the most widespread folds in biology |
| TetM/TetO | Translational GTPase | EF-G and relatives |
| TetA and relatives | MFS | One of the largest transporter superfamilies |

**The fold encodes "acetyltransferase", not "resistance."** What makes a protein a resistance determinant is substrate specificity and biological context — which residues line the pocket, what it modifies, what regulates it. This is precisely why Tier 3 (active-site geometry) rather than Tier 1 (global shape) carries the discriminative signal, and why the negative-class problem in §4.2 is severe rather than cosmetic.

Where structure *does* work: families with distinctive catalytic chemistry. β-lactamases are the strongest case and the largest block of CARD — metal-site detection separates metallo- from serine-β-lactamases decisively, and the serine classes retain the S-x-x-K nucleophile motif and shared architecture across classes A, C and D even below 20% pairwise identity, while class B occupies an entirely unrelated metallo-hydrolase fold.

### 7.1 Predicted performance on currently weak families

The five weakest families in the existing classifier are all generic-fold or context-defined, which is a coherent biological boundary rather than random underperformance:

| Family | Why it fails | Tier 1+3 expected to help? |
|---|---|---|
| LNU | Nucleotidyltransferase fold | **Likely** — catalytic Asp triad and nucleotide-binding geometry |
| 16S rRNA MTase (G1405) | SAM-MTase Rossmann, shared with thousands | **Partially** — detects "is a SAM-MTase"; separating *16S rRNA* MTases needs the basic RNA-binding surface (Tier 2, declined) |
| SMR efflux | 4-TM membrane protein, no catalytic pocket | **No** — discriminator is membrane character (Tier 2, declined) |
| vanS | Histidine kinase, architecturally generic | **No** — discriminator is operon context (declined) |
| Miscellaneous ABC-F | Ubiquitous ABC ATPase fold, *and a grab-bag category* | **No** — see §7.2 |

This expectation is stated in advance deliberately: it should be reported as a prediction confirmed or refuted, not discovered post hoc.

### 7.2 Grab-bag categories are label noise, not model failure

A family named *"Miscellaneous ABC-F subfamily ATP-binding cassette ribosomal protection proteins"* is a curation residual, not a coherent class. No feature set will cleanly classify a catch-all, because the category has no underlying property to detect. Others of the same kind include `OXA beta-lactamase without subfamily`, `ADC beta-lactamases pending classification for carbapenemase activity`, `gene modulating beta-lactam resistance`, and `antibiotic efflux regulatory protein`.

**Handling:** identify these both by name pattern and by within-family dispersion relative to between-family separation (to catch incoherent categories the name heuristic misses), report headline metrics both including and excluding them so the effect is auditable, and write the flagged set up as a standalone finding about CARD label granularity. This is a direct extension of the project's existing documented observation that CARD's `AMR Gene Family` is a mechanism label rather than an orthology group.

---

## 8. Validation strategy

No wet-lab capability is available, so every output is a **candidate**, never a confirmed resistance gene. Framing must be prediction and prioritisation throughout. The available evidence chain, in ascending order of strength:

1. **Applicability domain** — existing OOD machinery rejects queries too far from training distribution to score honestly.
2. **Catalytic-motif conservation** — does the candidate present the expected catalytic constellation in 3D (SxxK for serine β-lactamases, metal coordination for MBLs)?
3. **Structural alignment** — TM-score against solved reference structures of characterised resistance enzymes.
4. **Genomic context** — is the candidate on a plasmid, within an integron, adjacent to known ARGs? Weak evidence individually, strong in combination.
5. **Molecular docking** — binding of the relevant antibiotic substrate, following the approach in the `New_idea_paper` reference and reusing existing docking experience.
6. **Phenotype-discordance enrichment** — are candidates enriched among the 336 AST-characterised isolates that are resistant without a known genetic explanation? (§3.3)
7. **Mobility stratification** — the differentiating analysis: are cryptic candidates disproportionately carried on conjugative or mobilizable elements relative to the known resistome? This is the question no existing study has asked.

---

## 9. Staged sequence

| Stage | Work | Blocked by |
|---|---|---|
| 0 | **Identity-stratified de-risking experiment** (§5) | Nothing — run now |
| 1 | CV-grouping fix: identity clustering, threshold calibration, mmseqs2 cross-check | Stage 0 outcome |
| 2 | Re-run baseline under fixed CV; quantify prior leakage as a reportable delta | Stage 1 |
| 3 | Negative-class assembly; two-stage ARG/non-ARG design | Independent; can run in parallel |
| 4a | Pfam domain scan | Nothing — CPU-only, tooling and database already present |
| 4b | ESMFold structure prediction (Tier 1 + Tier 3 descriptors) | Biohub API key |
| 4c | Structural-alignment features against external PDB panel | Stage 4b |
| 5 | R1–R5 ablation; R3-vs-R4 stratified comparison; grab-bag audit | Stages 1–4 |
| 6 | Prospective screen of ESKAPE genomes; mobility stratification of candidates | Stages 3, 5 |
| 7 | Candidate validation chain (§8) | Stage 6 |
| 8 | Manuscript | Stage 7 |

Stages 0, 3 and 4a require no API access, no GPU and no new infrastructure, and can begin immediately.

---

## 10. Risks and falsification conditions

**What kills the premise outright:** no crossover in the identity-stratified experiment (§5). If feature-based classification does not overtake profile-HMM detection at low identity, the thesis is false for this dataset and the direction should be abandoned rather than rescued.

**Where reviewers will push:**

- *"These are not validated resistance genes."* Correct, and the framing must concede it in the abstract rather than under review.
- *"How does this differ from DeepARG, HMD-ARG and similar?"* Must be answered head-on. **Superseded by §13.8** — the closest prior work is not DeepARG but Ghosh et al. (bioRxiv 2025.07.03.663053), which already occupies the ESKAPE-scoped, domain-feature, multiscale ground. The surviving differentiators are narrower than "mobility integration and ESKAPE scoping"; see §13.8 for the corrected positioning.
- *"Your baseline is BLAST."* Avoided by benchmarking against profile-HMM (§4.3).
- *"Your CV leaks."* Avoided only by completing §4.1 first.
- *"Your model has never seen a non-ARG."* Avoided only by completing §4.2 before any genome screen.

**Standing limitation to state rather than defend:** all validation is cross-validation within CARD's existing diversity — sequences CARD already contains, held out and grouped so near-duplicates cannot leak. This is a reasonable proxy for generalisation to divergent sequences, but it is not equivalent to testing on a genuinely novel determinant discovered tomorrow with no CARD entry. There is no ground truth for that case until one exists and is characterised independently. Cross-validated performance is the best obtainable evidence now; it is not a substitute for prospective validation later.

**Circularity risk:** domain features must never enter the interpretability analysis (§6), and the structural-alignment reference panel must never be drawn from the training set (§6). Both failures would manufacture impressive results that mean nothing.

---

## 11. Candidate framing for the manuscript

> **Title:** Beyond the homology floor: structure-aware detection of divergent resistance determinants reveals unrecognised mobile resistance potential in ESKAPE pathogens

> **Abstract skeleton:** Surveillance of antimicrobial resistance depends on homology-based detection, in which genes are identified by similarity to curated references. This imposes an intrinsic detection floor: determinants that have diverged beyond the identity thresholds of tools such as RGI or AMRFinderPlus are excluded from the resulting resistome by construction rather than by chance. Because protein structure is conserved across far greater evolutionary distances than protein sequence, we asked whether classification based on structural descriptors, rather than alignment, can recover resistance gene family assignment below this floor. Using N curated CARD reference proteins, we evaluated held-out sequences stratified by maximum identity to the training set and compared performance against BLAST- and profile-HMM-based assignment. We then applied the resulting model across N ESKAPE genomes and, using MOB-suite reconstruction, determined the genomic location and mobility class of candidate divergent determinants. [Results.] These findings indicate that routine surveillance under-represents transmissible resistance potential, and that a measurable fraction of the unrecognised resistome is carried on conjugative elements.

The phrase carrying the most weight is *unrecognised mobile resistance potential* — novelty, mechanism, and appropriate caution in one construction.

---

## 12. Related documents

- `Project_Evaluation_Current_Status_and_Five_Month_Roadmap.md` — current project status; the ML track is flagged there as a bounded secondary study, which this roadmap develops into its own coherent direction rather than an appendix to the mobility paper.
- `Project_Summary_Hypothesis_Results_and_Next_Steps.md` — §4.4 documents the OXA within-family allelic radiation, the one case where domain identity gives no discriminative signal by construction and structural features must carry any explanatory weight. This is the named validation target for the R2 interpretability run.
- `Gene_Family_Alignment_and_Drug_Class_Discussion.md` — origin of the "mechanism label, not orthology group" observation that §7.2 extends.
- `README.md` — pipeline history and dated findings.
- `Papers/CARD_WORK/From sequence to signature Machine learning uncovers multiscale.pdf` — Ghosh et al., bioRxiv 2025.07.03.663053. **The closest prior work to this roadmap's direction**; source of the domain/HMM methodology in §13 and of the corrected positioning in §13.8.
- `ecoli_project_13_06/AMR_COG_Category_broad.ipynb` — existing `COG_CATEGORIES` dictionary and eggNOG-tabular parsing, reusable for §13.6.

---

## 13. Domain and HMM annotation methodology (added 2026-09-17)

Extracted from Ghosh et al. and adapted to this project's protein set. This section is the operational recipe for the Pfam/InterPro and HMM components referenced in §6, plus a correction to the competitive positioning in §10.

### 13.1 Source

**Ghosh, A., Brenner, E.P., Vang, C.K., Wolfe, E.P., Boyer, E.A., Lesiyon, R.L., Manpearl, K.R., Sridhar, V., Burke, J.T., Krol, J.D., Bilodeaux, J.M.R., Ravi, J.** *From sequence to signature: Machine learning uncovers multiscale feature landscapes that predict AMR across ESKAPE pathogens.* bioRxiv 2025.07.03.663053, posted 20 October 2025. Preprint, not peer-reviewed. Dashboard at jravilab.org/amr. Local copy: `Papers/CARD_WORK/From sequence to signature Machine learning uncovers multiscale.pdf`.

Their design: ~13,000 BV-BRC genomes with lab-derived AST phenotypes across all six ESKAPE pathogens (Kpn 5,120; Sau 3,612; Aba 1,710; Pae 1,323; Efa 1,106; *Enterobacter* spp. 499), 57 drugs, 25 drug classes. Interpretable logistic regression (glmnet/tidymodels), median nMCC 0.89.

Four molecular scales, each in both count and binary encodings:

| Scale | Tool | Matrices |
|---|---|---|
| Gene | Panaroo v1.5.2 (95% identity, 5% length tolerance; families at 50%) | `gene_count`, `gene_binary` |
| Structure | Panaroo gene-triplet forks (pangenome graph neighbourhood rearrangements) | `struct` binary |
| Protein | CD-HIT v4.8.1 (`-d 0`) | `protein_count`, `protein_binary` |
| Domain | **InterPro v5.74-105.0, Pfam/Xfam annotations** | `domain_count`, `domain_binary` |

Stored as columnar sparse Parquet, queried through DuckDB views to subset per drug — a storage pattern worth copying directly, since it is the clean answer to wide sparse matrices.

**Naming trap to avoid:** their "struct" scale is *gene-neighbourhood structure in the pangenome graph*, not protein 3D structure. There is no folding, and there are no structural descriptors of any kind, anywhere in that paper. The structural work proposed in §6 of this roadmap is orthogonal to it, not duplicative.

Two quantitative findings about the domain scale specifically. Domain models ran **~50× faster than gene-structure and ~43× faster than the protein features from which the domains were extracted**, with no performance penalty — no single scale dominated (median nMCC 0.89 binary vs 0.90 count). Their stated caveat: not all proteins carry annotated domains, so a domain-only approach silently misses resistance in unannotated proteins.

### 13.2 The structural difference that governs every adaptation

Their prediction target is **genome → AMR phenotype**, with labels from lab AST. Ours is **protein → CARD gene family**, with labels from CARD curation.

This is not cosmetic. Because their labels are phenotypic, they can honestly report that *"known ARGs constituted only a minor fraction of the top predictive features"* — the label never came from an ARG database, so no circularity exists. Our labels **are** the annotation, and CARD family labels are frequently domain-defined (§7). Pfam features predicting CARD family is therefore partly tautological.

This does not disqualify the feature block; it determines where it may be used. Per §6: domain features enter the accuracy track (R3) and are **excluded from the SHAP interpretability output (R2)**, enforced structurally by never merging them into R2's feature matrix rather than by filtering them afterwards.

### 13.3 Verified local environment

Checked directly on this machine, September 2026 — everything needed for layers 1 and 2 is already installed, with no downloads required.

| Requirement | Status |
|---|---|
| HMMER | **3.4 (Aug 2023)** across ten conda envs — exactly the version the paper cites |
| `hmmsearch` / `hmmscan` / `hmmpress` / `hmmstat` | all present |
| Pfam-A | **release 35.0, pressed and complete**, **19,632 models**, at `~/miniforge3/envs/antismash/lib/python3.12/site-packages/antismash/databases/pfam/35.0/Pfam-A.hmm` |
| NCBI ARG HMM catalogue | **already present** as AMRFinderPlus `AMR.LIB`, release **2024-12-18.1**, at `~/Pranjal-11-12-2025/bakta_database/bakta_db/db-light/amrfinderplus-db/2024-12-18.1/`. **Pressed binaries only — no plain-text `.hmm`** |
| InterProScan | not installed (deliberately not required — see 13.4) |
| `Pfam-A.clans.tsv` | **absent** — see 13.10 |
| COG data | no COG HMM set and no `cog-20` files locally; eggNOG-mapper (`emapper.py`, `hmm_mapper.py`) **is** installed |
| Input FASTA | `Protein_Individual_Records/card_protein_individual_records.fasta` — 6,059 records, 6,059 unique headers, clean 20-residue alphabet apart from 14 `X` characters in the entire set |
| Compute | 16 cores, 207 GB free. No GPU required for any layer. |

### 13.4 Layer 1 — Pfam domains as features

**Tool decision: HMMER + Pfam-A, not InterProScan.** The paper's predictive features were Pfam/Xfam specifically, so `hmmsearch` against Pfam-A reproduces the same feature space with zero installation. What is given up: InterPro accessions, GO terms, and the other thirteen member databases. These aid interpretation but are not required for feature construction, and InterProScan can be added later without invalidating anything built here, since Pfam accessions remain a subset of what it returns.

```bash
hmmsearch --cut_ga --cpu 16 \
  --domtblout pfam_domtbl.txt -o /dev/null \
  Pfam-A.hmm card_protein_individual_records.fasta
```

`--cut_ga` applies Pfam's curated per-family gathering thresholds instead of an arbitrary E-value. This is both the reproducible choice and what InterProScan applies internally, so it remains faithful to the paper's effective behaviour.

`hmmsearch` (models as queries against a small sequence set) is usually faster than `hmmscan` for this shape; benchmark both on a 100-protein subset and keep the faster.

**Parsing gotcha.** `--domtblout` column semantics are reversed between the two programs: with `hmmscan`, *target* is the Pfam model and *query* is the protein; with `hmmsearch` it is the opposite. Parsing the wrong way round yields a silently transposed matrix.

**Accession handling.** Strip the version suffix (`PF10417.12` → `PF10417`) for column names so the schema survives Pfam releases, but retain the versioned accession in the long table for provenance.

**Outputs**, all keyed by `aro_accession` via the existing `canonical_aro()` parser:

- `pfam_hits_long.csv` — ground truth, one row per domain instance: `aro_accession, pfam_acc, pfam_acc_versioned, pfam_name, clan, seq_evalue, seq_score, dom_ievalue, dom_score, hmm_from, hmm_to, ali_from, ali_to, env_from, env_to`
- `domain_binary.csv` — `aro_accession` × Pfam accession, 0/1
- `domain_count.csv` — same shape, integer copies *within that protein*

Both encodings, as the paper does. For single proteins rather than genomes the two differ only for repeat-containing families; pentapeptide-repeat Qnr proteins are the obvious case here, and one the paper itself surfaces (`penta_rpt_QnrA/QnrB/QnrVC`, its Figure 7C). That makes a convenient built-in sanity check: Qnr proteins should show count > 1 with binary = 1.

**Column filtering.** Drop Pfam families occurring in fewer than two proteins — singletons carry no classification signal and only widen an already sparse block. Keep the long table unfiltered so the threshold remains a tunable downstream parameter.

### 13.5 Layer 2 — NCBI Reference HMM Catalogue of ARGs

The paper maps its top features to this catalogue with HMMER v3.4 to verify recovery of known determinants. Applied to our protein set it does something more valuable: **it supplies an ARG annotation that does not derive from CARD.** Agreement between a protein's CARD family label and its best NCBI ARG-HMM hit is genuine cross-database corroboration; disagreement flags a curation discrepancy worth inspecting. It also functions as a second, purpose-built homology comparator alongside Pfam for the R3 baseline — arguably the stronger of the two, since these models are built specifically for ARG detection.

**No download needed.** The catalogue is already on disk as the AMRFinderPlus HMM library bundled with the bakta database, release **2024-12-18.1** — substantially more current than the Pfam 35.0 copy, so record both release strings in output metadata.

Because it is stored as pressed binaries with no plain-text `.hmm`, this layer **must** use `hmmscan` (which reads the pressed index); `hmmsearch` requires the text file and will fail.

```bash
hmmscan --cut_ga --cpu 16 \
  --domtblout amr_hmm_domtbl.txt -o /dev/null \
  <amrfinderplus-db>/2024-12-18.1/AMR.LIB card_protein_individual_records.fasta
```

Run `hmmstat` on the library first to record its model count for the methods section. Outputs: `ncbi_arg_hmm_hits.csv` keyed by `aro_accession`, plus a CARD-versus-NCBI reconciliation table.

### 13.6 Layer 3 — COG assignment

The paper's rationale, quoted because it is the crux:

> *"While Pfam domains can be applied readily for cross-species analysis, comparing wildly variable gene or protein names can introduce problems even within a single species... By assigning gene or protein clusters to COGs, we bridge phylogenetic distance and find readily comparable features across scales."*

Pfam accessions are already species-agnostic, so within-CARD family classification gains little directly. The value is downstream — this is the layer that makes features comparable once the work extends to the cross-species genome screen (§9, Stage 6), and it allows top features to be grouped into functional categories for interpretation.

**Existing precedent in this workspace to reuse.** `ecoli_project_13_06/AMR_COG_Category_broad.ipynb` already performs COG-category analysis built around eggNOG-mapper output: it reads a `.tabular` annotations file, parses the `COG_category` column, and maps single-letter codes through a `COG_CATEGORIES` dictionary into broad functional groups. That dictionary and its plotting code are directly reusable; only the input changes.

No COG HMM set is present locally, so the route is eggNOG-mapper rather than raw COG HMMs. In order of practicality:

1. **Galaxy eggNOG Mapper** — the source of the prior notebook's input (`Galaxy9-[eggNOG Mapper on dataset 7_ annotations].tabular`). Upload the 6,059-protein FASTA, retrieve the annotations tabular, feed it to the existing parser. No installation, matches established precedent, and 6,059 sequences is a trivial load.
2. **Local `emapper.py`** — installed in the `eggnog` env, but requires downloading the eggNOG database first (`download_eggnog_data.py` is present).
3. **Raw COG HMMs via `hmmscan`** — closest to the paper's literal method, but the COG HMM set must be sourced.

**Sensitivity caveat.** The paper's stated purpose for this layer was *distant* homology detection via HMMs. eggNOG-mapper defaults to DIAMOND-based seed orthology search, which is faster but less sensitive at long evolutionary distance than the HMM approach the paper used. For functional-category summarisation the default is fine; for distant-homology bridging the difference is real, and `hmm_mapper.py` is available if it matters.

### 13.7 Rigor controls worth importing

Three controls from the paper, each cheap and each stronger than anything currently in this project's pipeline:

- **Label shuffling as a measured null.** Retrain on randomly shuffled labels. Their expected nMCC of 0.50 against an observed median of 0.49 is what makes every other number in the paper interpretable. This is a better control than any currently in use here.
- **Fisher's exact test baseline** — feature-wise, two-sided, Benjamini-Hochberg corrected at Q = 0.05, on the binary matrix. Demonstrates what joint modelling contributes over per-feature enrichment; they recovered features Fisher calls insignificant (PF00533, PF05144) precisely because logistic regression evaluates features jointly.
- **Iterative feature elimination** — remove the top 10% of features, retrain, repeat to 80% removed. Performance declined *gradually* rather than sharply, evidence that AMR signal is distributed across hundreds of features rather than concentrated in single ARGs. Note they fixed regularisation to **ridge** for this, because lasso collapses correlated clusters into single representatives and manufactures apparent robustness.

Also consider adopting **nMCC** as the headline metric. Its normalisation (0.5 = random, 1.0 = perfect) makes the shuffled-label null directly comparable, which raw AUC or average precision do not.

Their geographic and temporal holdout designs are directly available to us: `ALL_ESKAPE_Clinical_metadata.xlsx` carries both country and collection date. Their temporal result is instructive — aminoglycoside acetyltransferases move from one-of-hundreds with flat importance (2000–2004) to top-two at a 1.3–2× margin (2010–2014) to top-two at ~8× (2015–2019), tracking the fixation of a mechanism over two decades.

### 13.8 Competitive positioning — correction to §10

**This paper, not DeepARG, is the closest prior work to the direction in this roadmap**, and must be cited and distinguished as such.

It already occupies: ESKAPE scoping, InterPro/Pfam domain features, multiscale feature comparison, geographic and temporal holdout validation, an interactive dashboard — and it independently finds **MGE-associated features (transposases, integrases, plasmid replication proteins, prophage) dominating top predictors**, which brushes directly against the mobility angle in §1.3.

What it does **not** do, and where our differentiators survive:

- **No protein structural features of any kind.** No folding, no 3D descriptors. Their "struct" is pangenome gene-neighbourhood structure.
- **No formal mobility-class stratification.** They observe MGE enrichment qualitatively; they do not classify features by MOB-suite conjugative / mobilizable / non-mobilizable status.
- **No identity-stratified evaluation.** They never ask whether performance holds at low sequence identity — which is the entire crux of the premise in §1.1–1.2 and the de-risking experiment in §5.
- **No applicability-domain or OOD framework.** Our newest notebook already has one.

The unclaimed space is therefore narrower and sharper than the earlier framing of "ESKAPE-scoped, mobility-integrated": **structure-aware, identity-stratified detection of divergent determinants with formal mobility-class stratification.** That remains genuinely ours, but the abstract and introduction must position against this paper explicitly rather than against the general ML-for-ARG literature.

### 13.9 Cautionary findings to inherit

Two of their honest self-critiques apply directly to our setting:

- **Imbalance can manufacture perfect scores.** Vancomycin resistance appeared in only 3% of their *S. aureus* isolates, yet models returned nMCC ≈ 1.00 — with top features including thymidylate synthase, dihydrofolate reductase and a DegV-family protein, i.e. clonal artifacts of a few related resistant isolates rather than mechanism. They state this plainly. Our §7.2 grab-bag audit and any small-family result needs the same scepticism.
- **Presence/absence features are blind to SNP-driven resistance.** Fluoroquinolone (*gyrA*/*gyrB*), rifampin (*rpoB*) and trimethoprim (*dfrB*) resistance arise from point mutations; their rifampin *S. aureus* model sat at random baseline, exactly as expected. Any claim our framework makes about such drug classes must carry the same caveat.

A third observation is useful rather than cautionary: several of their top features act as **effective proxies without being causative** — *aac(6')-Ib-cr* acting on both aminoglycosides and fluoroquinolones, and *bla* genes co-propagating with *qnr* on shared mobile elements. Feature importance in this domain frequently reflects genomic linkage rather than mechanism.

### 13.10 Known limitations and open items

- **Missing `Pfam-A.clans.tsv`.** Without it, multiple families from the same Pfam clan hitting the same region are all retained, producing correlated redundant columns that widen the feature space and muddy SHAP attribution. `pfam_scan.pl` or post-processing against the clans file resolves this; the file is a small download. Fix it or state it explicitly in the methods.
- **Pfam 35.0 is from November 2021.** It runs today with no download and is fine for development, but the paper used InterPro 5.74-105.0, which bundles a considerably newer Pfam. Re-run against a pinned current release before publication, and record the release string in output metadata either way.
- **`SEQUENCE_CLUSTER_CSV` remains `None`** (§4.1). Adding a wide sparse binary block changes feature-space geometry and degrades the `radius_neighbors_graph(radius=1.0)` CV-grouping proxy, letting near-duplicates leak across folds. **The identity-based clustering must be populated before this feature block is evaluated**, or the resulting comparison means nothing.
- **Domain-only approaches miss unannotated proteins**, per the paper's own caveat. Report what fraction of the 6,059 proteins receive no Pfam annotation at all — that figure bounds the coverage of this entire feature block.

### 13.11 Output location and integration

New directory: `CARD_DATA/card-data/refined_details/Final_Results/InterPro_Pfam_Domains/`.

Merge point is `load_drug_training_data()` in `ARG_family_drug_class_SHAP_comparison.ipynb`, which already dedupes to one row per `aro_accession` and handles multi-family membership as multi-label `Y` matrices — so a plain left-join onto `meta["aro_accession"]` suffices, with no fan-out logic. Alongside it: add an `"InterPro_Pfam"` tag to `feature_sources`, move `EXPECTED_FEATURE_COUNT` from 996 to 996 + n_domain_columns for the R3 variant, and record a sorted-feature-name hash in run metadata so schema drift is caught even if the count constant is overlooked.

21-09-2026: 
The notebook protein_annotation_physicochemical_integration.ipynb connects your existing sequence-derived protein features with biological annotation evidence from
  InterPro/Pfam and UniProt.

  Its main jobs are:

  1. Reconcile the CARD protein dataset
      - Matches each protein sequence, its ARO identifier, ARG family, drug class, mechanism, and existing feature rows.
      - Checks that the expected 6,059 proteins are represented consistently.

  2. Run InterProScan locally
      - Scans each CARD protein for Pfam, Gene3D, SUPERFAMILY, PROSITE, and related signatures.
      - Produces protein × annotation matrices: for example, whether a protein contains a given Pfam domain.

  3. Retrieve UniProt metadata
      - Uses protein accessions in CARD to retrieve available UniProt records.
      - Captures evidence tier, reviewed/unreviewed status, protein name, GO terms, topology/site information, and cross-references.
      - Exact sequence matching prevents an accession from being assigned to the wrong CARD protein.

  4. Create sequence-similarity groups
      - Uses MMseqs2 to cluster proteins at 40% sequence identity and 80% coverage.
      - These groups reduce the risk of reporting associations caused only by many nearly identical homologues.

  5. Associate physicochemical features with annotations
      - Tests whether a Pfeature/ProFET descriptor differs between proteins with and without an annotation.
      - Reports effect size (Cliff’s delta), FDR-adjusted significance, and length-adjusted/group-aware results.
      - Example interpretation: “Proteins containing a particular Pfam domain have consistently higher hydrophobicity-related descriptor values.”

  6. Compare prediction models
      - Evaluates five feature sets for ARG family and drug class prediction:
          - Existing Pfeature + ProFET descriptors
          - InterPro/Pfam annotation features
          - Generic UniProt numeric evidence
          - Combined annotation features
          - All features together

      - Uses sequence-grouped cross-validation, so closely related proteins do not inflate performance.
      - Includes label-shuffled controls to check whether results exceed chance.

  7. Generate tables, figures, and a results draft
      - Association tables, model-performance tables, heatmaps, and optional links to your previous SHAP-safe feature table.
      - Results are written under:

  CARD_DATA/card-data/refined_details/Final_Results/
  Protein_Annotation_Physicochemical_Association/

  The required inputs already configured are:

   Input                                        Purpose
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   protein_fasta_protein_homolog_model.fasta    The 6,059 CARD protein sequences for InterProScan and clustering
  ───────────────────────────────────────────  ───────────────────────────────────────────────────────────────────────────────
   all_families_features.csv                    Pfeature descriptors
  ───────────────────────────────────────────  ───────────────────────────────────────────────────────────────────────────────
   all_families_profet_features.csv             ProFET physicochemical descriptors
  ───────────────────────────────────────────  ───────────────────────────────────────────────────────────────────────────────
   aro_index.tsv                                ARO IDs, ARG family labels, drug-class labels, mechanism data, and accessions
  ───────────────────────────────────────────  ───────────────────────────────────────────────────────────────────────────────
   all_targets_top_features_shap_safe.csv       Optional bridge to compare prior SHAP-selected family/drug-class features
  ───────────────────────────────────────────  ───────────────────────────────────────────────────────────────────────────────
   InterProScan installation                    Generates domain and signature annotations
  ───────────────────────────────────────────  ───────────────────────────────────────────────────────────────────────────────
   UniProt REST API                             Retrieves available curated protein metadata
  ───────────────────────────────────────────  ───────────────────────────────────────────────────────────────────────────────
   MMseqs2                                      Builds sequence-similarity groups

  Initially, every computational stage is disabled. You enable one stage at a time using RUN_INTERPROSCAN, FETCH_UNIPROT, RUN_MMSEQS, RUN_ASSOCIATIONS, RUN_ABLATIONS,
  and WRITE_RESULTS_DRAFT.

