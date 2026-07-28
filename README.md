# ESKAPE Pathogen AMR Gene Family Profiling

## Overview
This pipeline processes Antimicrobial Resistance (AMR) gene data for ESKAPE pathogens. It parses RGI (Resistance Gene Identifier) output files, extracts unique AMR Gene Families (handling multi-class semicolon-separated strings), and generates a unified Presence/Absence matrix for downstream heatmap visualization.

## Directory Structure
*   CARD_Results : Contains the results from RGI run in this folder, there are 6 files of 6 ESKAPE Pathogens. 
*   Results : This Folder contains MOB suite results,  Drug class mapping ,
## Genefamily: Extraction of the ARG Gene families, organised into 6 folders, in each folders there are .fasta files, each fasta files is named as the ARG gene family names.  
## Metadata : This folder contains the metadata of the gene-families corresponding to 6 folders of 6 ESKAPE pathogens, in each folder there are .csv files which contains the following column headers AMR gene name	Start	Stop	Contig id	Drug class
  
## DRUG_class_family_mapping : This folder contains 6 drug_class_mapping.csv with 2 column headers : Drug class ; AMR GENE FAMILES ; Multiple Drugs class corresponding to gene families

* ESKAPE_AMR_Presence_Absence_Matrix.csv : This file has been created by taking the AMR gene  family column from each of the six .csv files and creating a 0/1 matrix , 1st row are the AMR gene families , 1st column is the ESKAPE pathogens. 
## Pipeline Steps
1.  **ARG_Family_classification.ipynb** : This notebook does the extraction and creation of the ARG gene families, Metadata and Binary matrix preparation.
2.  **ARG_Chromosome_Plasmid_Mapping.ipynb** : This notebook does the merging of AMR genes which are on chromosome and plasmid, there was bug in this notebook due to which it didnot classify the plasmids as mobilizable and non mobilizable conjugative and non conjugative plasmids due to which another notebook **Mobsuite_Chromosome_Plasmid_Mobility.ipynb** was used whose results were used by **ARG_Chromosome_Plasmid_Mapping.ipynb** notebook to merge and classify
## Usage
Run the Jupyter Notebook cells in sequential order.
# Added file Mobsuite_Chromosome_Plasmid_Mobility.ipynb
** This Notebook classifies the plasmid from the 6 ESKAPE pathogens into mobilizable and non mobilizable , conjugative and non conjugative plasmids
 
## Git version control : 
git add 
git commit -m ""
git push
## Check the commitment history
git log --oneline
git diff <older-hash> <newer-hash> -- <filename>

## List the files 
git ls-files 
## Branch
git switch -c feature_1/
## Switch to main
git switch main
## Add everything and push at once
git status              # see everything changed, across all files
git add .                # stage everything at once (respects .gitignore)
git commit -m "describe the overall change"
git push

# Date: 28-07-2026

## Classification of ARG across AMR Gene families for ESKAPE Pathogens

### ESKAPE_AMR_Gene_Family_Percent_of_Genes_Matrix_2.csv : Contains the matrix of AMR counts/ Total AMR gene count for each gene family
## ESKAPE_AMR_Gene_Family_Percent_of_Genes_Matrix_3.csv: was used to create the heatmap

## ESKAPE_AMR_Gene_Family_Percentage_Matrix.csv : There was a mistake in this file as the no of gene counts was divided by Total no of gene family.

