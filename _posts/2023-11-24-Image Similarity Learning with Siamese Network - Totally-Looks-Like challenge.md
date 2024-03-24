---
title: Image Similarity Learning with Siamese Network - Totally-Looks-Like challenge
teaser: cv, image similarity learning
category: Python
tags: [computer vision]
---

Totally-Looks-Like challenge can be used to evaluate human-like perception of images. This task is based on [https://www.reddit.com/r/totallylookslike/](https://www.reddit.com/r/totallylookslike/), where users share pairs of images of things that they think look similar. 

In this task, one image is taken from a Totally-Looks-Like pair as input and its match will be selected from a list of possible candidates. It is a challenging task because these image pairs might contain similar colours, shapes, textures, poses, or facial expressions, but they are not semantically related, and sometimes only part of the image is relevant to the comparison.

To solve this task, I proposed two methods: Siamese Network with a triplet loss; Siamese Network with a contrastive loss, achieving 83% and 84% accuracy.

## Siamese Network with a contrastive loss

Siamese networks are neural networks that contains multiple identical sub-networks, and these sub-networks share the same architecture and weights. Each sub-network processes a different input and produces an embedding vector for further training. And the reason why we choose the Siamese network is that it has proven to be particularly helpful in supervised similarity learning for the small dataset.

### Classification Task

If we view the problem as a classification task, we can calculate the probability of the image pair being the Totally-Looks-Like image pair. During the training stage, we label the TLL image pairs as 1 and other random matched image pairs as 0, and then train the neural network to identify the real image pair.

### Network Architecture

<img src="https://raw.githubusercontent.com/loki-pup/lokiphoto/master/left image2.png" width="100%" height="100%" />

Contrastive loss tries to minimize the distance when images are from the same class but maximizes the distance otherwise. It takes three inputs: "left" image (x1), "right" image (x2) and label (y), and the 0 or 1 label indicates whether the images are from the same class. The contrastive loss function is defined as:

<img href="https://latex.codecogs.com/svg.image?&space;L=(1-y)D(x1,x2)^2&plus;y(max(0,margin&space;D(x1,x2)))^2&space;" width="100%" height="100%" />
