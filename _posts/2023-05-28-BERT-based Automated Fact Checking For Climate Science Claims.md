---
title: BERT-based Automated Fact Checking For Climate Science Claims
teaser: natural language processing, evidence retrieval, claim-label classification
category: Python
tags: [NLP, sentence classification]
---

This task is to develop an automated fact-checking system, where given a claim, the goal is to find related evidence passages from the corpus, and classify whether the claim is supported by the evidence. The status of the claim can be classified in the following 4 classes: {SUPPORTS, REFUTES, NOT_ENOUGH_INFO, DISPUTED}.

Based on BERT model evidence retrieval is treated as sentence pairs classification problem, while claim-label classification is
solved as a single sentence classification problem.

## BERT & SBERT

Bidirectional Encoder Representations from Transformers (BERT) implements the masked language model for the next sentence prediction task. It also achieves fast training with the transformer architecture. BERT also provides two models: BERTBASE and BERTLARGE. In this project, I use BERTBASE.

Siamese BERT (SBERT) is developed based on BERT. It removes the final classification head and uses siamese architecture. Unlike BERT, SBERT can be directly used to measure the similarity between two sentences by calculating the cosine similarity score.

## Evidence Retrieval

After searching the training dataset, it appears that the maximum evidence list for claim-text is 5, so the limit of the final
evidence list is set to 5. But the first step is to reduce the range of evidence passages by prematching the claim text with evidence, since the evidence corpus has 1208827 evidence.

### Prematch Routine

The prematch procedure has 5 steps:

1. Tokenize each claim-text and evidence passage, remove the stop words and repeated words. Optional choice: lemmatize the words before removing the repeated words.
  
2. Store the processed words into keywords list. 

3. Perform the first prematch: if a keyword appears both in the keyword lists of claim-text and evidence passage, then store this evidence passage into the prematch evidence list of this claim-text.

4. Based on the prematch evidence list, do another round of keywords match, but this time the matched keywords are increased by 1. That is only adding the evidence into the list when 2 keywords appear in both of the keyword lists.

5. Repeat step 4, until the prematched evidence passages list is smaller than 100 or close to 100 for each claim-text. 

### SBERT

SBERT is used to calculate the cosine similarity score between claim-text and each evidence passage, and to return the evidence passages of top 5 scores for each claim-text. The score's threshold for selecting the evidence is first set to 0.7, but if there is no evidence with score > 0.7, the threshold will be updated to 0.5. If there is no evidence with score > 0.5, the list of the top 5 score evidence passages will be returned.

### BERT

To implement BERT model, the evidence retrieval is treated as a sentence pairs classification problem. The first sentence is the claim-text, while the second sentence is the evidence candidate. So during the preprocessing stage, the pair of claim-text and real evidence is labelled as 1, while the pair of claim-text and evidence candidate from the prematch evidence list is labelled as 0. Then the data will be shuffled when the datasets are created by dataloader, since the training data are stored sequentially.

BERT model is trained on the dataset, and every epoch the model will be saved if the development accuracy achieves improvement. In the prediction stage it returns the probability of each claim-text, evidence candidate pair. Then similar to SBERT, among the returned highest 5 probabilities, 0.8 and 0.5 are set as the first and the second threshold. If none of the threshold is met, the evidence passages of the highest 5 probabilities are returned.

### Evaluation

F-score is used here to measure how well the retrieval component works. The results of SBERT and BERT models evaluated on the development datasets are around 0.2 and 0.49. From the evidence retrieval F-scores, it's obvious that BERT performs much better than SBERT. Although SBERT is much faster and more efficient, the lack of model training makes it a general model and leads to the bad performance, as the claim-text and evidence are about climate science, while SBERT is trained on a general corpus. With lemmatization, the F-score increases by 0.023, indicating an improvement, since the prematch process is more accurate with the lemmatizied words.

## Claim-label Classification

To solve this multiclass classification problem, I implement BERT model in 4 different settings based on the following 2 questions. Should the claim-text and the evidence be treated as sentence pairs or a single sentence? Should all the evidence for a single claim-text be concatenated and treated as a sentence or treated independently as a single sample?

### Preprocessing

First, for each claim-text, I concatenate all the evidence and treat it as a single sentence. Then, I make two versions when encoding the data. One has the seg-ids for sentence pairs classification, while the other one concatenates the claim-text and the evidence as a single sentence.

Second, for each claim-text, each evidence is processed separately. And it also has two versions, with or without seg-ids. Due to the sequentially stored data, shuffling is required. 

### BERT

When implementing the model, I also try to add a dropout layer to prevent the model overfits. The prediction returns the claim-label of each claim-text. When the evidence is not concatenated, I group the data by the claim-text, count the number of each claim-label and select the claim-label with the most votes.

### Evaluation

Accuracy is used to assesses how well the system classifies the claim.

| Evidence concatenated?   | Single sentence or pairs?   | Dropout   | Accuracy |
| ------------------------ | --------------------------- | --------- | -------- |
| yes                      | pairs                       | no        | 0.33     |
| yes                      | single                      | no        | 0.37     |
| no                       | pairs                       | no        | 0.55     |
| no                       | single                      | no        | 0.52     |
| no                       | single                      | yes       | 0.5      |

It's clear that the model has the best performance when the claim-text is concatenated with its each evidence and treated as a single sentence.

The worst performance goes to the model with concatenated evidence. Since the training dataset has only 1228 claims, if the evidence are concatenated, the processed training dataset also has only 1228 claims, which makes the model prone to overfitting. But with single evidence, the training dataset has around 5000 claims and can provide more valid data to the model. That also explains why the model performs better with single evidence.

Although the evidence are relevant to the claim-text, the claim-text and evidence shouldn't be concatenated and treated as a single sentence, as it can confuse the model with partially similar sentences and lead to lower accuracy.