File content
============
PLSDB version 2024_05_31_v2: 
----------------------------
* sequences.fasta.bz2: PLSDB plasmid sequences

Metadata-based annotations:
* nuccore.csv: Nuccore associated information.
* nucc_identical.csv: Nuccore associated information of plasmids discarded during dereplication step.
* assembly.csv: Assembly associated information.
* taxonomy.csv: Taxonomy entries associated with PLSDB records.
* biosample.csv: Processed BioSample associated information.
* biosample_attributes_plsdb.csv:  BioSample attributes retrieved and their description.
* disease_terms.csv: Disease/Symptom terms from Disease Ontology (release 2024-06-28) and Symptom Ontology (release 2024-05-17).
* ecopaths.csv: Ecosystems hierarchical levels used to classify plasmid ecosystems.

Sequence-based annotations:
* amr.tsv: Antimicrobial resistance genes predicted by AMRFinderPlus (version 3.12.8; database version 2024-01-31.1; amrfinder --report_all_equal --plus --ident_min 0.95 --coverage_min 90) and RGI (version 6.0.3, CARD version 3.2.9; rgi main --local --clean --liow_quality --include_nudge) using hAMRonization (version 1.1.4). To avoid redundancy, records sharing the same gene symbol, strand, and genomic coordinates (range +-10nt) were considered duplicates and only AMRFinderPlus records were retained.
* typing.csv: Plasmid Typing by MOB-typing (version 3.1.8; mob_suite --multi) and mlst (version 2.23.0, mlst --minid 85 --mincov 66 --minscore 50) with pMLST data available on 2024-05-31.
* typing_markers.csv: Plasmid Typing by MOB-typing results, feature-based.
* plasmidfinder.csv: Plasmid Typing by PlasmidFinder (version 2.1.6; plasmidfinder_db version 2.2.0; plasmidfinder.py -l 0.60 -t 0.80) surprassing the thresholds (min_cov=60; min_ident=80). For more info about the output check [here](https://cge.food.dtu.dk/services/PlasmidFinder/output.php).
* annotated_elements.csv: Sequences are annotated by retrieved from the automatic NCBI Prokaryotic Genome Annotation Pipeline (PGAP) already available on genbank records.
* plsdb_mashdb_sim.tsv: Pair of similar plasmids by Mash distance (version 2.3, mash dist -d 0.00123693)

Others:
* plsdb_sketch.msh: Mash sketches (version 2.3, cla: 'mash sketch -S 123 -k 21 -s 1000 -i')
* changes.tsv: List of removed/added/changed plasmid records compared to version 2023_11_03_v2.


2023_11_03_v2 vs 2024_05_31_v2
===========================
New file: nuccore_20240531_v2.csv (72556 x 21)
Old file: plsdb_20231103_v2.csv (59895 x 68)


Columns:
    * Only in new file: NUCCORE_has_indetical, VIRAL_category, VIRAL_putative, STATUS, STATUS_note
    * Only in old file: NUCCORE_BiosampleID, BIOSAMPLE_ACC, BIOSAMPLE_Location, BIOSAMPLE_Coordinates, BIOSAMPLE_IsolationSource, BIOSAMPLE_Host, BIOSAMPLE_CollectionDate, BIOSAMPLE_HostDisease, BIOSAMPLE_SampleType, ASSEMBLY_ACC, ASSEMBLY_Status, ASSEMBLY_coverage, ASSEMBLY_SeqReleaseDate, ASSEMBLY_SubmissionDate, ASSEMBLY_Lastest, ASSEMBLY_BiosampleID, TAXONOMY_superkingdom, TAXONOMY_phylum, TAXONOMY_class, TAXONOMY_order, TAXONOMY_family, TAXONOMY_genus, TAXONOMY_species, TAXONOMY_strain, TAXONOMY_taxon_rank, TAXONOMY_taxon_name, TAXONOMY_taxon_lineage, TAXONOMY_superkingdom_id, TAXONOMY_phylum_id, TAXONOMY_class_id, TAXONOMY_order_id, TAXONOMY_family_id, TAXONOMY_genus_id, TAXONOMY_species_id, TAXONOMY_strain_id, has_biosample, has_assembly, has_location, rMLST_hits, rMLST_hitscount, inclusions, Length, BIOSAMPLE_Host_processed, BIOSAMPLE_Host_processed_source, BIOSAMPLE_Host_label, BIOSAMPLE_HostDisease_processed, loc_lat, loc_lng, loc_parsed, plasmidfinder, pmlst

    * Old column names: NUCCORE_TaxonID, D1, D2 
    * New column names: TAXONOMY_UID, UMAP_D1, UMAP_D2 

Field descriptions
==================
The description of some columns can be found below.

* NUCCORE
    - `NUCCORE_UID`: `nuccore` UID
    - `NUCCORE_ACC`: `nuccore` accession
    - `NUCCORE_Description`: record description (retrieved from record's FASTA header)
    - `NUCCORE_CreateDate`: when the record was created (format: `yyyy/m/d`)
    - `NUCCORE_Topology`: topology, e.g. linear or circular
    - `NUCCORE_Completeness`: `nuccore` record completeness status
    - `NUCCORE_Genome`: genome tag
    - `NUCCORE_Source`: record source (INSDC or RefSeq)
    - `NUCCORE_Length`: sequence length
    - `NUCCORE_GC`: sequence GC content (computed from record's FASTA)
    - `NUCCORE_DuplicatedEntry`: plasmid records having the same accession but with other name (removed in filtering steps)
    - `NUCCORE_Identical`:  selected plasmid record during dereplication process.
    - `VIRAL_`: experimental feature generated using the following [protocol]/(https://www.protocols.io/view/viral-sequence-identification-sop-with-virsorter2-5qpvoyqebg4o/v3)

* ASSEMBLY
    - `ASSEMBLY_UID`: `assembly` UID
    - `ASSEMBLY_Status`: assembly status, e.g. "Complete Genome"
    - `ASSEMBLY_SeqReleaseDate`: assembly release date
    - `ASSEMBLY_SubmissionDate`: assembly submission date
    - `ASSEMBLY_Lastest`: whether the assembly is tagged as "latest"

* BIOSAMPLE. More columns descriptions can be found at biosample_attributes_plsdb.csv
    - `BIOSAMPLE_UID`: `biosample` UID (associated with the corresponding `nuccore` record)
    - `BIOSAMPLE_ACC`: `biosample` accession

    - `LOCATION_query`: Original location name/coordinates (BIOSAMPLE_geo_loc_name/BIOSAMPLE_lat_lon)
    - `LOCATION_type`: Type of original location string (name/coordinates).
    - `LOCATION_name`: Parsed location name.
    - `LOCATION_lat`: Lattitude coordinate derived from processed location.
    - `LOCATION_lng`: Longitude coordinate derived from processed location.

    - `ECOSYSTEM_query`: BioSample attributes assessed for ecosystem plasmid inference. BIOSAMPLE: host, host_taxid, host_common_name, host_animal_breed, animal_env, local_class, soil_type, metagenome_source, samp_mat_type, source_type, host_tissue_sampled, tissue, host_body_habitat, isolation_source, env_medium, env_broad_scale, env_local_scale.
    - `ECOSYSTEM_tags`: hierarchical classification of plasmid ecosystem. See `ecopaths.csv`.
    - `ECOSYSTEM_taxid`: TAXONOMY UID for host-associated ecosystems.
    
    - `DISEASE_query`: BioSample attributes assessed for disease inference of host-associated ecosystems. BioSample: host_disease,host_disease_outcome, host_disease_stage, host_health_state, phenotype, health_state, disease.
    - `DISEASE_tags`: Diseases inferred.
    - `DISEASE_ontid`: Disease/Symptom Ontology UID associated. For detailed information see `disease_terms.csv`

* TAXONOMY
    - `TAXONOMY_UID`, `TAXONOMY_taxon_name`, `TAXONOMY_taxon_rank`: Taxon ID, name, and rank
    - `TAXONOMY_taxon_lineage`: Complete taxonomy lineage
    - `TAXONOMY_<rank>_id`, `TAXONOMY_<rank>`: Taxon ID and name for relevant taxonomic ranks (see below)

* TYPING. By MOB-typer and pMLST.
    - `num_contigs`: Number of sequences belonging to plasmid.
    - `md5`: md5 hash.
    - `rep_type(s)`: Replicon type(s).
    - `rep_type_accession(s)`: Replicon sequence accession(s).
    - `relaxase_type(s)`: Relaxase type(s).
    - `relaxase_type_accession(s)`: Relaxase sequence accession(s).
    - `mpf_type`: Mate-Pair formation type.
    - `mpf_type_accession(s)`: Mate-Pair formation sequence accession(s).
    - `orit_type(s)`: Origin of transfer type.
    - `orit_accession(s)`: Origin of transfer sequence accession(s).
    - `predicted_mobility`: Mobility prediction for the plasmid (Conjugative, Mobilizable, Non-mobilizable).
    - `mash_nearest_neighbor`: Accession of closest plasmid database match.
    - `mash_neighbor_distance`: Mash distance from query to match.
    - `mash_neighbor_identification`: Host taxonomy of the plasmid database match.
    - `primary_cluster_id`: primary MOB-cluster id of neighbor.
    - `secondary_cluster_id`: secondary MOB-cluster id of neighbor.
    - `predicted_host_range_overall_rank`: Taxon rank of convergence between observed and reported host ranges.
    - `predicted_host_range_overall_name`: Taxon name of convergence between observed and reported host ranges.
    - `observed_host_range_ncbi_rank`: Taxon rank of convergence of plasmids in MOB-suite plasmid DB.
    - `observed_host_range_ncbi_name`: Taxon name of convergence of plasmids in MOB-suite plasmid DB.
    - `reported_host_range_lit_rank`: Taxon rank of convergence of literature reported host ranges.
    - `reported_host_range_lit_name`: Taxon name of convergence of literature reported host ranges.
    - `associated_pmid(s)`: PubMed ID(s) associated with records.
    
    - `PMLST_scheme`: Scheme results are separated by "|"
    - `PMLST_sequence_type`: scheme name (ST)
    - `PMLST_alleles`, see below for allele formatting

*MLST allele formatting (from https://github.com/tseemann/mlst)*

Symbol | Meaning | Length | Identity
---   | --- | --- | ---
`n`   | exact intact allele                   | 100%            | 100%
`~n`  | novel full length allele similar to n | 100%            | &ge; `--minid`
`n?`  | partial match to known allele         | &ge; `--mincov` | &ge; `--minid`
`-`   | allele missing                        | &lt; `--mincov` | &lt; `--minid`
`n,m` | multiple alleles                      | &nbsp;          | &nbsp;

* PLASMIDFINDER. Typing by plasmidfinder.
    - `typing`: Plasmid against which the input genome has been aligned.
    - `identity`: Percent identity in the alignment between the best matching plasmid in the database and the corresponding sequence in the inputgenome (also called the high-scoring segment pair (HSP)). A perfect alignment is 100%, but must also cover the entire length of the plasmid in the database (compare example 1 and 3).
    - `Query/HSP Length`: Query length is the length of the best matching plasmid in the database, while HSP length is the length of the alignment between the best matching plasmid and the corresponding sequence in the genome (also called the high-scoring segment pair (HSP)).
    - `position_in_contig`: Starting position of the found gene in the contig.
    - `note`: Notes to the plasmid
    - `accession_number`: Reference Genbank accession number accoding to NCBI for the plasmid in the database.

--------------------------------------------------
# References

- **Mash**: "Mash: fast genome and metagenome distance estimation using MinHash", B. D. Ondov, T. J. Treangen, P. Melste
d, A. B. Mallonee, N. H. Bergman, S. Koren and A. M. Phillippy, Genome Biology, 2016, [paper link](https://genomebiology
.biomedcentral.com/articles/10.1186/s13059-016-0997-x), [repository link](https://github.com/marbl/Mash)
- **UMAP**: "UMAP: Uniform Manifold Approximation and Projection for Dimension Reduction", L. McInnes and J. Healy, N. Saul and L. Großberger, Journal of Open Source Software
v, 2018,
[paper link](http://joss.theoj.org/papers/10.21105/joss.00861), [repository link](https://github.com/lmcinnes/umap)
- **BLAST**: "Basic local alignment search tool." , S.F. Altschul, W. Gish, W. Miller, E. W. Myers and D. J.  Lipman, J. Mol. Biol. 215:403-410, [BLAST paper link](https://www.ncbi.nlm.nih.gov/pubmed/2231712), [BLAST+ paper link](https://www.ncbi.nlm.nih.gov/pubmed/20003500), [tool link](ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+)
- **CARD**: "CARD 2017: expansion and model-centric curation
of the comprehensive antibiotic resistance database.", B. Jia, A. R. Raphenya, B. Alcock, N. Waglechner, P. Guo, K. K. Tsang, B. A. Lago, B. M. Dave, S. Pereira, A. N. Sharma, S. Doshi, M. Courtot, R. Lo, L. E. Williams, J. G. Frye, T. Elsayegh, D. Sardar, E. L. Westman, A. C. Pawlowski, T. A. Johnson, F. S. Brinkman, G. D. Wright, and A. G. McArthur, Nucleic Acids Res., 2017, [paper link]( https://www.ncbi.nlm.nih.gov/pubmed/27789705)
- **pMLST in PubMLST**: [web-site](https://pubmlst.org/plasmid/)
- **mlst**: Tool implemented by Thorsten Seemann, [repository link](https://github.com/tseemann/mlst)
- **rMLST**: [rMLST at PubMLST](https://pubmlst.org/rmlst/)
- **MOB-suite**:  “MOB-suite: software tools for clustering, reconstruction and typing of plasmids from draft assemblies.” Robertson, James, and John H E Nash.  Microbial genomics vol. 4,8 (2018) [paper link](https://www.microbiologyresearch.org/content/journal/mgen/10.1099/mgen.0.000206), [repository link](https://github.com/phac-nml/mob-suite)

