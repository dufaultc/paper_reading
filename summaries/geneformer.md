# [Transfer learning enables predictions in network biology](https://www.nature.com/articles/s41586-023-06139-9)

Nature paper published May 31 2023. Most authors affiliated with the Broad Institute.

[Twitter thread from first author](https://twitter.com/TheodorisLab/status/1664284672711593984)

[Model](https://huggingface.co/ctheodoris/Geneformer)

[Pretraining Data](https://huggingface.co/datasets/ctheodoris/Genecorpus-30M)


**Authors**: Christina V. Theodoris, Ling Xiao, Anant Chopra, Mark D. Chaffin, Zeina R. Al Sayed, Matthew C. Hill, Helene Mantineo, Elizabeth M. Brydon, Zexian Zeng, X. Shirley Liu, Patrick T. Ellinor

**Why am I reading it**: I want to explore different methods of learning the genome. Alan recommended the paper based on their method of training their attention-based model, where they rank the genes in single-cell transcriptomes by their expression level, and use that as their model input.

## Notes

### Paper

#### Abstract

You need a lot of transcriptomic data to learn the connections between genes, which is a problem when trying to understand diseases which are rate or which are found in difficult to sequence tissues. They pre-trained an attention-based model called Geneformer on 30 million single-cell transcriptomes. They fine-tuned the model for a bunch of different tasks and consistently saw improvements upon existing methods.

#### Introduction

- If you map the gene regulatory networks driving disease progression, you can find molecules that correct the networks. But doing this mapping requires a lot of transcriptomic data.
- The amount of available transcriptomic data, especially single-cell data, has expanded rapidly in recent years.
- Transfer learning allows for a model to be created which already has learned fundamental knowledge of the problem domain from a pre-training training step, and is thus able to be more easily fine-tuned for related tasks requiring similar knowledge as the pre-training task.
- Context-aware, attention-based models like transformers are well-suited for modeling systems where the function/meaning of elements in the system varies based on which other elements are present and how they are connected.
- They created a corpus of ~30 million human single-cell transcriptomes and pre-trained Geneformer (their transformer-based model) using an MLM task.
- Geneformer learned a lot of transferrable knowledge from pre-training. Upon task-specific finetuning, it can improve predictive accuracy on many tasks related to chromatin and network dynamics.   
- After finetuning, can predict dosage sensitive (gene expression highly affected by changes in the copy number of the gene) disease genes and their downstream targets (those genes whose expression is regulated by changes in the expression of some gene of interest). After a different finetuning task, Geneformer was successfully used to find therapeutic targets for the disease cardiomyopathy.
 
#### Geneformer Architecture and Pre-training

- Geneformer is an attention-based model pretrained on single-cell transcriptomic data from ~30 million cells from a wide array of human tissues
- They did not include malignant cells. Also attempted to remove doublets and damaged cells.
- For each cell in the dataset, genes have their expression normalized by that genes expression across all cells in the data, and then for each cell the genes are ranked by their expression level. This normalization is important for making housekeeping genes lower ranked, and genes important for determining cell state (like transcription factors) higher ranked.
  - One of the benefits of simply ranking genes, instead of using their actual expression values, is that technical artefacts in sequencing shouldn't bias gene rankings as much as expression levels. Essentially, if a cell were sequenced with multiple different technologies, we would expect the gene ranking obtained from each sequencing to be more similar than the actual expression levels.
- The input to the model is a sequence of ranked genes from a given cell.
- The model itself is 6 stacked transformer encoder units.
- MLM was used as the pretraining task, with 15% of genes in each transcriptome being masked.

#### Context Awareness and Batch Integration

- For each cell, every gene is embedded into a 256 dimensional contextual vector
  - Gene embeddings are not affected by the technical artefacts which generally cause differences between different batches of cells in single-cell sequencing. 
  - They verified that the gene embeddings are indeed dependent on the rankings of other genes in the given cell. They found that highly context-dependent genes have more variability in their gene expression between different cells than housekeeping genes.
- They combined gene embeddings from the pretrained model to generate cell embeddings, and verified that they clustered by cell type and phenotype, not by patient.
  - Even after the model was finetuned using only data from one sequencing platform, cell embeddings (including for cells from other platforms) still clustered primarily by cell type. The embeddings were better integrated than the orginal data, even after batch effect removal tools were applied to it.
  - These cell embeddings were used to assess Geneformer's usefulness for cell-type prediction tasks. They found that Geneformer performed better than alternative methods, especially as the number of classes to select between increased. 

#### Gene Dosage Sensitivity Predictions

- They finetuned Geneformer to predict which transcription factors are dosage sensitive, with results significantly better than alternative methods.
  - This finetuned model successfully identified genes known with high or medium confidence from a previous study to be associated with neurodevelopmental disease upon their deletion. 96% of the high confidence known genes were identified by the model as dosage sensitive in fetal cerebral cells.
  - With the medium confidence genes, the model identified them as dosage sensitive at a higher rate in fetal cerebral cells than other cell types. This is good because it is primarily in this cell type we expect these genes to be dosage sensitive.
- Longer pretraining resulted in better prediction results even when the amount of finetuning data stayed constant.
- They measured the effect of deleting genes from rankings on the embeddings given by the pretrained model. In cardiomyocytes, deletion of structural heart genes had a significantly larger affect on the embeddings of the remaining genes than other heart disease related genes which affect other cell types.

#### Chromatin Dynamics Predictions

- A finetuned Geneformer was significantly better at predicting if genes were bivalently marked than alternative methods, and the prediction capabilities were generalizable to regions of the genome not used in the finetuning.
- A finetuned Geneformer could predict which transcription factors are long range versus short range acting, significantly better than alternative methods (their predictions were almost random).
  - Predicting this just from transcriptomes is really cool!
  - Fun fact: 0% of genes in the genome code for transcription factors. They are the largest family of human proteins.

#### Network Dynamics Predictions

- After finetuning, Geneformer could identify central genes in the NOTCH1 gene network using only transcriptional data from 30000 cells. Central genes/factors are those whose correction restored the network to normal state state.
- Finetuning also allowed Geneformer to distinguish downstream targets of the NOTCH1 network from non-targets.

#### Pre-training Encoded Network Hierarchy

- They looked at the attention weights of the pretrained Geneformer model.
- For aortic endothelial cells, 20% of their 24 attention heads (so I guess 5 of them) significantly attended transcription factors more than other genes. Specific attention heads significantly paid more attention to central regulatory nodes in the Notch1 gene network than peripheral genes.
  - When applied to a variety of cell types, the central regulatory attention heads significantly attended more to the highest ranked genes of each cell.
- Early layer attention heads attended to distinct gene ranks from one another, middle layer heads attended broadly, and final layer heads attended to the highest ranked genes that uniquely define cell states.

#### In Silico Gene Network Analysis

- They looked at the genes in fetal cardiomyocyte cells whose gene embeddings (taken from the pretrained Geneformer) were most changed by the removal of a known heart disease gene. Genes whose embeddings were most affected by the deletion of the heart disease gene were those known to be most significantly dysregulated by variations in the heart disease related gene. They say this means you can use *in silico* perturbation of transcriptomes given to the model to understand gene network connections.
- They found that the sum of the separate effects deleting two TF known to bind together on the embeddings of the downstream cobound targets of the genes, was less than the effect on the embeddings of the targets when both were deleted at the same time. They say this suggests Geneformer understands the two TF bind together to affect the targets.

#### In Silico Treatment Analysis

- Wanted to test if their perturbation strategy from the last section could be used to find candidate therapeutic targets of human disease.
- They finetuned Geneformer to distinguish cardiomyocyte cells from hearts with a certain disease from those which were non-failing.
  - They then found which genes when activated or deleted caused the gene emebeddings from non-failing heart cells to move towards those from diseased heart cells. They found close to 1000 genes which did this.
- They then looked at whether they could *in silico* activate or inhibit gene pathways in the cell gene ranks which would shift the gene embeddings from diseased cells towards the embeddings from the non-failing cells.
  - I can't tell if this was a success or not. I don't think they say if the pathways they used to identify targets were found using this approach.
- They experimentally validated that two therapeutic targets predicted by Geneformer improved function of cardiomyocyte cell function. Used CRISPR to knockout the two genes and saw what they were looking for.

#### Discussion

- Using Geneformer to predict dosage-sensitive genes could be useful for understanding which GWAS hits to care about when trying to understand the drivers of complex traits. Would also be useful for understanding which tissues likely to be affected by the given variation.
- As the amount of publicly available transcriptomic data increases, Geneformer should continue to improve

### Methods

Im only going to note stuff mentioned in this section they didn't say earlier.

#### Pretraining Corpus Building

 - They filtered out cells with total read counts and percentage of mitochondrial counts outside 3 SD from the mean in the dataset. After this filtering they had 27.4 million cells remaining from the original 29.9 million
 - When calculating the median expression of a gene for the purposes of normalization, they did not include 0 values. They say the reason for this is to not weight the value by the tissue representation of their dataset, which I don't really understand.
- They had a vocabulary of 25424 protein-coding or miRNA genes

#### Geneformer Architecture and Pretraining

- Model input size was 2048, which was large enough for 93% of rank value encodings in the corpus. Model embedding size was 256, feed forward layer size of 512.
- They used pytorch and Huggingface Transformers.
- To make training more efficient they used the trainer from the Huggingface Transformers library which does a lot of make training more efficient.
- They performed distributed GPU training using Deepspeed.
- Training took around 3 days using 3 compute nodes with 4 32GB GPUs each.

#### Geneformer Finetuning

- Finetuning objective were always either gene classification or cell classification
- They used the same finetuning hyperparameters for all tasks. Since tuning hyperparameters for each task would improve performance of the model, they likely underestimated the performance of Geneformer. Also only used one training epoch when finetuning.
- For most of the finetuning tasks they froze no layers, except for the dosage sensitivity and long versus short range TF tasks, where they froze 4 layers.

#### Gene Embeddings, Cell Embeddings, and Attention Weights

- Gene embeddings were extracted from the 2nd last layer of the model, as the last layer can have features more related to the pretraining task.
- Cell embeddings are the average of the embeddings of each gene (from 2nd last layer) in the cell

#### In Silico Perturbation

- They measured the difference between cell and gene embeddings before and after they performed perturbation (which was done by removing a given gene from the cells gene ranking) using cosine similarity.
- In silico activation was done by moving the given gene to the front of the gene ranking.




