# AB (Acinetobacter baumannii) — MOB-suite Contig/Plasmid Metrics, Explained

Notes on the section 3.1 (AB) output from `Mobsuite_Chromosome_Plasmid_Mobility.ipynb`. Figures below were verified directly against the raw files (`Results/AB_Mob_Results/summary/master_contig_report.tsv` and `master_mobtyper_results.tsv`), not just the notebook's printed output.

## Raw numbers

```
AB: 2714 contigs, 1234 assembled plasmids

molecule_type value_counts:
plasmid       1401
chromosome    1313
```

967 total AB genomes underlie these numbers.

## Why "2,714 contigs" and "1,234 assembled plasmids" are different units

- **2,714 contigs** = every row in `master_contig_report.tsv` — every assembled DNA fragment across all 967 AB genomes. `1,313 chromosome + 1,401 plasmid = 2,714`. ✓
- **1,234 assembled plasmids** = every row in `master_mobtyper_results.tsv`, which is coarser: MOB-suite groups multiple contigs into one plasmid when they share the same replicon (`primary_cluster_id` within a genome), because a plasmid doesn't always assemble into a single clean circular contig.

## Why 1,401 plasmid *contigs* ≠ 1,234 assembled *plasmids*: fragmentation

Of the 1,234 distinct plasmids:

| # contigs per plasmid | # plasmids |
|---|---|
| 1 | 1,077 |
| 2 | 149 |
| 3 | 7 |
| 5 | 1 |

Check: `1077×1 + 149×2 + 7×3 + 1×5 = 1401` — exactly matches the plasmid-contig count.

So ~13% of AB's plasmids didn't assemble into a single piece — normal for draft/WGS genomes, where a plasmid's sequence gets broken across more than one contig by the assembler.

## Other relevant context (not in the printed line, but explains the totals)

- Only **768 of the 967** AB genomes have any plasmid at all (distinct `pipeline_sample_id` count in mobtyper). The other **199 genomes contributed 0 plasmids** — which is why plasmid-contig count isn't simply "≈1 per genome."
- Chromosome fragmentation, same story: **791 genomes** have their chromosome as a single clean contig, but **176 genomes** have it split across 2+ contigs — one outlier genome has its chromosome in **46 pieces** (a very fragmented draft assembly). This is why 1,313 chromosome contigs comes out to more than 967 (one per genome).

## Takeaway

The gap between "contig count" and "assembled plasmid count" is a real, checkable signal of assembly completeness/fragmentation — not a bug or inconsistency in the pipeline.

## What `molecule_type` (chromosome vs. plasmid) is actually classified on — `mob_recon`

This is a **sequence-comparison-based classification, not a size cutoff**. `mob_recon` compares every contig against MOB-suite's curated database of thousands of known reference plasmids (using Mash, a fast genome-sketch similarity tool), plus checks for plasmid-specific marker genes. Checked directly against AB's contig table:

| Evidence | Chromosome contigs | Plasmid contigs |
|---|---|---|
| Has a Mash hit to a known reference plasmid | **0%** (1/1,313) | **100%** (1,401/1,401) |
| Has a recognized plasmid replicon (`rep_type`) gene | 35% | 80% |
| Median contig size | 3.93 Mb | 19.7 kb |

The Mash comparison is essentially the deciding factor — every plasmid-labeled contig matched something in the reference plasmid database; almost no chromosome contig did. The replicon gene (which a plasmid uses to control its own copy number/replication — typed families like IncF, IncH, IncN) is corroborating evidence. Size is a real, strong pattern (plasmids are ~200× smaller on average here) but not the actual rule — it's a consequence of what plasmids biologically are, not the first thing `mob_recon` checks. Any contig that doesn't match a known plasmid gets classified chromosome by default.

## What `predicted_mobility` (conjugative / mobilizable / non-mobilizable) is actually classified on — `mob_typer`

This only applies *after* a contig is already called plasmid, and depends on which of two transfer-machinery genes that assembled plasmid carries:

- **Relaxase** — the enzyme that nicks the plasmid at its origin of transfer (oriT) to kick off conjugation.
- **MPF (mating pair formation) / T4SS** — the type IV secretion system that physically builds the conjugation channel between donor and recipient cells.

Checked directly against AB's 1,234 assembled plasmids:

| Class | n | Has relaxase | Has MPF |
|---|---|---|---|
| conjugative | 430 | 100% | 100% |
| mobilizable | 388 | 100% | 0% |
| non-mobilizable | 416 | 0% | 6% |

The rule is exactly what it looks like: **relaxase + MPF → conjugative** (can transfer itself, fully self-sufficient); **relaxase, no MPF → mobilizable** (can nick and package itself for transfer, but needs another plasmid's MPF machinery riding along in the same cell to actually move); **no relaxase → non-mobilizable** (can't even initiate transfer — without the enzyme to nick DNA at oriT, having MPF or not is moot, which is why the handful of non-mobilizable plasmids with a stray MPF signature still land in this bucket).

## What `primary_cluster_id` actually is

It's a **plasmid family/lineage label**, not a unique ID per physical plasmid instance.

MOB-suite maintains its own curated database of thousands of known, previously-sequenced reference plasmids from GenBank, pre-grouped into clusters by sequence similarity (built by a companion tool, MOB-cluster). When `mob_recon` finds a plasmid contig, it compares it (via Mash) against that reference database and assigns it to whichever cluster its closest matches belong to — so `primary_cluster_id` answers "which known plasmid family/type is this," not "which specific physical plasmid instance is this."

That's why cluster `AC163` shows up in **141 different, unrelated AB genomes**: not the same plasmid double-counted, but 141 different strains each independently carrying a plasmid belonging to that same widespread family — a real, expected pattern, since certain plasmid backbones circulate broadly across many strains via horizontal transfer.

**Evidence it's a "family," not one single reference plasmid** — inside cluster `AC163` alone, across 6 of its contigs:

```
mash_nearest_neighbor: NC_021489, MG954378, MK431775, MK431775, MK431775, MK386683
```

Four *different* individual reference-plasmid accessions all fall under the same `AC163` cluster label. Each contig gets matched to whichever single reference plasmid it resembles most, but several distinct references can belong to the same broader cluster — that's the family grouping in action. `rep_type(s)` (the replicon/copy-number-control gene, `rep_cluster_1172` here) staying consistent across most of these rows is supporting evidence: related plasmids in the same family tend to share the same replication machinery.

**Two-level hierarchy — `primary_cluster_id` vs. `secondary_cluster_id`:** cluster `AC163` (one primary cluster) actually splits into **4 distinct `secondary_cluster_id` values** (`AL239`, `AL240`, `AL241`, `AL243`). Across all of AB's 103 primary clusters, **29 split into more than one secondary cluster** — a real two-level structure: `primary_cluster_id` = broad plasmid family, `secondary_cluster_id` = a finer sub-lineage/variant within that family (roughly analogous to genus vs. species).

This is also exactly why the join needed `sample_id` alongside `primary_cluster_id`: many unrelated genomes legitimately carry the same plasmid lineage, so the cluster ID alone was never enough to identify one specific plasmid.

## Conjugative vs. non-conjugative plasmids, and does conjugative imply mobilizable?

**Conjugative plasmid**: carries the complete machinery to transfer itself into another cell on its own — both the **relaxase** (nicks the plasmid at its origin of transfer, oriT, to prep it for transfer) and a complete **MPF/T4SS system** (the mating-pair-formation machinery that physically builds the conjugation channel/pilus between donor and recipient). Self-sufficient — needs nothing from another plasmid.

**Non-conjugative** isn't one thing — it splits into two very different situations, which is exactly where MOB-suite's three-way scheme comes from:
- **Mobilizable**: has the relaxase (can nick itself and prep for transfer) but lacks the MPF/channel-building machinery. Transfer-ready but stuck, unless a co-resident conjugative plasmid in the same cell lends it its MPF apparatus — then it can hitch a ride through that borrowed channel.
- **Non-mobilizable**: lacks the relaxase entirely. No nicking enzyme means it can't even initiate transfer at oriT, regardless of what other plasmids share the cell.

**Are conjugative plasmids always mobilizable?** Depends which sense of "mobilizable" is meant — this is a genuine terminology overlap:

- **Broad/plain-English sense** — "mobilizable" meaning "capable of moving between cells via conjugation, one way or another" — yes, conjugative plasmids are trivially mobilizable: the *most* mobile category, being fully self-sufficient. In this sense "conjugative" is really a subset of "mobile plasmids."
- **MOB-suite's `predicted_mobility` field** — the three labels are **mutually exclusive by construction**; every plasmid gets exactly one. Here "mobilizable" is used in the *narrow* technical sense: "has a relaxase but no MPF — needs external help." Under this narrow definition, a conjugative plasmid is *not* labeled "mobilizable," precisely because it doesn't need that help — it gets its own distinct label instead.

So the tool isn't claiming conjugative plasmids are immobile or excluded from being mobile — it's using "mobilizable" as a specific technical term for the "relaxase-but-no-MPF, dependent-on-a-helper" case, and giving self-sufficient plasmids their own separate label rather than nesting them under it. That's why the relationship "isn't mentioned" — in this tool's vocabulary, `conjugative` / `mobilizable` / `non-mobilizable` are deliberately treated as separate, non-overlapping buckets, not a hierarchy.

# Research Gap Analysis and Study Design

Six papers in `Papers/ESKAPE-AMR/` were read in full (via background agents) to find a research gap this pipeline (RGI/CARD + MOB-suite across all six ESKAPE pathogens, ~9,947 genomes) could address.

## 1. Literature synthesis table

| Paper | ARG tool | Plasmid/mobility tool | Scope |
|---|---|---|---|
| ARKbase (*Nucleic Acids Research*, 2026) | RGI + ResFinder + AMRFinderPlus + ABRicate (parallel) | **None** | 14 WHO priority pathogens (database resource), human-host only |
| Che et al. (*PNAS*, 2021) | Not RGI/CARD | Custom tool "Plascad" (conjugative/mobilizable/non-mobilizable, validated vs. MOB-typer) | Broad multi-genus (14,029 plasmids, 12,059 genomes); ESKAPE is a minor subset |
| Liu & Liu (*mSystems*, 2026) | **RGI v6 + CARD v4.0.1** | **MOB-suite v3.1.9** — `mob_recon` only (replicon typing/clustering) | Enterobacteriaceae only (159 species, 6,895 genomes) — just K. pneumoniae overlaps with ESKAPE |
| Aggarwal et al. (*BBRC*, 2026) | **RGI/CARD** | None (plasmid links only cited from literature) | 3 pathogens (A. baumannii, K. pneumoniae + non-ESKAPE E. coli), India, 871 isolates |
| Orlek et al. (*Scientific Reports*, 2023) | Not RGI/CARD (ResFinder) | CONJscan (coarser conjugative call) | Broad NCBI plasmid survey (14,143 plasmids), not ESKAPE-specific, **plasmid-only** (no paired chromosome) |
| Liu & Han (*IJMS*, 2026) | **RGI v6 + CARD v4.0.1** | **MOB-suite v3.1.9** — `mob_recon` only again | P. aeruginosa only (10,412 genomes), single organism despite citing ESKAPE as motivation |

## 2. The identified gap

Two papers use the exact core toolchain this pipeline uses (RGI/CARD + MOB-suite) — and both stop at `mob_recon` (plasmid reconstruction, replicon typing). **Neither uses `mob_typer`'s conjugative/mobilizable/non-mobilizable call.** Every paper here is either single-organism, single-family, or broad-and-ESKAPE-agnostic. **No paper analyzes all six ESKAPE pathogens together with the full RGI/CARD + MOB-suite (`mob_recon` + `mob_typer`) pipeline, including three-way mobility classification.** This dataset (~9,947 genomes) is larger than four of these six studies' genome counts.

Supporting threads from the papers:
- **Aggarwal et al.** found genotype-phenotype discordance (resistance genes present in susceptible isolates), only partly explained (missing upstream regulatory elements) — never tested genomic location as a factor, since they had no plasmid/mobility data at all.
- **Orlek et al.** explicitly flagged "chromosome-plasmid interactions not captured" as a limitation of their plasmid-only design.
- **Che et al.** found ARGs specifically enriched on *conjugative* plasmids (not plasmids generally), and that IS elements bridge ARGs between conjugative plasmids across huge phylogenetic distances — tested on a broad multi-genus set, not a defined ESKAPE panel.

## 3. Finalized study design

- **(A) Mobility risk profile**: for every ARG gene family, % of occurrences that are chromosomal / non-mobilizable / mobilizable / conjugative, across all six pathogens.
- **(B) Cross-pathogen sharing test**: do conjugative-plasmid-borne gene families show more cross-ESKAPE-genus presence than non-mobilizable/chromosomal ones? — a direct, ESKAPE-scoped extension of Che et al.'s finding.

## 4. Selection-pressure layer: dN/dS by mobility class

For gene families with enough copies (≥15–20 per mobility-class subgroup is a reasonable floor; NDM/KPC/OXA-48-like/CTX-M are good first candidates):

1. **Extract sequences**: RGI's `.txt` output already has `Predicted_DNA` per hit — no new sequencing/annotation needed, just extraction, grouped by gene family and tagged with mobility class.
2. **Align**: codon-aware MSA per gene family (MAFFT), keeping all mobility-class subgroups in one alignment so sites stay comparable across groups.
3. **Tree**: a quick gene tree from the alignment (FastTree/IQ-TREE) for the branch/site-based tests below.
4. **Selection test**: HyPhy over classical pairwise dN/dS (PAML `codeml` M0), since it tests *per-branch*/*per-site* selection rather than one genome-wide number:
   - **FUBAR** (site-level): which codon sites are under pervasive diversifying vs. purifying selection, per mobility-class subgroup.
   - **aBSREL** (branch-level): tests each tree branch independently for episodic diversifying selection — lets you directly ask whether conjugative-plasmid branches are enriched for selection episodes vs. chromosomal branches.
5. **Compare across groups**: statistical comparison (Mann-Whitney on per-branch dN/dS, or a proportion test on positively-selected-site counts) between mobility classes, for the same gene family.

The installed `phylogenetics` scientific-agent-skill covers tree-building conventions; no installed skill wraps HyPhy/PAML directly.

## 5. mob_typer classification dimensions for ARG gene family profiling

- **Mobility mechanism**: `predicted_mobility` (conjugative/mobilizable/non-mobilizable), `relaxase_type(s)` (MOBF/MOBH/MOBP/MOBQ/MOBC/MOBV), `mpf_type` (MPF_F/MPF_T/MPF_I/MPF_G/MPF_B), `orit_type(s)`.
- **Plasmid identity/lineage**: `primary_cluster_id`/`secondary_cluster_id` (single lineage → clonal spread, vs. many lineages → repeated independent acquisition), `rep_type(s)` (backbone/Inc-group concentration), `mash_nearest_neighbor`/`mash_neighbor_distance` (well-characterized vs. novel/divergent plasmid).
- **Host range**: three independent evidence sources — `predicted_host_range_overall_*`, `observed_host_range_ncbi_*`, `reported_host_range_lit_*` (each with rank + name) — plus `associated_pmid(s)` (documented outbreak/dissemination history).
- **Structural characteristics**: `size`, `gc` (divergent GC = signature of recent horizontal acquisition), `circularity_status`, `repetitive_dna_id`/`repetitive_dna_type`.
- **Derived (built from joined data, not a single column)**: cross-pathogen spread (direction B), co-occurring cargo on the same plasmid (via `primary_cluster_id` grouping — hitchhiking/co-selection), copy number per genome.

## 6. Additional plasmid classification frameworks beyond mob_typer's own output

- **Narrow- vs. broad-host-range (NHR/BHR)**: classical ecological classification — certain replicon families (IncP-1/IncQ/IncW/IncN) are known generalist backbones; others are genus-restricted. Derivable natively from `predicted_host_range_overall_rank`/`observed_host_range_ncbi_rank`/`reported_host_range_lit_rank` (a `family`/`class`-rank value is already a broad-host-range signal), or via `rep_type(s)` cross-referenced against a known BHR-replicon list.
- **Multireplicon/hybrid plasmids**: `rep_type(s)` already returns comma-separated multiple values on a single plasmid (e.g. `IncC,IncFIB,IncHI1B,IncU`, seen in an earlier Kp example) — worth a derived single-vs-multireplicon flag; multireplicon plasmids are increasingly recognized as particularly effective AMR vehicles.
- **Classical size-class categories**: small/high-copy/non-self-transmissible (typically <10 kb, ColE1-like) vs. large/low-copy/conjugative (typically >30–50 kb) — a coarser, classical version of the quantitative `size` column.
- **PTU (Plasmid Taxonomic Units)**: a separately published classification (Redondo-Salvo et al., COPLA/PLSDB) based on whole-plasmid identity clustering — conceptually similar to but distinct from MOB-suite's own `primary_cluster_id`. Cross-referencing via `mash_nearest_neighbor` or the cluster IDs would give external validation against a taxonomy the wider field already uses.
- **Epidemic/high-risk lineage flagging**: named plasmid lineages individually tracked in the AMR literature as major global disseminators (e.g., IncX3 carrying *bla*NDM, IncA/C2 carrying multiple carbapenemases). Cross-reference `primary_cluster_id`/`mash_nearest_neighbor` against a curated list; `associated_pmid(s)` alone already gives a native, no-extra-data signal of "this lineage has documented history."

## 7. Column-mapping table

| Classification | Column(s) used | Needs external reference? |
|---|---|---|
| Narrow- vs. broad-host-range (NHR/BHR) | `rep_type(s)` (vs. external BHR list), **or** natively: `predicted_host_range_overall_rank`/`observed_host_range_ncbi_rank`/`reported_host_range_lit_rank` | `rep_type(s)` route: yes. Host-range-rank route: **no** |
| Multireplicon / hybrid plasmid | `rep_type(s)` — comma-separated value check | No |
| Size-class (small mobilizable vs. large conjugative) | `size`, optionally cross-checked against `predicted_mobility` | No |
| PTU (Plasmid Taxonomic Units) | `mash_nearest_neighbor` or `primary_cluster_id`/`secondary_cluster_id` as lookup key into COPLA/PLSDB | **Yes** — external database |
| Epidemic/high-risk lineage flagging | `primary_cluster_id`/`secondary_cluster_id`/`mash_nearest_neighbor` vs. a curated named-lineage list; `associated_pmid(s)` alone needs nothing extra | Partially — `associated_pmid(s)` native; curated list is external |

Three of the five extra classifications (NHR/BHR via host-range ranks, multireplicon, size-class) need **zero new data sources** — they're already in `master_mobtyper_results.tsv`. Only PTU cross-referencing and a curated epidemic-lineage list require an external source.

## 8. Project presentation: objective, hypotheses, and framing

Written using the scientific-critical-thinking skill's claim-evaluation principles (specific/falsifiable questions, proportional confidence, correlation vs. causation kept explicit) rather than as a narrative summary.

### Background

AMR genes don't all pose the same threat. A gene's location in the genome -- chromosome vs. plasmid -- and, if on a plasmid, whether that plasmid can conjugate into other cells, determines whether it can spread between strains and species. Knowing *which* resistance genes an ESKAPE pathogen carries is only half the picture; knowing *how mobile* those genes are is what predicts future spread.

### Gap (from the six-paper literature review, section 2 above)

1. **Tool-completeness gap**: studies using this project's exact toolchain (RGI/CARD + MOB-suite) stop at `mob_recon` and never use `mob_typer`'s conjugative/mobilizable/non-mobilizable classification.
2. **Scope gap**: no existing study analyzes all six ESKAPE pathogens together with the full pipeline -- every paper reviewed is single-organism, single-family, or broad-and-ESKAPE-agnostic.

### Central objective (one falsifiable question)

> **Does an AMR gene family's genomic mobility classification predict its cross-pathogen distribution and the selection pressure acting on it, across the six ESKAPE pathogens?**

Deliberately phrased as *predicts*, not *causes* -- an associational question the data can actually answer, not a causal claim a cross-sectional genomic dataset (no manipulation, no time series) can support.

### Three sub-hypotheses, each independently falsifiable

**Layer A -- foundational, not a hypothesis test.** A systematic mobility-risk profile (% chromosomal / non-mobilizable / mobilizable / conjugative per gene family, per pathogen) is descriptive infrastructure, not a claim to falsify. It's what B and C are tested against.

**Layer B -- associational hypothesis.**
- H_B: gene families predominantly carried on conjugative plasmids show significantly greater cross-ESKAPE-genus presence than families that are predominantly chromosomal or non-mobilizable.
- Testable (e.g. Kruskal-Wallis across the four mobility classes on genus-count, with effect size) and falsifiable: fails if cross-genus presence doesn't differ significantly by mobility class.
- A direct, ESKAPE-scoped extension of Che et al. (PNAS, 2021), which showed ARG enrichment on conjugative plasmids broadly, not within a defined ESKAPE panel.

**Layer C -- associational, mechanistic hypothesis.**
- H_C: within a gene family, lineages associated with conjugative plasmids show a different selection regime (branch-site dN/dS) than chromosomal/non-mobilizable lineages of the same gene.
- Falsifiable via the branch-site likelihood-ratio test; fails if no significant difference emerges.
- **Confound to disclose up front, not concede later**: mobility class correlates with recency of acquisition, and recency alone (independent of *being* mobile) can elevate dN/dS. The defensible claim is "selection regime differs by mobility class," not "mobility causes the difference," unless recency is controlled for or explicitly flagged as an alternative explanation.

### What the pilot work already shows (evidence, not conclusions)

- The gene-family distribution heatmap across all six pathogens is Layer A's foundation, already built.
- The beta-lactamase phylogenetic pilot found tree topology is explained almost entirely by enzyme class (Ambler class needed only 7 monophyletic clades to explain the tree, vs. 248 for organism) -- consistent with widespread horizontal transfer, and a plausibility check supporting H_B before the formal statistical test is run.
- The OXA exception (partial organism-specific clustering, driven by AB's and Pa's own intrinsic allelic radiations) is valuable *because* it complicates the simple story -- it shows the pattern isn't universal, which is what makes H_B a real test rather than a foregone conclusion. OXA is the case that could have falsified the general pattern and didn't (the pooled result still leaned heavily toward family-driven clustering).

### Limitations to state upfront

- Cross-sectional genomic data -- no temporal sampling, so "recent transfer, now adapting" is inference from phylogenetic placement, not direct observation.
- Recombination (known for blaTEM/CTX-M/OXA lineages) can manufacture false positive-selection signals at breakpoints; no recombination screen is in the pipeline yet.
- Low-diversity gene families (several already found dominated by one near-invariant allele) will lack power for H_C regardless of true effect.

### Significance statement

This would be the first study to combine the full RGI/CARD + MOB-suite mobility classification with phylogenetic and selection-pressure analysis across all six ESKAPE pathogens simultaneously (~9,947 genomes) -- directly filling both gaps identified in the literature review, and turning Che et al.'s general conjugative-plasmid enrichment finding into a specific, testable, ESKAPE-scoped, multi-line-of-evidence claim.
