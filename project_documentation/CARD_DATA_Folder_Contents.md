# CARD_DATA Folder Contents

Documents every file in `CARD_DATA/card-data/` (extracted from `card-data.tar.bz2`, the official CARD database download bundle). Descriptions below combine the bundle's own `CARD-Download-README.txt` with direct inspection of each file's actual structure (columns, row counts, sample rows) rather than the README alone, since a couple of details there don't exactly match what's on disk.

**Database version**: CARD v4.0.2 (from `card.json`'s `_version` field), downloaded/timestamped 2026-08-11.

**License note** (from the bundle's own README): commercial use or reproduction is prohibited without written permission from McMaster University. Citation: Alcock et al. 2023, *Nucleic Acids Research* 51, D690-D699.

## `card.json` (38 MB) -- the core database

The complete CARD database: 6,451 AMR detection models (keyed by numeric model ID, plus three metadata keys `_version`/`_timestamp`/`_comment`). This is the file RGI actually loads to run detection -- everything else in this folder is a derived export/summary of what's in here. Each model entry has: `model_id`, `model_name`, `model_type` (e.g. "protein homolog model"), `model_type_id`, `model_description`, `model_param`, `model_sequences`, `ARO_accession`, `ARO_id`, `ARO_name`, `CARD_short_name`, `ARO_description`, `ARO_category`.

## FASTA files -- reference sequences by model type

CARD splits its reference sequences into separate nucleotide/protein FASTA pairs per **model type** -- the detection logic differs by type (see below), so keeping them separate matters for which analysis a file is appropriate for. Verified record counts:

| Model type | Nucleotide file | Protein file | Records |
|---|---|---|---|
| Protein homolog | `nucleotide_fasta_protein_homolog_model.fasta` | `protein_fasta_protein_homolog_model.fasta` | 6,059 |
| Protein variant | `nucleotide_fasta_protein_variant_model.fasta` | `protein_fasta_protein_variant_model.fasta` | 239 |
| Protein knockout | `nucleotide_fasta_protein_knockout_model.fasta` | `protein_fasta_protein_knockout_model.fasta` | 16 |
| Protein overexpression | `nucleotide_fasta_protein_overexpression_model.fasta` | `protein_fasta_protein_overexpression_model.fasta` | 15 |
| rRNA gene variant | `nucleotide_fasta_rRNA_gene_variant_model.fasta` | *(no protein equivalent -- rRNA genes aren't translated)* | 86 |

**What each model type means** (from the README): **protein homolog** = genes that confer resistance simply by being present, no mutation needed -- e.g. an acquired beta-lactamase (this is the model type the vast majority of CARD entries use, and what most BLAST/metagenomic screening relies on). **Protein variant** = reference wild-type sequences used to map specific resistance-conferring SNPs onto a normally-susceptible gene (e.g. a chromosomal efflux regulator that only confers resistance with a particular point mutation) -- using these without secondary SNP screening produces false positives, since the reference sequence itself is the *susceptible* form. **Protein knockout** = resistance from gene *loss* (a functioning copy is required for susceptibility). **Protein overexpression** = resistance from elevated expression of a normally-present gene. **rRNA gene variant** = SNP-based resistance in ribosomal RNA genes specifically (e.g. 16S mutations), kept separate from protein variant since rRNA isn't translated.

Nucleotide headers carry the GenBank accession, strand, coordinates, ARO accession, and CARD short name, e.g.:
```
>gb|GQ343019.1|+|132-1023|ARO:3002999|CblA-1 [mixed culture bacterium AX_gF3SD01_15]
```
Protein headers are the same but without the strand/coordinate fields.

## Index / lookup TSV files

| File | Rows | Columns (verified) | What it is |
|---|---|---|---|
| `aro_index.tsv` | 6,464 | ARO Accession, CVTERM ID, Model Sequence ID, Model ID, Model Name, ARO Name, Protein Accession, DNA Accession, AMR Gene Family, Drug Class, Resistance Mechanism, CARD Short Name | The master per-model index -- one row per detection model, with its full classification (family/drug class/mechanism) and both accession types in one place. The widest, most complete of the index files. |
| `aro_categories_index.tsv` | 6,412 | Protein Accession, DNA Accession, AMR Gene Family, Drug Class, Resistance Mechanism | GenBank accessions cross-referenced against the three major ARO category types (family/drug class/mechanism) -- narrower than `aro_index.tsv` (no model/ARO-name columns), one row per accession-category link (an accession can appear more than once if it maps to multiple categories). |
| `aro_categories.tsv` | 686 | ARO Category, ARO Accession, ARO Name | The category vocabulary itself -- the list of distinct AMR Gene Family / Drug Class / Resistance Mechanism *terms* that `aro_index.tsv`/`aro_categories_index.tsv` reference, each with its own ARO accession. This is the terminology this whole ESKAPE project's "AMR Gene Family" column values are drawn from. |
| `PMID.tsv` | 4,409 | ARO Accession, CVTERM ID, ARO Name, PMID, DOI, ISBN | Literature citations per ARO term (semicolon-separated list of PMIDs where more than one paper documents a term). Note: the README calls this file `pmids.tsv` (lowercase, plural) -- the actual filename on disk is `PMID.tsv`. |
| `snps.txt` | 3,736 | Accession, Name, Model Type, Parameter Type, Mutations, CARD Short Name, source, citation | The specific resistance-conferring mutations used by protein variant / rRNA variant / overexpression models -- e.g. `Acinetobacter baumannii gyrA conferring resistance to fluoroquinolones`, mutation `S81L`. This is what a "protein variant model" hit is actually being screened against. |
| `shortname_pathogens.tsv` | 99 | Abbreviation, Pathogen | The pathogen-name abbreviation table used to build CARD Short Names (e.g. `Abau` = *Acinetobacter baumannii*) -- capitalized first letter of genus + lowercase first three letters of species, per the README's naming convention. |
| `shortname_antibiotics.tsv` | 82 | AAC Abbreviation, Molecule | The antibiotic-class abbreviation table used the same way (e.g. `AMG` = Aminoglycosides), sourced from the ASM journal antibiotic abbreviation standard plus CARD-custom additions. |

**CARD Short Name convention** (from the README, relevant since this project's own gene-family short-code work paralleled this): unique per model, whitespace replaced with underscores, gene names ≤15 characters kept as-is, longer names abbreviated by CARD curators. Typically follows `pathogen_gene` or `pathogen_gene_drug` (e.g. `Abau_gyrA_FLO` for the *A. baumannii* gyrA fluoroquinolone-resistance variant model seen in `snps.txt` above).

## Other files

- `CARD-Download-README.txt` -- the bundle's own documentation, used as the primary source for the descriptions above.
- `card-data.tar.bz2` (in the parent `CARD_DATA/` folder, one level up from `card-data/`) -- the original downloaded archive these files were extracted from.
