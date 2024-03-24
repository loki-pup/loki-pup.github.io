---
title: Image Similarity Learning with Siamese Network - Totally-Looks-Like challenge
teaser: cv, image similarity learning
category: Python
tags: [computer vision]
---

Totally-Looks-Like challenge can be used to evaluate human-like perception of images. This task is based on [https://www.reddit.com/r/totallylookslike/](https://www.reddit.com/r/totallylookslike/), where users share pairs of images of things that they think look similar. 

In this task, one image is taken from a Totally-Looks-Like pair as input and its match will be selected from a list of possible candidates. It is a challenging task because these image pairs might contain similar colours, shapes, textures, poses, or facial expressions, but they are not semantically related, and sometimes only part of the image is relevant to the comparison.

To solve this task, I proposed two methods: Siamese Network with a triplet loss, Siamese Network with a contrastive loss, achieving 83% and 84% accuracy.