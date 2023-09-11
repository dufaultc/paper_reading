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
  - These cell embeddings were used to assess Geneformer's usefulness for cell-type annotation tasks. They found that Geneformer performed better than alternative methods in different tissues.


#### Gene Dosage Sensitivity Predictions

#### Chromatin Dynamics Predictions

#### Network Dynamics Predictions

#### Pre-training Encoded Network Hierarchy

#### In Silico Gene Network Analysis

#### In Silico Treatment Analysis

#### Discussion

### Methods






