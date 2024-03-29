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
evidence list is set to 5. But the first step is to reduce the range of evidence passages by prematching the claim text with evidence.

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

BERT model is trained on the dataset, and the model will be saveevery epochd  if the development accuracy achieves improvement. And the prediction returns the probability of each claim-text, evidence candidate pair. Then similar to SBERT, from the returned highest 5 probabilities, the first threshold is 0.8 and the second threshold is 0.5. If none of the threshold is met, the evidence with highest 5 probabilities are returned.