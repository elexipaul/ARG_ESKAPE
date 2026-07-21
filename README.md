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

## Usage
Run the Jupyter Notebook cells in sequential order.

## Git version control : 

