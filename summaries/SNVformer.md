# [SNVformer: An Attention-based Deep Neural Network for GWAS Data](https://www.biorxiv.org/content/10.1101/2022.07.07.499217v2.full.pdf)

This paper was released in July 2022 as part of the *2022 ICML Workshop on Computational Biology*

**Authors**: Kieran Elmes, Diana Benavides-Prado, Neset Ozkan Tan, Trung Bao Nguyen, Nicholas Sumpter, Megan Leask, Michael Witbrock, Alex Gavryushkin.

**Why am I reading it**: I want to see what existing methods are available for representation learning of GWAS data.

## Notes

### Paper

#### Abstract

- GWAS are limited by their reliance on simple models of genetic effects
- Epistasis-aware models are more complex, the scale of GWAS data is too much for them
  - Epistasis: The effect of a gene mutation is dependent on the presence or absence of mutations in one or more other genes.
- They use a Transformer architecture to allow epistasis-aware models to operate on GWAS scale 
- Demonstrate prediction of gout from SNVs using their architecture
- Achieve competitive performance

#### 1. Introduction

- Most GWAS assume the effect of each SNV on the phenotype are independent, meaning they are unable to model epistasis
- Most GWAS use an additive model, meaning they do not account for dominance effects
- They present a Transformer-based architecture for GWAS data
    - Uses a SNV encoder which can model gene interactions
    - Can also model complex multidimensional phenotypes
    - Scales to size of standard GWAS data
- Trained on UK Biobank with ~13000 data points
    - Input is genotyped SNV data, output is patient gout diagnosis
- Since vanilla transformer scales quadratically, they build upon the Linformer model, which reduces the attention calculation to linear time

#### 2. Methodology

##### Data

- SNV data for ~487000 individuals from UK Biobank
    - ~870000 SNVs measured for each, 90 million imputed
- Data preprocessing
    1. Get sequence of SNVs for each person, along with their genome position 
    2. Build a map from each SNV to an integer
    3. Apply this map, end up with sequence of integers for each SNV sequence

- Each SNV has two components: Its position on the genome and its alleles
    - They use a method for representing which alleles are present which requires only 32 tokens to represent all possible allele combinations. Thus every SNV can be tokenized to an integer from 0 to 31.
        - They do this by representing long sequences as one character, refer to the paper to see what this means
- A subset of SNVs is used for the gout classification task
    - Only use non-imputed SNVs from the genotypes data which have a minimum allele frequency of $10^{-4}$
    - They also exclude SNV values which score below $10^{-6}$ with the Hardy-Weinberg equilibrium exact test
        - The HWE exact test is used to determine which SNVs may be different from their expected frequency due to population structure, and not because of natural genetic variation. 
    - Of the remaining SNVs plink1.9 is used to find those associated with urate using linear regression (p-value $10^{-1}$). This is because gout is primarily caused by high serum urate levels.
    - End up with around 66000 SNVs to feed to the model
- They also provide the model with the age, sex, and BMI of all individuals
- The dataset for training and evaluation of the model had approximately 9000 gout and 9000 non-gout samples. 70% are in training set, 25% in test set, 5% held out.

##### Transformer Network Architecture
- Each SNV is mapped to one of the 32 tokens and one-hot encoded. This embedding is then concatenated to a sine/cosine positional encoding of length 64. This makes the input embedding vector 96 elements in size for each token. Since there are 66000 SNVs this makes the input length very long.
- This input embedding is then passed into a Transformer encoder
    - Self-attention is performed using the Linformer architecture. This is necessary because of the very long input length.
    - 6 layers, 4 attention heads per layer
- The first token of the encoder's output is used as the classification token, whose output is passed to a fully connected linear layer used for classification using Softmax.


#### 3. Results
- When the model is confident with its prediction, it tends to be more likely to be accurate. ~80% accuracy when Softmax output is <0.1 or >0.9.
- Model achieves an AUROC score of 0.83

#### 4. Discussion and Work in Progress
- This naive approach still achieves accuracy similar to top methods (0.84)
- There are different methods for incorporating long term dependencies which might be better than Linformer, they mention Memorizing Transformers and Perceiver IO
- They want to use attention visualization to determine which SNVs are important for gout prediction
- They want to incorporate pretraining into their system and use data from the entire UK BioBank

--- 
### [ICML 2022 Workshop Presentation Video](https://icml.cc/virtual/2022/workshop/13464#wse-detail-20833)

- 45-73% of gout risk should be heritable/explainable from ones genetics
    - But using linear models like in GWAS, only 7-41% is explained
- Want to use a transformer because can show relation between SNV of arbitrary distance from one another, and because it encodes position of each SNV on the genome
- They used only ~66000 SNVs instead of full ~800000 which were measured because even using Linformer their sequence is too long to fit in memory
- Without providing model with age, sex, and BMI they have prediction accuracy of 59% (0.63 ROC, SOA is also 0.63)
    - When they are provided prediction accuracy is 75% (0.83 AUROC, SOA is same)
- In the video he says they did pretraining, but in paper not mentioned.
