# Gene Family Alignment Considerations & Drug Class Discussion

Notes from a discussion on what to keep in mind before running sequence alignment
(ahead of dN/dS selection-pressure analysis), and a follow-up on why a single CARD
"AMR Gene Family" can span multiple, unrelated drug classes.

## Genome / mobility-class traceability

**Q (user):** For genome mobility class traceability, I have the mobsuite results
so if required later it can be done from `AB_ARG_Location_Mapping1.csv`. Also:
explain point 1 (gene-family heterogeneity) a bit more, and reframe the alignment
considerations around biological/methodological points, not tooling.

**Findings:**

- `AB_ARG_Location_Mapping1.csv` (in `Results/Mobsuite/`) carries `sample_id`,
  `Contig`, `AMR gene name`, and `predicted_mobility` per RGI hit. The row count
  for `armA` in that file (424) matches exactly the 424 `armA` records in the
  gene-family FASTA, confirming a positional join back to mobility class is
  available whenever the branch-site (conjugative vs. non-conjugative) test is
  actually built. This closes the traceability gap flagged earlier -- no action
  needed now, just noted as resolved-when-needed.

### Point 1, expanded: "AMR Gene Family" is a mechanism label, not an ancestry label

CARD assigns a family based on shared resistance mechanism, not shared
evolutionary origin. A single family name can bundle four different situations,
and alignment for dN/dS is only valid for the first:

- **True alleles** -- one gene, differing by a handful of point mutations
  (TEM-1 vs TEM-2 vs TEM-103). The only case where "align the whole family" is
  correct.
- **Paralogs** -- genuinely different genes, often subunits of the same operon,
  each with its own evolutionary history. `adeA`, `adeB`, `adeC` are three
  separate proteins forming one tripartite efflux pump -- aligning `adeA`
  against `adeB` is like aligning hemoglobin against myoglobin: distantly
  related at best, and a dN/dS ratio computed across them measures nothing
  meaningful about selection on either gene.
- **Xenologs** -- non-orthologous genes from different lineages that converge
  on the same function. Klebsiella's `acrB` and Acinetobacter's `adeB` both
  land under "RND efflux pump" because they do the same job, but they're
  independently-acquired lineages with no real codon-level homology.
- **Chromosomal point-mutation calls, not genes at all** -- entries like
  "ramR mutants" or "soxS mutation conferring resistance" are SNP variants of a
  normal core-genome regulator, not acquired resistance genes. This is a
  different evolutionary process (adaptive mutation in the core genome) from
  horizontally-acquired mobile resistance genes, and arguably shouldn't be in a
  mobility-focused selection analysis at all.

Practical upshot: before committing to an alignment group, pull the distinct
gene names out of the family and sanity-check they're plausible alleles
(similar length, high pairwise identity -- rough rule of thumb >80-90%) rather
than trusting the family label at face value.

### Other biological/methodological points to keep in mind (alignment of a gene family)

1. **Length/indel consistency within the chosen group.** Outlier-length
   sequences usually mean a partial/truncated RGI call at a contig edge, an
   IS-element disruption, or an assembly artifact. Left in, they force spurious
   gap columns into the alignment that distort per-site dN/dS around them.
2. **Divergence has to sit in a usable range.** Too little variation (hundreds
   of byte-identical copies of one allele) means there's no polymorphism to
   test selection against. Too much divergence and synonymous sites saturate
   (multiple substitutions overwrite each other at the same codon position),
   biasing dS downward and making the ratio unreliable -- a classic failure
   mode for genes under long-term strong purifying selection.
3. **Recombination.** Beta-lactamase families in particular (blaTEM, blaCTX-M,
   blaOXA lineages) are known for mosaic recombination between alleles. PAML's
   site and branch-site models assume the gene tree correctly reflects the
   history at every site -- recombination breaks that assumption and can
   manufacture false "positively selected sites" that are really just
   recombination breakpoints.
4. **Minimum informative variation.** The likelihood-ratio tests need enough
   distinct haplotypes to have power. A gene with hundreds of hits but only 1-2
   unique sequences across all four organisms contributes essentially nothing
   to a selection test, no matter how many genomes it was found in.

## Why one gene family can span multiple drug classes

**Q (user):** CARD assigns a gene family based on shared mechanism, not
homology -- so why does a single gene family contain genes that provide
resistance to different drug classes?

**Answer:**

The framing needed a small correction: CARD's Gene Family grouping *is*
homology-based, just at the level of a broad structural/mechanistic superfamily
(e.g. "shares the RND transporter fold," "shares the MFS transporter fold")
rather than at the level of a single gene lineage. That distinction is exactly
what explains the multi-drug-class spread.

Checked against the project's own AB data (`AB_ARG_Location_Mapping1.csv`):
within the RND family, a single gene -- not the family as a whole -- already
spans many unrelated drug classes on its own:

| Gene | # Drug classes | Drug classes |
|---|---|---|
| `adeJ` | 10 | carbapenem, cephalosporin, diaminopyrimidine, fluoroquinolone, lincosamide, macrolide, penicillin beta-lactam, phenicol, rifamycin, tetracycline |
| `adeN` | 10 | (same set as `adeJ`) |
| `adeK` | 10 | (same set as `adeJ`) |
| `adeI` | 10 | (same set as `adeJ`) |
| `rsmA` | 3 | diaminopyrimidine, fluoroquinolone, phenicol |
| `adeS` | 2 | glycylcycline, tetracycline |

So the multi-drug-class spread isn't mainly coming from lumping many different
genes into one family -- it's already present at the single-gene level, for two
compounding reasons:

1. **The mechanism itself is inherently non-specific.** An efflux pump doesn't
   recognize a drug the way an antibody recognizes an antigen (tight,
   lock-and-key). It's a large hydrophobic channel that exports anything
   amphipathic/lipophilic enough to fit -- which includes macrolides,
   fluoroquinolones, tetracyclines, and beta-lactams simultaneously, because
   they're all moderately hydrophobic small molecules, chemically unrelated as
   antibiotic *classes* but similar enough as *physical objects* for one
   binding pocket to handle. This is why they're called "multidrug efflux
   pumps" in the literature -- polyspecificity is the defining trait of the
   mechanism, not an exception to it.
2. **"Drug Class" is annotated per gene from real/predicted substrate
   evidence, not derived from the family label.** CARD lists whatever drug
   classes a given variant has documented activity against. Family-level
   drug-class diversity is just the union of each member's own list -- and
   since each member's own list is already broad, the family total looks even
   broader.

On top of both of those, different paralogs in the same structural family can
still specialize -- `adeS` above shows only 2 classes, much narrower than
`adeJ`'s 10 -- so there's real divergence in substrate range across family
members too, layered on top of each gene's individual promiscuity.

For enzymatic mechanisms (beta-lactamases) the logic differs slightly but lands
in the same place: catalytic-site plasticity lets a handful of point mutations
shift a single allele's substrate range across beta-lactam subclasses -- the
classic TEM-1 (narrow-spectrum penicillinase) -> ESBL TEM variants (hydrolyze
expanded-spectrum cephalosporins) progression is one gene lineage's *own*
substrate range moving, not an artifact of grouping.

**Caveat:** part of the apparent breadth is a curation effect, not pure
biology -- better-studied genes (like `adeJ`, part of a well-characterized
efflux system) accumulate more documented drug classes over time simply
because more MIC assays have been run against them, while a less-studied
variant may look artificially narrow just for lack of testing.

## Do gene trees for a single gene family cluster by species or by gene/allele?

**Q (user):** If a gene tree is built for a single gene family, do the sequences
cluster by species, or by gene family (allele lineage)? Is this already
reported, or would it be an interesting finding?

**Answer:**

Already reported, and it's one of the classic pieces of evidence used to
demonstrate horizontal gene transfer (HGT) in the AMR literature. But there's a
sharper version of this question that would be a genuine contribution for this
study specifically, not just a yes/no.

**What's well-established:** for genuinely mobile AMR genes, a gene tree built
across species does *not* cluster by host species -- it clusters by allele
lineage instead, cutting straight across host phylogeny. This is the textbook
signature used to prove HGT:
- **blaNDM-1** -- first reported in Klebsiella pneumoniae and E. coli in India,
  then found nearly sequence-identical in Acinetobacter, Pseudomonas,
  Enterobacter, and beyond, carried on conjugative plasmids. Its gene tree
  looks nothing like the species tree of its hosts.
- **blaCTX-M** -- originated from a chromosomal Kluyvera gene, mobilized onto
  plasmids, now globally disseminated across Enterobacteriaceae and other
  genera; clusters by CTX-M group (1, 2, 8, 9, 25), not by host.
- **mcr-1** and **qnr** genes show the same pattern.

Why this happens biologically: a species tree tracks *vertical* descent --
divergence accumulates proportional to time since two lineages' common
ancestor. A horizontally-transferred gene's tree instead tracks the history of
the *mobile element* carrying it -- transfer resets that relationship, so a
recently-acquired gene in Klebsiella can be nearly identical to the same gene
in Acinetobacter even though those two species diverged tens of millions of
years ago. Chromosomal, vertically-inherited genes don't get this reset, so
they cluster by species.

**Connection to this project's own literature review:** Che et al. (PNAS,
2021) -- already in the paper-comparison table above -- found that IS elements
bridge ARGs between conjugative plasmids "across huge phylogenetic distances"
on a broad multi-genus set. Same phenomenon, demonstrated a different way
(IS-element bridging, not gene-tree topology), and not scoped to ESKAPE
specifically.

**Where the actually interesting angle is:** doing this one gene at a time
(like the NDM/CTX-M case studies above) isn't novel anymore. What would be new
is doing it *systematically* across all 30 shared families at once, and
correlating the *degree* of species-clustering breakdown with the graded
MOB-suite mobility class already computed (chromosomal -> non-mobilizable ->
mobilizable -> conjugative), rather than just eyeballing a tree. That turns
"conjugative genes get shared across species" (currently a presence/absence
claim in Study B above) into a dose-response phylogenetic test -- direct
evidence of transfer, not just co-occurrence -- and it's exactly the gap the
paper-comparison table shows nobody has filled for the full ESKAPE panel.
Layering the dN/dS work on top (does the loss of species-clustering coincide
with a burst of elevated dN/dS right at the host-jump branch -- adaptive
fine-tuning right after a transfer) would make it a three-way result:
phylogenetic signal x mobility class x selection.

**Expectation to set going in:** this likely won't be a uniform yes across all
30 families. Some will show clean species-mixing (recent, actively-spreading
elements); others -- despite being "shared" by presence/absence -- will still
cluster by species (older acquisitions now under within-species purifying
selection, or narrow-host-range plasmids). That heterogeneity, and whether it
lines up with mobility class, is probably the more interesting/publishable
result than a blanket "yes, HGT happens."

## The "nan" family: MexAB-OprM, silently missing from every family-level analysis

**Q (user):** One family is literally named `nan`, present only in Pseudomonas,
and it's missing from `ESKAPE_AMR_Gene_Family_Counts_percentage.csv` and the
`gene_family_counts_long_df` dataframe. Why did it get left out, and what is
its importance?

**Answer:**

**What "nan" actually is:** `Pa_ARG_Fasta_Files/nan.fasta` has 8,065 records
covering exactly 7 distinct genes: **MexA, MexB, MexG, MexR, ArmR, nalC,
nalD** -- the **MexAB-OprM efflux system and its regulators**, Pseudomonas's
principal *intrinsic* (chromosomal, constitutive) multidrug efflux pump.

**Why it's named "nan":** traced back to the raw RGI output itself, not
something this pipeline broke. A raw `MexB` hit pulled directly from
`CARD_Results/Pa_rgi_results/`:
```
Best_Hit_ARO = MexB
Resistance Mechanism = antibiotic efflux
Drug Class = 
AMR Gene Family = 
```
CARD's own database leaves the `AMR Gene Family` field genuinely blank for
these entries -- RGI knows *that* MexB is an efflux gene (`Resistance
Mechanism = antibiotic efflux`) but never assigned it a family label. When
the original extraction pipeline read that blank cell with pandas, it became
`NaN`; `str(nan)` -> `"nan"`, and that became the filename.

**Why Pseudomonas only:** MexAB-OprM and its regulon are curated in CARD as
Pseudomonas-specific reference sequences -- one of the species' defining
intrinsic resistance traits -- so RGI only ever matches it against Pa
genomes. Expected biology, not a pipeline error.

**Why it disappeared from percentages, not just counts:** the row *is*
present in the saved CSV: `Pseudomonas aeruginosa,nan,7,,` -- Count = 7 is
correct (matches the 7 unique gene names found directly in the fasta file).
But the two percentage columns are blank. `7` is an unambiguous integer, but
`"AMR Gene Family" = "nan"` is exactly the string pandas' `read_csv` treats
as a missing value by default, in *any* column, regardless of what that
column means. Somewhere downstream where the data got re-read from CSV or
grouped by `AMR Gene Family` to compute the percentages, `"nan"` silently
became a real missing value and dropped out of that groupby, while the row
itself survived elsewhere. A classic pandas footgun: a string that happens to
spell "nan" gets swallowed on read, not on write.

**Why this matters:** MexAB-OprM is arguably the single most clinically
important resistance mechanism in *P. aeruginosa* -- largely responsible for
the species' baseline intrinsic resistance to beta-lactams (except
imipenem), fluoroquinolones, tetracyclines, macrolides, chloramphenicol, and
more, *without needing any acquired resistance gene at all*. MexR normally
represses it; ArmR (itself controlled by nalC/nalD) antagonizes MexR.
Loss-of-function mutations in `mexR`, `nalC`, or `nalD` derepress the pump
and are one of the best-documented drivers of MDR/XDR *P. aeruginosa* in the
clinical literature. With 8,065 hits -- found in essentially every single Pa
genome in this dataset -- this is a near-universal Pseudomonas trait that has
been silently invisible to every family-level percentage/heatmap/shared-family
analysis built on the notebook so far.
