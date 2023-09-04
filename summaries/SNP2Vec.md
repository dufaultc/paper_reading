# [SNP2Vec: Scalable Self-Supervised Pre-Training for Genome-Wide Association Study](https://arxiv.org/pdf/2204.06699.pdf)

This paper was released on Arxiv in April 2022 and presented at the ACL 2022 21st Workshop on Biomedical Language Processing.

**Authors**: Samuel Cahyawijaya, Tiezheng Yu, Zihan Liu, Tiffany T.W. MAK, Xiaopu Zhou, Nancy Y. Ip, Pascale Fung

**Why am I reading it**: I want to see what existing methods are available for representation learning of GWAS data.

## Notes

### Paper

#### Abstract
- Self-supervised pre-training on genomic sequences has not focused on understanding genetic variation, since its only applied to haploid sequences.
- They introduce SNP2Vec, which is a "scalable self-supervised pre-training approach for understanding SNP".
- They use SNP2Vec for Alzheimer's disease risk prediction, outperforming existing methods.

#### 1. Introduction
- Pre-trained genomic models only have language of A, C, T, and G. Don't model genomic varation.
    - This is fine for tasks like promoter region identification, but bad for tasks like GWAS that require understanding of long genomic sequences and the variation between chromosome pairs.
- Their proposed self-supervised pre-training approach uses a SNP database to inject genomic variations during pre-training on genomic sequences.
- They use SNP2Vec with a Linformer model.
- They compare against performance of non-pretrained models and polygenic risk scoring model.
- Their Contributions:
    - First scalable self-supervised pre-training approach to learn genomic variations
    - Model sequences larger 20000 bp with attention
    - Demonstrate how SNP2Vec outperforms all baselines
    - Compare their tokenization method's effectiveness to others


#### 2. Related Works
##### 2.1 GWAS
- Predicting risk of hereditary diseases from an individuals genotype is done using GWAS. A polygenic risk score is determined from the GWAS data by looking at SNVs within a range of a gene of interest. From these SNVs a classifier is built which can quantify an individuals risk from any given genotype.
- Doesn't actually use information from the underlying genomic sequences, just the variations.

##### 2.2 Statistical Modeling for Genomics
- Nothing very notable in this section

##### 2.3 Self-Supervised Pre-training
- They pre-train on the human reference genome

#### 3. SNP2Vec
- BigBird and DNABERT are two existing pre-training methods in genomics. Both only trained to understand haploid sequence from the reference genome.
- SNP2Vec is pre-training method for understanding patterns of genomic variation in diploid sequences.
- Three main components to SNP2Vec:
    - SNP encoding
    - Self-supervised SNP dataset
    - Self-supervised SNP pretraining
    

#### 4. Experiment Settings

#### 5. Results

#### 6. Discussion

#### 7. Conclusion

#### 8. Future Work


