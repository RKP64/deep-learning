# CLIP

📜 [Learning Transferable Visual Models From Natural Language Supervision](https://arxiv.org/pdf/2103.00020)

> We demonstrate that the simple pre-training task of predicting which caption goes with which image is an efficient and scalable way to learn SOTA image representations from scratch on a dataset of 400 million (image, text) pairs collected from the internet.

> After pre-training, natural language is used to reference learned visual concepts (or describe new ones) enabling zero-shot transfer of the model to downstream tasks.

> For instance, we match the accuracy of the original ResNet-50 on ImageNet zero-shot without needing to use any of the 1.28 million training examples it was trained on.

> Could scalable pre-training methods which learn directly from web text result in a similar breakthrough in computer vision?

Could a pre-training approach used by models like GPT-3 capture high-level knowledge just like it does with language modeling, except for images?

> In this work, we close this gap and study the behaviors of image classifiers trained with natural language supervision at large scale.

> Enabled by the large amounts of publicly available data of this form
> on the internet, we create a new dataset of 400 million (image, text) pairs and demonstrate that a simplified version of ConVIRT trained from scratch, which we call CLIP, for Contrastive Language-Image Pre-training, is an efficient method of learning from natural language supervision.

They train 8 different CLIP models with different orders of magnitude of compute, and again find a linearly scaling quality curve.

> We find that CLIP, similar to the GPT family, learns to perform a wide set of tasks during pre-training including OCR, geo-localization, action recognition, and many others.

> We also […] show that CLIP outperforms the best publicly available ImageNet model while also being more computationally efficient.

### Approach

**1. Natural Language Supervision**

> At the core of our approach is the idea of learning perception from supervision contained in natural language.

> It’s much easier to scale natural language supervision compared to standard crowd-sourced labeling for image classification since it does not require annotations to be in a classic “machine learning compatible format” such as the canonical 1-of-N majority vote “gold label”.

Natural language supervision is far easier to scale because text labels for images are abundant on the internet. Meanwhile, using labeling methods for classifiers makes the data collection process much longer since people need to manually fit images into 1-of-N classification labels.

> Learning from natural language also has an important advantage over most unsupervised or self-supervised learning approaches in that it doesn’t “just” learn a representation but also connects that representation to language which enables flexible zero-shot transfer.

Learning from natural language also means the model connects images to language with sufficient scale, rather than just being able to classify broadly what an image represents.

**2. Creating a Sufficiently Large Dataset**

> A major motivation for natural language supervision is the large quantities of data of this form available publicly on the internet.

Unlike previous datasets which are far smaller, the internet offers a huge amount of data available that could be used for natural language supervision

> To address this, we constructed a new dataset of 400 million (image, text) pairs collected form a variety of publicly available sources on the Internet.

They balance the dataset by searching for image, text pairs where the text contains specific queries (500,000) and then including 20,000 examples per query.

**3. Selecting an Efficient Pre-Training Method**

> State-of-the-art computer vision systems use very large amounts of compute. […] The task of learning an open set of visual concepts from
> natural language seems daunting.

Previous image models took many years of core compute time to train, and were only trained to predict 1000 ImageNet classes - this suggest the challenge of training image models.

> [Our initial approach tried] to predict the exact words of the text accompanying each image. This is a difficult task due to the wide variety of descriptions, comments, and related text that co-occur with images.

> Recent work in contrastive representation learning for images has found that contrastive objectives can learn better representations than their equivalent predictive objective.

> Noting these findings, we explored training a system to solve the potentially easier proxy task of predicting only which text as a whole is paired with which image and not the exact words of that text.

Because of how many ways to describe an image there are (a picture is worth a thousand words), creating a model specifically to predict the exact words that describe an image in the training set is extremely difficult. The training task itself would not even be possible for humans, which is a good proxy to understanding its difficulty.

> Given a batch of $N$ (image, text) pairs, CLIP is trained to predict which of the $N \times N$ possible (image, text) pairings across a batch actually occurred.

Instead, they train the model with batches of $N$ images and $N$ text pairings, and the model has to match up the correct images to the correct text.

> To do this, CLIP learns a multi-modal embedding space by jointly training an image encoder and text encoder to maximize the cosine similarity of the image and text embeddings of the $N$ real pairs in the batch, while minimizing the cosine similarity of the embeddings of the $N^2 - N$ incorrect pairings.

> [This] objective was first introduced […] as the _multi-class N-pair loss_.

This objective function is the entire reason why CLIP works as an text-to-image embedding that understands both!

The model is built with two encoders, where one learns to compress text into an embedding space, and the other which learns to compress images into the same embedding space.

It’s ability to minimize it’s objective function comes from it’s ability to compress the correct text labels for each image and the image themselves into similar places in the embedding space while making different images farther apart.

> Due to the large size of our pre-training dataset, over-fitting is not a major concern and the details of training CLIP are simplified.

The dataset is so big and the data so complex that overfitting to the noise of the dataset is unlikely.

**4. Choosing and Scaling a Model**

For the image encoder, they try both the Vision Transformer (ViT) and the most recent ResNet-50 model (with an attention pooling mechanism instead of the global average pooling layer)

> The attention pooling is implemented as a single layer of “transformer-style” QKV attention where the query is conditioned on the global average-pooled representation of the image.

For the text encoder, they use a Transformer similar in structure to GPT-2

> Masked self-attention was used in the text encoder to preserve the ability to initialize with a pre-trained language model or add language modeling as an auxiliary objective, though exploration of this is left as future work.

They keep the architecture similar just for convenience/keeping the possibility of potential weight initialization.

**5. Training**

They trained 5 ResNets and 3 Vision Transformers of different scales.

> We use the Adam optimizer with decoupled weight decay regularization applied to all weights that are not gains or biases, and decay the learning rate using a cosine scheduler. Initial hyper-parameters were set using a combination of grid searches, random search, and a manual tuning.

> We use a very large mini-batch size of 32,768

This helps to make the (image,text) pairing task far more robust as the model has to distinguish pairs from a very large set (but still makes it far easier than guessing the words from nothing)

### Experiments

**1. Zero-Shot Transfer**

> In computer vision, zero-shot learning usually refers to the study of generalizing to unseen object categories in image classification. We instead use the term in a broader sense and study generalization to unseen datasets. We motivate this as a proxy for performing unseen tasks.

> While much research in the field of unsupervised learning focuses on the _representation learning_ capabilities of machine learning systems, we motivate studying zero-shot transfer as a way of measuring the _task learning_ capabilities of machine learning systems.

> Our focus on studying zero-shot transfer as an evaluation of task learning is inspired by work demonstrating task learning in the field of NLP.

This focus on task learning as transfer learning is meant to mirror the effects of GPT-1 and GPT-2 in language modeling where they showed that training on a large Wikipedia dataset made the model capable of doing other tasks.

> CLIP is pre-trained to predict if an image and a text snippet are paired together in its dataset. To perform zero-shot classification, we reuse this capability. For each dataset, we use the names of all the classes in the dataset as the set of potential text pairings and predict the most probable (image, text) pair according to CLIP.

![Screenshot 2024-05-17 at 12.12.03 PM.png](../../images/Screenshot_2024-05-17_at_12.12.03_PM.png)

![Screenshot 2024-05-17 at 12.12.26 PM.png](../../images/Screenshot_2024-05-17_at_12.12.26_PM.png)

> Over the past few years, empirical studies of deep learning systems have documented that performance is predictable as a function of important quantities such as training compute and dataset size.

![Screenshot 2024-05-17 at 12.14.27 PM.png](../../images/Screenshot_2024-05-17_at_12.14.27_PM.png)

> While the overall trend is smooth, we found that performance on individual evaluations can be much noisier.

CLIPs performance on a variety of tasks actually has a noisy progression curve with more compute (although it is steadily improving).

![Screenshot 2024-05-17 at 12.15.24 PM.png](../../images/Screenshot_2024-05-17_at_12.15.24_PM.png)

> On this broader evaluation suite, the benefits of CLIP are more clear. All CLIP models, regardless of scale, outperform all evaluated systems in terms of compute efficiency.

CLIP is far more data efficient that previously trained image classifiers

**3. Robustness to Natural Distribution Shift**

> Recent research has repeatedly found that [SoTA image classifier models] still make many simple mistakes, and new benchmarks testing these systems has often found their performance to be much lower than both their ImageNet accuracy and human accuracy.

> To what degree are these failures attributable to deep learning, ImageNet, or some combination of the two?

> CLIP models, which are trained via natural language supervision on a very large dataset and are capable of high zero-shot performance, are an opportunity to investigate this question from a different angle.

> Intuitively, a zero-shot model should not be able to exploit spurious correlations or patterns that hold only on a specific distribution, since it is not trained on that distribution. Thus it is reasonable to expect zero-shot models to have much higher effective robustness.

One theory to explain the inaccuracies of some image classifiers was that they identified spurious consistencies in the ImageNet dataset or their respective datasets that work to make predictions there, but not in real datasets. CLIP could not have such problems given how it’s trained.

![Screenshot 2024-05-17 at 12.21.31 PM.png](../../images/Screenshot_2024-05-17_at_12.21.31_PM.png)

### Limitations

> While zero-shot CLIP generalizes well to many natural image distributions […], we’ve observed that zero-shot CLIP still generalizes poorly to data that is truly out-of-distribution for it.

> However, CLIP only achieves 88% accuracy on the handwritten digits of MNIST. An embarrassingly simple baseline of logistic regression on raw pixels outperforms zero-shot CLIP.

Just because MNIST data was out of distribution, CLIP just completely does not understand it.

> Although CLIP can flexibly generate zero-shot classifiers for a wide variety of tasks and datasets, CLIP is still limited to choosing from only those concepts in a given zero-shot classifier. This is a significant restriction compared to a truly flexible approach like image captioning which could generate novel outputs.

CLIP can’t generate it’s own captions, and can only do classification with it’s embedding space (which text & image are the closest?)

### Broader Impacts

> CLIP makes it possible to easily create your own classes for categorization (to ‘roll your own classifier’) without a need for re-training.

### Conclusion

> We have investigated whether it is possible to transfer the success of task-agnostic web-scale pre-training in NLP to another domain.

> We find that adopting this formula results in similar behaviors emerging in the field of computer vision and discuss the social implications of this line of research.

> In order to optimize their training objective, CLIP models learn to perform a wide variety of tasks during pre-training.

The trend continues - large-scale pre-trained models with transfer learning can perform a variety of tasks.