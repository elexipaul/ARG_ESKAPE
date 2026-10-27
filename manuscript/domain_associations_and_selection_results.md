# Physicochemical signatures of resistance-protein domains and selective constraint across ESKAPE resistance mechanisms

## 1. Physicochemical features associated with protein domains

### Data and approach

Associations between sequence-derived physicochemical descriptors and protein domain annotations were examined across 6,059 Comprehensive Antibiotic Resistance Database (CARD) reference proteins, each described by 996 Pfeature and ProFET-style descriptors and annotated with InterProScan (Pfam domains). For each domain–descriptor pair, the strength of association was summarised as a partial Spearman correlation (ρ) adjusted for log protein length, with an exploratory 95% interval from 2,000 whole-cluster bootstrap replicates. Sequence dependence was handled using MMseqs2 sequence groups (connected components at 40% identity and 80% coverage). To judge whether an association was carried by a single lineage, ρ was recalculated after removing the largest group of domain-carrying proteins. Descriptor names follow the feature tables: AAC_X is the percentage of amino acid X in the protein, DPC1_XY is the percentage of adjacent X–Y dipeptides, and the prefixes N_, M_ and C_ denote the N-terminal, middle and C-terminal thirds of the sequence; pI is the isoelectric point and GRAVY the grand average of hydropathy.

### Table 1. Domain–descriptor associations

| Domain (Pfam) | Domain function | Main carrier families | Descriptor signal in carriers vs all other proteins | ρ [95% bootstrap interval] | Sequence groups | ρ without largest group |
|---|---|---|---|---|---|---|
| MFS_1 (PF07690) | Major facilitator superfamily transporter; proton-coupled drug export | MFS efflux pumps (93/119) | More hydrophobic: GRAVY 0.88 vs −0.19; aliphatic index 124 vs 88; Asp 2.2% vs 5.2% | +0.22 [0.14, 0.33] (aliphatic index) | 40 | +0.20 |
| ABC_tran (PF00005) | ATP-binding cassette nucleotide-binding domain | ABC efflux pumps (19/29); ABC-F ribosomal protection proteins (12/12) | Glu-rich (8.1% vs 4.8%); Pro-poor; acidic C-terminal third (pI 5.2 vs 8.9) | +0.22 [0.11, 0.33] (Glu) | 26 | +0.17 |
| RrnaAD (PF00398) | rRNA adenine methyltransferase | Erm 23S rRNA methyltransferases (47/48) | Strongly basic: pI 10.2 vs 8.5; net charge +17.6 vs +2.0 at pH 7 | +0.16 [0.08, 0.29] | 12 | +0.13 |
| APH (PF01636) | Aminoglycoside and macrolide phosphotransferase | APH(3′), APH(2″) and other APH families; MPH (14/15) | Acidic: pI 4.9 vs 8.5 (APH(3′) 4.6; MPH 5.1) | −0.14 [−0.24, −0.08] | 16 | −0.12 |
| Acetyltransf_1 / Acetyltransf_8 (PF00583, PF13523) | GNAT-family acetyltransferase | AAC(6′), AAC(3), AAC(2′), streptothricin acetyltransferase | Acidic: pI 5.2 (PF00583) and 4.7 (PF13523) vs 8.5 | −0.11 [−0.21, −0.05] (PF00583) | 16 / 8 | −0.09 / −0.06 |
| Response_reg / HisKA (PF00072, PF00512) | Two-component system receiver and sensor-kinase domains | VanR, VanS, antibiotic efflux regulators | Acidic N-terminal third in receivers (pI 4.4 vs 7.9); Glu-rich sensor kinases (7.3% vs 4.9%) | −0.09 [−0.17, −0.04] / +0.11 [0.06, 0.16] | 11 / 14 | −0.06 / +0.09 |
| DHFR_1 (PF00186) | Dihydrofolate reductase (trimethoprim target replacement) | Trimethoprim-resistant dfr (49/59) | Higher aromaticity in C-terminal third (0.15 vs 0.08) | +0.13 [0.02, 0.26] | 11 | +0.09 |
| Lactamase_B (PF00753) | Metallo-β-lactamase (zinc-dependent) | IMP (91/100), VIM (90/90), NDM (71/71) | His-rich (3.2% vs 1.5%); Gly–His dipeptides (DPC1_GH) | +0.40 [0.09, 0.57] (His); +0.56 [0.15, 0.70] (DPC1_GH) | 8 | +0.28; +0.40 |
| VanY (PF02557) | D,D-carboxypeptidase of vancomycin-resistance clusters | Glycopeptide resistance gene cluster (10 proteins) | Trp–His dipeptides (0.42% vs 0%); Tyr-rich (5.5% vs 3.1%) | +0.25 [0.12, 0.38] (DPC1_WH) | 5 | +0.19 |
| Transpeptidase (PF00905) | Penicillin-binding protein / class D (OXA) serine β-lactamase fold | OXA-51-like (383), OXA-50-like (104), OXA-213-like (103) and other OXA families | Trp-rich (2.6% vs 1.4%); more basic middle third (pI 7.0 vs 5.5) | +0.47 [0.01, 0.69] (Trp) | 8 (91% in one group) | +0.21 |
| Beta-lactamase2 (PF13354) | Class A serine β-lactamase | CTX-M (267/267), KPC (231/231), SHV (216/216) | Lower aromaticity (0.057 vs 0.094); Thr-rich (7.2% vs 5.5%) | −0.67 [−0.77, −0.05] (aromaticity) | 7 (94% in one group) | −0.19 |
| ACR_tran (PF00873) | Resistance-nodulation-cell division (RND) inner-membrane transporter | RND efflux pumps (31/109) | Much larger proteins (median 112 vs 31 kDa); net negative charge at pH 7 (−7.5 vs +2.0) | −0.16 [−0.25, −0.02] (net charge) | 5 | −0.09 |

The size difference for ACR_tran is reported from medians only, because a length-adjusted correlation is not meaningful for a size descriptor. For every other pair listed, the unadjusted effect (Cliff's δ) and the length-adjusted ρ have the same sign.

### Interpretation

The associations fall into three broad patterns. The first concerns membrane transporters. Proteins carrying the MFS_1 domain were markedly more hydrophobic and aliphatic-rich than the rest of the dataset and contained less aspartate, a composition consistent with a transporter built from multiple membrane-spanning helices. This was the best-supported association in the analysis: it was distributed across 40 sequence groups and was essentially unchanged when the largest group was removed. RND transporters were distinguished mainly by their size, as expected for large multi-domain inner-membrane pumps.

The second pattern links protein charge to the charge of the molecule that the protein binds or modifies. Erm methyltransferases were strongly basic, which is consistent with enzymes that bind ribosomal RNA, a polyanion. Conversely, the phosphotransferases that inactivate aminoglycosides and macrolides (APH and MPH) and the GNAT acetyltransferases that inactivate aminoglycosides were acidic, with median isoelectric points between 4.6 and 5.2; their substrates carry positively charged amino sugars, so an acidic binding surface is the expected arrangement. The receiver domains of two-component regulators were also acidic, consistent with their conserved acidic phosphorylation site. These effects were modest in size (|ρ| ≈ 0.1–0.16), but each rested on 11–16 sequence groups and kept its direction when the largest group was excluded.

The third pattern involves residue composition that may reflect active-site chemistry. Metallo-β-lactamases were enriched in histidine and in Gly–His dipeptides, which is consistent with the histidine ligands that coordinate zinc in these enzymes; the association weakened but persisted when the dominant group was removed (ρ for DPC1_GH from 0.56 to 0.40). The two numerically strongest associations, tryptophan enrichment in OXA-type transpeptidase domains and lower aromaticity with higher threonine content in class A β-lactamases, each depended on fewer than ten sequence groups, with more than 90% of carriers in a single group. In both cases ρ fell to approximately 0.2 once that group was removed, so these are better described as composition traits of the sampled lineages than as general properties of the enzyme classes.

### Limitations

These associations are exploratory and descriptive. The bootstrap intervals were computed on the same data used to screen the pairs, so they are stability checks rather than independent confirmation. Domain calls and physicochemical descriptors are derived from the same sequences, and Pfam and InterPro annotations are not independent of one another. Whole-protein composition does not locate catalytic or binding residues; mapping descriptors onto domain coordinates and experimentally characterised sites would be required to support residue-level explanations. The population is CARD reference proteins rather than clinical isolates.

## 2. Resistance mechanisms and selective constraint in Gram-positive and Gram-negative ESKAPE pathogens

### Data and approach

Selection pressure was estimated for 19,071 resistance genes from 92 AMR gene families identified in the six ESKAPE pathogens. Within each family, every gene was compared with a single family reference sequence, and non-synonymous (dN) and synonymous (dS) substitution rates and their ratio (ω = dN/dS) were estimated by maximum likelihood in PAML. Organisms were grouped as Gram-negative (*Acinetobacter baumannii*, *Enterobacter* spp., *Klebsiella pneumoniae* and *Pseudomonas aeruginosa*; 15,533 genes in 77 families) and Gram-positive (*Enterococcus faecium* and *Staphylococcus aureus*; 3,538 genes in 42 families). Each family was assigned the resistance mechanism most frequently given to its members in CARD.

Because pairwise ω is unreliable when substitution rates are saturated or close to zero, comparisons of selective constraint were restricted to those with 0.01 ≤ dS ≤ 2 and dN ≤ 2 (4,681 of 18,782 comparisons with measurable dS).

### Table 2. Composition of the analysed resistance genes by mechanism

| Resistance mechanism | Gram-negative genes (families) | % | Gram-positive genes (families) | % |
|---|---|---|---|---|
| Antibiotic target alteration | 5,705 (19) | 36.7 | 1,185 (20) | 33.5 |
| Antibiotic efflux | 4,823 (5) | 31.1 | 1,837 (5) | 51.9 |
| Antibiotic inactivation | 3,323 (44) | 21.4 | 283 (12) | 8.0 |
| Reduced permeability to antibiotic | 1,421 (4) | 9.1 | 0 | 0.0 |
| Antibiotic target replacement | 181 (2) | 1.2 | 108 (2) | 3.1 |
| Antibiotic target protection | 80 (3) | 0.5 | 125 (3) | 3.5 |

The resistance genes analysed in the Gram-negative pathogens were dominated by largely chromosomal mechanisms. Target alteration included lipid A modification by the pmr and arn genes (2,019 genes), elfamycin-resistant EF-Tu (657) and penicillin-binding protein and UhpT variants. Efflux was represented by the MFS (2,390), ABC (821), *P. aeruginosa* Mex RND (818), SMR (500) and MATE (294) families. Reduced permeability through porins (1,421 genes) was found only in this group. Inactivating enzymes accounted for fewer genes but the widest range of families (44), mainly β-lactamases and aminoglycoside-modifying enzymes.

In the two Gram-positive pathogens, efflux was the predominant mechanism (MFS 1,311, kdpDE 222 and MATE 194 genes), followed by target alteration, chiefly the vancomycin-resistance clusters of *E. faecium* and fluoroquinolone-resistant parC. Inactivation was represented by few families (BlaZ, APH(3′)-IIIa and the bifunctional AAC(6′)-APH(2″) protein), and target replacement by the mecA-encoded PBP2a of *S. aureus* (94 genes).

Two features of these counts affect their interpretation. Approximately 1,235 of the Gram-negative target-alteration genes are homologues of vancomycin-resistance cluster genes (glycopeptide resistance gene cluster, Van ligase, vanH and vanW), which are unlikely to confer glycopeptide resistance in Gram-negative bacteria. In addition, the Gram-positive group comprises only two species, so differences between the groups cannot be separated from differences between organisms.

### Table 3. Selective constraint by resistance mechanism

Median ω for comparisons with 0.01 ≤ dS ≤ 2 and dN ≤ 2.

| Resistance mechanism | Gram-negative median ω (n) | Gram-positive median ω (n) |
|---|---|---|
| Antibiotic efflux | 0.030 (207; ABC family only) | 0.148 (138) |
| Antibiotic target alteration | 0.037 (2,110) | 0.057 (461) |
| Reduced permeability to antibiotic | 0.037 (104) | — |
| Antibiotic inactivation | 0.149 (1,307) | 0.243 (135) |
| Antibiotic target protection | 0.176 (19) | 0.184 (79) |
| Antibiotic target replacement | 0.229 (120; sul) | 0.288 (1; not interpretable) |
| All mechanisms | 0.051 (3,867) | 0.119 (814) |

Purifying selection was the predominant pattern: 99.4% of the bounded comparisons had ω < 1 (median 0.056). Constraint was strongest for chromosomally encoded resistance proteins, including efflux components, porins and drug targets (median ω 0.03–0.06), in which resistance arises through a small number of substitutions in an otherwise conserved protein. Acquired resistance determinants, including β-lactamases, aminoglycoside-modifying enzymes, sul and tetracycline ribosomal protection proteins, were less constrained (median ω 0.15–0.29), consistent with ongoing allelic diversification of mobile resistance genes. Comparisons from the Gram-positive pathogens were less constrained overall (median ω 0.119 vs 0.051), which partly reflects the different mix of families in the two groups.

### Positive selection

Under the bounded criteria, 30 comparisons (28 distinct alleles) had ω > 1, all with values between 1.01 and 1.91. These comprised APH(3′)-IIIa in *S. aureus* (12) and *E. faecium* (6), with ω ≈ 1.12; ANT(2″)-Ia in *A. baumannii* and *P. aeruginosa* (4); OXA-4 in *P. aeruginosa* (2); and single comparisons for AAC(3)-IVa, APH(6)-Id, SHV-12, TEM-214 and Mrx in *K. pneumoniae* and sepA in *S. aureus*. Whole-gene ω values this close to 1 do not constitute evidence of positive selection. The selection data therefore indicate that resistance genes across the ESKAPE pathogens are dominated by purifying selection, without a robust signal of positive selection at the whole-gene level.

### Limitations

Most comparisons fell outside the range in which pairwise ω can be interpreted: 12,158 of the 18,782 comparisons with measurable dS had dS > 5, and 1,016 had dN > 2. This arises because each family was compared against a single reference sequence, while many CARD families group different genes; for example, the pmr phosphoethanolamine transferase family includes ArnT, PmrF, EptB, ArnA, BasRS and CprRS. Comparisons with near-zero dS, such as Mrx (dS 0.0001; ω = 60), give inflated ratios. Unrestricted counts of ω > 1 (407 genes) should therefore not be interpreted as positive selection. Testing for positive selection requires codon-site or branch-site models applied to alignments within individual genes or subfamilies (for example PAML models M7 vs M8, or HyPhy FUBAR and MEME).

## 3. Data sources and notes

- Domain–descriptor associations: `CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/tables/stable_exploratory_associations.csv`, with `physicochemical_matrix.csv`, `pfam_domain_binary.csv`, `sequence_clusters.csv`, `protein_manifest.csv` and `target_annotation_prevalence.csv` from the same folder. The ρ values after removing the largest group were recalculated with the partial rank method in `project_documentation/Protein_Annotation_Audit_2026-09-22/audit_saved_results.py`.
- Heatmap of the leading associations: `CARD_DATA/card-data/refined_details/Final_Results/Protein_Annotation_Physicochemical_Association/figures/annotation_physicochemical_heatmap.png`.
- Selection estimates: `Results/selection_analysis/ALL_reference_vs_all_PAML_dNdS.tsv`; family names from `Results/AMR_GeneDistribution/AMR_Family_ID_Mapping.csv`; organism from the code in each gene identifier.
- Resistance mechanism: the most frequent "Resistance Mechanism" among each family's entries in `CARD_DATA/card-data/aro_categories_index.tsv`. Families absent from that file or with tied mechanisms were assigned manually: Erm 23S ribosomal RNA methyltransferase, penicillin-binding protein mutations and rifamycin-resistant rpoB as target alteration; OXA beta-lactamase and the ampR regulator as inactivation; methicillin-resistant PBP2 as target replacement; and the unnamed family f142 (*P. aeruginosa* Mex genes) as efflux.
- The reference sequence for each family is the first sequence of that family's input FASTA and is not itself among the query comparisons; its gene identity cannot be recovered from the results table.
- Biological interpretations are stated as consistency with established protein function and require citation of verified primary sources before publication.
