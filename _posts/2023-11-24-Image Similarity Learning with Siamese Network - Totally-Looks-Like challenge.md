---
title: Image Similarity Learning with Siamese Network - Totally-Looks-Like challenge
teaser: computer vision, image similarity learning
category: Python
tags: [computer vision]
---

Totally-Looks-Like challenge can be used to evaluate human-like perception of images. This task is based on [https://www.reddit.com/r/totallylookslike/](https://www.reddit.com/r/totallylookslike/), where users share pairs of images of things that they think look similar. 

In this task, one image is taken from a Totally-Looks-Like pair as input and its match will be selected from a list of possible candidates. It is a challenging task because these image pairs are semantically unrelated but visually similar, they might contain similar colours, shapes, textures, poses, or facial expressions, and sometimes only part of the image is relevant to the comparison.

To solve this task, I proposed two methods: Siamese Network with a triplet loss; Siamese Network with a contrastive loss, achieving 83% and 84% accuracy.

## Siamese Network with a contrastive loss

Siamese networks are neural networks that contains multiple identical sub-networks, and these sub-networks share the same architecture and weights. Each sub-network processes a different input and produces an embedding vector for further training. And the reason why we choose the Siamese network is that it has proven to be particularly helpful in supervised similarity learning for the small dataset.

### Classification Task

If we view the problem as a classification task, we can calculate the probability of the image pair being the Totally-Looks-Like image pair. During the training stage, we label the TLL image pairs as 1 and other random matched image pairs as 0, and then train the neural network to identify the real image pair.

### Network Architecture

<img src="https://raw.githubusercontent.com/loki-pup/lokiphoto/master/left image2.png" width="100%" height="100%" />

Contrastive loss tries to minimize the distance when images are from the same class but maximizes the distance otherwise. It takes three inputs: "left" image (x1), "right" image (x2) and label (y), and the 0 or 1 label indicates whether the images are from the same class. The contrastive loss function is defined as:

<img src="https://raw.githubusercontent.com/loki-pup/lokiphoto/master/eq1.png" width="100%" height="100%" />

I combine the Siamese network and the classification network into a full network and use contrastive loss as the loss function. The inputs of the Siamese network are extracted features from the pretrained model, after processed by the deep network, the outputs of the Siamese network are then used as the inputs of the classification network. The classification network then returns the probability of the image pair being Totally-Looks-Like image pair. In this model, the deep network is a fully-connected laye

## Siamese Network with a triplet loss

### Image Retrieval Task

If we involve the challenge in an image retrieval task, we can calculate the similarity between the "left" image and the "right" candidate image, so the higher similarity indicates the candidate image is more likely to be the ground truth image.

### Network Architecture

<img src="https://raw.githubusercontent.com/loki-pup/lokiphoto/master/image.png" width="100%" height="100%" />

Triplet loss is a distance based loss function, it takes three inputs: anchor image (a), positive image (p) and negative image (n), and aims to minimize the distance between the same class images while maximizing the distance between the images from different classes. The anchor image and the positive image are from the same class, while the negative image is from a different class. The triplet loss function is defined as:

<img src="https://raw.githubusercontent.com/loki-pup/lokiphoto/master/eq2.png" width="100%" height="100%" />

$D(x, y)$ is the distance between x and y, and often implements L2 distance or (1 - cosine similarity).

I first extract the features of the images from the pretrained model as the inputs of the Siamese network. Then in the deep network part, I simply add a fully-connected layer to reduce the input by half. And the outputs of the deep network are used to calculate the triplet loss. 

Therefore, the deep network is trained to produce the prefect representation of the images for similarity learning. During the prediction stage, Is can simply calculate the cosine similarity between the deep network outputs of different images, and treat it as the probability of the image pair being Totally-Looks-Like image pair.