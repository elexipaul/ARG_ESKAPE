# Phylogenetic Tree Terminology Glossary

Terms used to describe the beta-lactamase pilot tree (`beta_lactamase_tree.nwk` / `beta_lactamase_tree.png`), explained using that tree's own numbers as examples.

**Leaf** (also "tip" or "terminal") -- one actual sequence in the tree; the endpoint of a branch. This tree had 577 leaves = the 577 deduplicated beta-lactamase sequences.

**Clade** -- a branch point plus everything descending from it (an ancestor and all its descendants). Any place in the tree where a branch could be snipped to pull out a self-contained sub-tree is a clade. A clade can be as small as a single leaf or as large as the whole tree.

**Monophyletic** -- a clade is "monophyletic for X" if *every* leaf inside it shares property X, with nothing else mixed in. "The TEM sequences form a monophyletic group" means: draw a branch around them, and every single leaf inside that branch is TEM -- no CTX-M, no NDM, nothing else sneaks in.

**Maximal monophyletic clade** -- the *largest* clade that can be drawn around a group before it would have to start including something outside that group. If all 66 TEM sequences sit together on one clean branch, that's one maximal clade for TEM -- it can't be expanded any further without picking up a non-TEM leaf.

**"N maximal clades needed to cover all leaves"** -- the quantitative metric reported for the tree (30 for CARD family, 7 for Ambler class, 248 for organism). It's literally: how many separate, non-overlapping branches -- each one pure for that label -- does it take to account for every leaf in the tree exactly once? If TEM is one single clean branch, that costs 1. If a group's sequences were scattered in 47 disconnected spots with no two ever adjacent, that costs 47. **Lower is better** -- it means that label explains the tree's actual branching structure. The best possible score equals the number of categories (9 families, 4 classes, 4 organisms); the worst possible score is 577 (every leaf isolated).

**Singleton (cluster)** -- a "clade" of exactly one leaf: a sequence whose immediate neighbors on the tree are a different label, so it can't be grouped with any same-label relative -- it's on its own. ENT's 47 sequences forming 47 separate singleton clusters means literally none of ENT's own sequences ever sit next to each other in the tree -- every one is isolated, which is why organism gave almost no clustering signal.

**Ambler class (A/B/C/D)** -- not specific to this analysis; it's the standard, decades-old scheme microbiologists use to classify beta-lactamase enzymes by catalytic mechanism and protein fold. Classes A, C, and D all use a serine residue to break open the beta-lactam ring (and are distantly related to each other); class B ("metallo-beta-lactamases," where IMP and NDM live) uses zinc ions instead and is a structurally unrelated enzyme family entirely -- hence the near-perfect 7-clade split by class.

**Branch length** (the x-axis in the tree image) -- the estimated amount of evolutionary change (amino-acid substitutions per site) between a sequence and its ancestor. Longer horizontal segments mean more accumulated divergence.

**Midpoint rooting** -- since there's no known true ancestor or outgroup for a mixed set of enzyme classes like this, the tree was rooted at its own geometric "middle" (the point minimizing the worst-case root-to-tip distance) -- a standard fallback for giving an otherwise-unrooted tree a top-to-bottom orientation for display.

## Genes common to all 4 gram-negative organisms, per beta-lactamase family

Checked at the individual-gene level (not just the family level): of the 9 beta-lactamase families, only **7 genes** are present in all four of AB, ENT, Kp, and Pa. Two families (CMY, GES) have no single gene shared across all four organisms -- each organism's copies are different alleles.

| Family | Gene common to AB, ENT, Kp, Pa |
|---|---|
| CARB beta-lactamase | CARB-2 |
| CMY beta-lactamase | *(none)* |
| CTX-M beta-lactamase | CTX-M-15 |
| GES beta-lactamase | *(none)* |
| IMP beta-lactamase | IMP-8 |
| NDM beta-lactamase | NDM-1 |
| OXA beta-lactamase | OXA-2 |
| OXA-2-like beta-lactamase | OXA-2 |
| TEM beta-lactamase | TEM-1 |

Note: `OXA-2` shows up under both `OXA beta-lactamase` and `OXA-2-like beta-lactamase` -- the same gene name is classified under two different CARD family labels depending on the genome, a CARD curation quirk rather than two different genes.

## How the tree was interpreted

**The question:** does the tree's branching pattern separate sequences by gene family/enzyme class, or by which organism they came from? With 577 leaves, this isn't something that can be answered by eyeballing the image alone -- a quantitative check was needed, with the visualization used as a supporting sanity check afterward, not the primary evidence.

**The method:** for each labeling scheme (CARD family, Ambler class, organism), the tree was walked to find the minimum number of maximal monophyletic clades required to account for every leaf exactly once (see the "N maximal clades needed" glossary entry above for the mechanics). This turns "does the tree look organism-driven or family-driven" into a single comparable number per scheme, rather than a subjective visual call. Two fixed reference points bound every score: the *best possible* (one clade per category -- 9 for family, 4 for class, 4 for organism) and the *worst possible* (577 -- every leaf isolated, meaning that label has zero relationship to tree structure).

**Reading the results:**

| Labeling scheme | Categories | Clades needed | Distance from best |
|---|---|---|---|
| Ambler class | 4 | 7 | +3 -- essentially as clean as possible |
| CARD family | 9 | 30 | +21 -- still very clean; the gap is almost entirely one family (OXA) |
| Organism | 4 | 248 | +244 -- barely better than total fragmentation |

Organism's score (248) sitting close to the worst case (577) rather than its best case (4) is what makes the call unambiguous: organism identity has almost no relationship to where a sequence sits in the tree. Family and class, by contrast, sit close to their best-case scores, meaning the tree's actual branch structure is explained by which enzyme a sequence is, not which bacterium it was found in.

**Why the family score (30) isn't a perfect 9:** breaking down which families contributed the gap showed it wasn't spread evenly -- CMY, CTX-M, GES, IMP, NDM, and TEM each contributed exactly 1 clade (perfect), while OXA alone contributed 14 and OXA-2-like contributed 8. That fragmentation is itself a real, known signal rather than noise: OXA is a recognized outlier among beta-lactamase families for containing multiple distantly related sub-lineages (OXA-48-like, OXA-1-like, OXA-10-like carbapenemases, etc.), so its own internal diversity -- not a failure of the family-vs-organism test -- explains most of the shortfall from the theoretical best score.

**Why class (7) beats family (9) as an explanation:** class groups CARB+CTX-M+GES+TEM together as one broader category (all class A), which absorbs some of the boundary noise between those four individually-clean families -- consistent with class being the deeper, more fundamental biological grouping (shared catalytic mechanism and fold) that family names sit inside.

**Role of the image:** the rendered tree (`beta_lactamase_tree.png`) was used only to visually confirm the quantitative result, not to derive it -- looking at it, each family occupies its own contiguous block of branches, and within each block the four organism colors are thoroughly interspersed rather than forming their own sub-blocks, matching the 30-vs-248 clade-count finding.

## All beta-lactam-related AMR gene families in the dataset

The 9 families used for the tree pilot (CARB, CMY, CTX-M, GES, IMP, NDM, OXA, OXA-2-like, TEM) are only the ones **shared across all four gram-negative organisms** (AB, ENT, Kp, Pa). Across the full dataset (all six ESKAPE organisms), there are **58 distinct beta-lactam-related families** in total -- 56 are `___ beta-lactamase` enzyme families, and 2 are other beta-lactam-resistance mechanisms that aren't enzymes (a porin-permeability mutation and a penicillin-binding-protein target mutation). Marked with `*` below are the 9 shared across all four gram-negative organisms; each entry also lists which organism(s) it was found in.

| Family | Present in |
|---|---|
| ACC beta-lactamase | ENT, Kp |
| ACT beta-lactamase | ENT |
| ADC beta-lactamase without carbapenemase activity | AB |
| ADC beta-lactamases pending classification for carbapenemase activity | AB |
| AFM beta-lactamase | Pa |
| BKC Beta-lactamase | ENT |
| BlaZ beta-lactamase | SA |
| **CARB beta-lactamase*** | AB, ENT, Kp, Pa |
| CMH beta-lactamase | ENT |
| **CMY beta-lactamase*** | AB, ENT, Kp, Pa |
| **CTX-M beta-lactamase*** | AB, ENT, Kp, Pa |
| DHA beta-lactamase | ENT, Kp |
| DIM beta-lactamase | Pa |
| FIM beta-lactamase | Pa |
| FONA beta-lactamase | Kp |
| FRI beta-lactamase | ENT |
| **GES beta-lactamase*** | AB, ENT, Kp, Pa |
| GIM beta-lactamase | AB |
| General Bacterial Porin with reduced permeability to beta-lactams (not an enzyme) | ENT, Kp |
| IMI beta-lactamase | ENT |
| **IMP beta-lactamase*** | AB, ENT, Kp, Pa |
| KHM beta-lactamase | ENT |
| KPC beta-lactamase | ENT, Kp, Pa |
| LAP beta-lactamase | ENT, Kp |
| LCR beta-lactamase | Pa |
| LEN beta-lactamase | ENT, Kp |
| MIR beta-lactamase | ENT |
| MOX beta-lactamase | Kp |
| **NDM beta-lactamase*** | AB, ENT, Kp, Pa |
| **OXA beta-lactamase*** | AB, ENT, Kp, Pa |
| OXA-1-like beta-lactamase | ENT, Kp, Pa |
| OXA-10-like beta-lactamase | ENT, Kp, Pa |
| OXA-134-like beta-lactamase | AB |
| **OXA-2-like beta-lactamase*** | AB, ENT, Kp, Pa |
| OXA-23-like beta-lactamase | AB |
| OXA-24-like beta-lactamase | AB |
| OXA-46-like beta-lactamase | Pa |
| OXA-48-like beta-lactamase | ENT, Kp |
| OXA-5-like beta-lactamase | ENT, Pa |
| OXA-50-like beta-lactamase | Pa |
| OXA-51-like beta-lactamase | AB |
| OXA-58-like beta-lactamase | AB |
| OXA-9-like beta-lactamase | ENT, Kp, Pa |
| PDC beta-lactamase | Pa |
| PER beta-lactamase | AB, Kp, Pa |
| PME beta-lactamase | Pa |
| Penicillin-binding protein mutations conferring resistance to beta-lactam antibiotics (not an enzyme) | ENT, Kp |
| RSA beta-lactamase | Kp |
| SCO beta-lactamase | Kp |
| SED beta-lactamase | ENT |
| SFO beta-lactamase | ENT, Kp |
| SHV beta-lactamase | AB, ENT, Kp |
| SIM beta-lactamase | ENT, Pa |
| SPM beta-lactamase | Pa |
| **TEM beta-lactamase*** | AB, ENT, Kp, Pa |
| VEB beta-lactamase | ENT, Kp, Pa |
| VIM beta-lactamase | ENT, Kp, Pa |
| ampC-type beta-lactamase | AB |

Note the pattern: most of the organism-specific ones (`OXA-51-like`, `OXA-23-like`, `OXA-58-like`, `ADC*`, `GIM`, `ampC-type` -> AB only; `PDC`, `AFM`, `DIM`, `FIM`, `LCR`, `PME`, `SPM` -> Pa only; `BlaZ` -> SA only) are intrinsic/species-characteristic enzymes, consistent with the species-specific clustering pattern found for `OXA beta-lactamase` in the CDS tree -- many beta-lactamase families are inherently tied to one organism's own chromosome, and the 9 marked with `*` are the exceptions genuinely shared across all four gram-negative species.

## The two non-enzyme families

Of the 58 beta-lactam-related families, 56 are `___ beta-lactamase` -- actual hydrolytic enzymes that break down the beta-lactam ring. Two are not enzymes at all:

1. **General Bacterial Porin with reduced permeability to beta-lactams** (ENT, Kp) -- a porin mutation that reduces membrane permeability, blocking the drug from entering the cell in the first place rather than destroying it.
2. **Penicillin-binding protein mutations conferring resistance to beta-lactam antibiotics** (ENT, Kp) -- a target-site mutation: the beta-lactam's normal binding target (PBP) is modified so the drug can no longer bind to it.

## Code: relabel ALL beta-lactam family FASTA headers (not just the 9 shared ones)

The bug in the pasted cell: the loop was over `gram_negative_family_short_names`, which only has 30 keys (the families shared across all four gram-negative organisms) -- that dictionary decides which families get processed, regardless of which folder `input_dir`/`output_dir` point at. Pointing at the full `ENT_ARG_Fasta_Files` folder didn't help, because the loop never asked about the other 43 files sitting in it.

Fix: a new dictionary covering all 57 beta-lactam families relevant to the four gram-negative organisms (58 total in the dataset, minus `BlaZ beta-lactamase` which is SA/gram-positive-only and won't exist in any of these four folders), looping over each organism's **full** `_ARG_Fasta_Files` folder (not `GN_AMR_Family`), and skipping a family if that organism doesn't have it rather than crashing. Reuses `relabel_fasta_headers` (already defined in section 14.1 of the notebook) unchanged.

```python
# Short codes for ALL beta-lactam-related AMR gene families relevant to the 4 gram-negative
# organisms (57 -- excludes BlaZ beta-lactamase, which is SA/gram-positive only).
all_beta_lactam_family_short_names = {
    'ACC beta-lactamase': 'ACC',
    'ACT beta-lactamase': 'ACT',
    'ADC beta-lactamase without carbapenemase activity': 'ADCn',
    'ADC beta-lactamases pending classification for carbapenemase activity': 'ADCp',
    'AFM beta-lactamase': 'AFM',
    'BKC Beta-lactamase': 'BKC',
    'CARB beta-lactamase': 'CARB',
    'CMH beta-lactamase': 'CMH',
    'CMY beta-lactamase': 'CMY',
    'CTX-M beta-lactamase': 'CTXM',
    'DHA beta-lactamase': 'DHA',
    'DIM beta-lactamase': 'DIM',
    'FIM beta-lactamase': 'FIM',
    'FONA beta-lactamase': 'FONA',
    'FRI beta-lactamase': 'FRI',
    'GES beta-lactamase': 'GES',
    'GIM beta-lactamase': 'GIM',
    'General Bacterial Porin with reduced permeability to beta-lactams': 'PORIN',
    'IMI beta-lactamase': 'IMI',
    'IMP beta-lactamase': 'IMP',
    'KHM beta-lactamase': 'KHM',
    'KPC beta-lactamase': 'KPC',
    'LAP beta-lactamase': 'LAP',
    'LCR beta-lactamase': 'LCR',
    'LEN beta-lactamase': 'LEN',
    'MIR beta-lactamase': 'MIR',
    'MOX beta-lactamase': 'MOX',
    'NDM beta-lactamase': 'NDM',
    'OXA beta-lactamase': 'OXA',
    'OXA-1-like beta-lactamase': 'OXA1',
    'OXA-10-like beta-lactamase': 'OXA10',
    'OXA-134-like beta-lactamase': 'OXA134',
    'OXA-2-like beta-lactamase': 'OXA2',
    'OXA-23-like beta-lactamase': 'OXA23',
    'OXA-24-like beta-lactamase': 'OXA24',
    'OXA-46-like beta-lactamase': 'OXA46',
    'OXA-48-like beta-lactamase': 'OXA48',
    'OXA-5-like beta-lactamase': 'OXA5',
    'OXA-50-like beta-lactamase': 'OXA50',
    'OXA-51-like beta-lactamase': 'OXA51',
    'OXA-58-like beta-lactamase': 'OXA58',
    'OXA-9-like beta-lactamase': 'OXA9',
    'PDC beta-lactamase': 'PDC',
    'PER beta-lactamase': 'PER',
    'PME beta-lactamase': 'PME',
    'Penicillin-binding protein mutations conferring resistance to beta-lactam antibiotics': 'PBP',
    'RSA beta-lactamase': 'RSA',
    'SCO beta-lactamase': 'SCO',
    'SED beta-lactamase': 'SED',
    'SFO beta-lactamase': 'SFO',
    'SHV beta-lactamase': 'SHV',
    'SIM beta-lactamase': 'SIM',
    'SPM beta-lactamase': 'SPM',
    'TEM beta-lactamase': 'TEM',
    'VEB beta-lactamase': 'VEB',
    'VIM beta-lactamase': 'VIM',
    'ampC-type beta-lactamase': 'ampC',
}

assert len(all_beta_lactam_family_short_names) == 57
assert len(set(all_beta_lactam_family_short_names.values())) == 57, 'duplicate short codes found'

# Organism code -> full (non-gram-negative-restricted) ARG_Fasta_Files folder name.
BETA_LACTAM_ORG_FOLDERS = {
    'AB':  'AB_ARG_Fasta_Files',
    'ENT': 'ENT_ARG_Fasta_Files',
    'Kp':  'Kp_ARG_Fasta_Files',
    'Pa':  'Pa_ARG_Fasta_Files',
}
BETA_LACTAM_OUTPUT_ROOT = os.path.join(GENEFAMILY_DIR, 'All_Beta_Lactam_Relabeled')

for org, folder in BETA_LACTAM_ORG_FOLDERS.items():
    input_dir = os.path.join(GENEFAMILY_DIR, folder)
    output_dir = os.path.join(BETA_LACTAM_OUTPUT_ROOT, org)
    os.makedirs(output_dir, exist_ok=True)

    org_counts = {}
    for family_name, short_code in all_beta_lactam_family_short_names.items():
        input_path = os.path.join(input_dir, f'{family_name}.fasta')
        if not os.path.exists(input_path):
            continue  # this organism doesn't have this family -- skip, don't crash
        output_path = os.path.join(output_dir, f'{family_name}.fasta')
        org_counts[family_name] = relabel_fasta_headers(input_path, output_path, org, short_code)

    print(f'{org}: {len(org_counts)} beta-lactam families relabeled, {sum(org_counts.values())} total records')
```

## Code: frame-integrity check for the pooled beta-lactam CDS (fixes a leftover-OXA-variable bug)

The bug in the pasted version: the frame-check loop correctly read from `beta_lactam_pooled_nt_path`, but the final write-out loop at the bottom read from `oxa_pooled_nt_path` instead -- a variable left over from adapting the OXA-only version, which either doesn't exist in this context or (if still in memory from an earlier OXA cell) would silently pull sequences from the wrong file, so the frame-check results and the actual output file wouldn't correspond to the same input. Also renamed the `oxa_frame_ok`/`oxa_frame_bad`/`oxa_cds_input_path` variables to `beta_lactam_*` throughout so nothing beta-lactam-specific is still OXA-labeled.

```python
from Bio.Seq import Seq

beta_lactam_frame_ok = []
beta_lactam_frame_bad = []
for header, seq in read_fasta_records(beta_lactam_pooled_nt_path):
    seq = seq.upper()
    if len(seq) % 3 != 0:
        beta_lactam_frame_bad.append((header, 'bad_length', len(seq)))
        continue
    protein = str(Seq(seq).translate(table=11, to_stop=False))
    body = protein[:-1] if protein.endswith('*') else protein
    if '*' in body:
        beta_lactam_frame_bad.append((header, 'internal_stop', body.index('*')))
        continue
    beta_lactam_frame_ok.append(header)

print(f'{len(beta_lactam_frame_ok)} / {len(beta_lactam_frame_ok) + len(beta_lactam_frame_bad)} sequences passed frame-integrity checks')
if beta_lactam_frame_bad:
    print('Failures:', beta_lactam_frame_bad[:10])

# Write only the passing sequences forward for alignment.
beta_lactam_cds_input_path = os.path.join(BETA_LACTAM_POOL_DIR, 'betalactam_cds_input.fasta')
beta_lactam_frame_ok_set = set(beta_lactam_frame_ok)
with open(beta_lactam_cds_input_path, 'w') as out:
    for header, seq in read_fasta_records(beta_lactam_pooled_nt_path):
        if header in beta_lactam_frame_ok_set:
            out.write(f'>{header}\n{seq}\n')

print(f'{len(beta_lactam_frame_ok)} sequences written to {beta_lactam_cds_input_path} for alignment')
```

## Code: MAFFT alignment for the pooled beta-lactam CDS (fixes two leftover-OXA bugs)

Two bugs in the pasted version: (1) the output **filename** was still `'oxa_cds_aligned.fasta'` even though the variable holding it was correctly renamed -- functionally harmless here since it's in a different folder than the OXA pilot's output, but misleading; (2) the final print statement referenced `oxa_aligned_records`, which is never defined in this snippet (the variable computed just above is `beta_lactam_aligned_records`) -- this would raise a `NameError` when run. `MAFFT_BIN` and `beta_lactam_cds_input_path` were already correct, reused as-is.

```python
import subprocess

betalactam_cds_aligned_path = os.path.join(BETA_LACTAM_POOL_DIR, 'betalactam_cds_aligned.fasta')
mafft_log_path = os.path.join(BETA_LACTAM_POOL_DIR, 'mafft.log')

with open(betalactam_cds_aligned_path, 'w') as out, open(mafft_log_path, 'w') as log:
    result = subprocess.run(
        [MAFFT_BIN, '--auto', '--thread', '16', '--inputorder', beta_lactam_cds_input_path],
        stdout=out, stderr=log, text=True,
    )
if result.returncode != 0:
    raise RuntimeError(f'MAFFT failed, see {mafft_log_path}')

beta_lactam_aligned_records = read_fasta_records(betalactam_cds_aligned_path)
alignment_lengths = {len(seq) for _, seq in beta_lactam_aligned_records}
print(f'MAFFT alignment complete: {len(beta_lactam_aligned_records)} sequences, alignment length {alignment_lengths}')
print(f'-> {betalactam_cds_aligned_path}')
```

## Error: FastTree "Non-unique name" -- root cause and fix

Real FastTree error (from `fasttree.log`, not the generic wrapper `RuntimeError`): `Non-unique name 'Escherichia' in the alignment`.

**Root cause:** FASTA/FastTree treat everything up to the *first whitespace* on a header line as the unique sequence name -- anything after a space is ignored as a comment. Some CARD gene names are free-text descriptions rather than short identifiers, e.g. `Escherichia coli soxS with mutation conferring antibiotic resistance`. The relabeling step built headers as `{gene_name}_{org}_{shortcode}_{index}`, so a gene name like that puts a space right after the first word -- FastTree truncates the whole header down to just `Escherichia`, discarding the org/shortcode/index suffix that was supposed to make it unique. Confirmed how widespread this is in the aligned file: 784 sequences collapse onto just 5 truncated names (475 `Haemophilus`, 236 `Klebsiella`, 58 `Escherichia`, 14 `Pseudomonas`, 1 `Acinetobacter`). MAFFT wasn't affected since alignment doesn't use header content, only FastTree's leaf-naming does.

**Fix -- no need to redo relabeling/CD-HIT/pooling/alignment**, just sanitize headers on the already-aligned file before tree-building (replace internal spaces with underscores so each header is one whitespace-free token), then build the tree from the sanitized copy:

```python
# Sanitize headers before tree-building -- see root-cause note above.
betalactam_cds_aligned_safe_path = os.path.join(BETA_LACTAM_POOL_DIR, 'betalactam_cds_aligned_safe.fasta')
with open(betalactam_cds_aligned_safe_path, 'w') as out:
    for header, seq in read_fasta_records(betalactam_cds_aligned_path):
        safe_header = header.replace(' ', '_')
        out.write(f'>{safe_header}\n{seq}\n')

print(f'Sanitized headers -> {betalactam_cds_aligned_safe_path}')

# Build the tree from the sanitized alignment.
cds_tree_path = os.path.join(BETA_LACTAM_POOL_DIR, 'cds_tree.nwk')
fasttree_log_path = os.path.join(BETA_LACTAM_POOL_DIR, 'fasttree.log')

with open(cds_tree_path, 'w') as out, open(fasttree_log_path, 'w') as log:
    result = subprocess.run(
        [FASTTREE_BIN, '-nt', '-gtr', betalactam_cds_aligned_safe_path],
        stdout=out, stderr=log, text=True,
    )
if result.returncode != 0:
    raise RuntimeError(f'FastTree failed, see {fasttree_log_path}')

print(f'FastTree complete -> {cds_tree_path}')
```
