# [SNVformer: An Attention-based Deep Neural Network for GWAS Data](https://www.biorxiv.org/content/10.1101/2022.07.07.499217v2.full.pdf)

This paper was released in July 2022 as part of the *2022 ICML Workshop on Computational Biology*

**Authors**: Kieran Elmes, Diana Benavides-Prado, Neset Ozkan Tan, Trung Bao Nguyen, Nicholas Sumpter, Megan Leask, Michael Witbrock, Alex Gavryushkin.

**Why am I reading it**: I want to see what existing methods are available for representation learning of GWAS data.

## Notes

#### Abstract

- GWAS are limited by their reliance on simple models of genetic effects
- Epistasis-aware models are more complex, the scale of GWAS data is too much for them
  - Epistasis: The effect of a gene mutation is dependent on the presence or absence of mutations in one or more other genes.
- They use a Transformer architecture to allow epistasis-aware models to operate on GWAS scale 
- Demonstrate prediction of gout from SNVs using their architecture
- Achieve competitive performance

#### 1. Introduction

- Most GWAS assume the effect of each SNV on the phenotype are independent, meaning they are unable to model epistasis
- Most GWAS use additive model, meaning they do not account for dominance effects
- They present a Transformer-based architecture for GWAS data
    - Uses a SNV encoder which can model gene interactions
    - Can also model complex multidimensional phenotypes
    - Scales to size of standard GWAS data
- Trained on UK Biobank with ~13000 data points
    - Input is genotyped SNV data, output is patient gout diagnosis
- Since vanilla transformer scales quadratically, they build upon the Linformer model, which reduces the attention calculation to linear time

#### 2. Methodology

- SNV data for ~487000 individuals from UK Biobank
    - ~870000 SNVs measured for each, 90 million imputed
- Data preprocessing:
    
    1. Get sequence of SNVs for each person, along with their genome position 
    2. Build a map from each SNV to an integer
    3. Apply this map, end up with sequence of integers for each SNV sequence
    

#### 3. Results

#### 4. Discussion and Work in Progress

