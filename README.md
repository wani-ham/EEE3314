# EEE3314

## Description 
* **Model A**: Fine-tune Conv5_x
* **Model B**: Fine-tune Conv4_x and Conv5_x
* **Model C**: Fine-tune all convolutional layers
* **Model D**:  Freeze all the convolution blocks, add two fully connected (FC) layers before the
softmax layer, and train them together with the softmax layer


## Size-Similarity Matrix

In order to locate this problem on this matrix, we have to examine the dataset size and the dataset similarity. For dataset size we can say it is small because we only have 5000 images. Reminding that the Imagenet used millions of pictures, our dataset is small. For dataset similarity, we can say its not similar. The source is ImageNet (generic objects like cars, dogs, fruits). The target is Faces. While both are visual, the specific high-level features required to distinguish 100 different humans are significantly different. Therefore, we can say this problem is in Quadrant 3.

The theoretical explanation suggests that since the data is small and different, training a linear classifier (Baseline) is often a starting point, but because the domain is different, “we also have to remove several late convolution layers and retrain a few early convolution layers only”, or “alternatively, consider finetune many late convolution laters and consider data augmentation”.

**Baseline Model (Linear Classifier on Frozen Features)**: This model performs poorly with a huge overfitting, since the train accuracy is 85.98% and the test accuracy is 55.60%. While the matrix suggests a linear classifier for small data to avoid overfitting, it fails here because the domain shift is too large. The frozen features from ImageNet are looking for generic object parts, not specific facial structures. The feature extractor is not producing relevant codes for the linear classifier to work with.

**Model A (Fine-tune Conv5_x)** and **Model B (Fine-tune Conv4_x + Conv5_x)**:  These models perform very well with test accuracy of 82.40% and 83.20%. By unfreezing the later layers (Conv4 and Conv5), you allowed the network to learn specific features relevant to faces, while keeping the early layers frozen to act as "Generic feature" extractors (edges, blobs). This aligns perfectly with the Quadrant 3 recommendation to "finetune many late conv layers". Model B performs slightly better because unfreezing more layers (Conv4) allows the model to adapt slightly more complex feature representations to the new domain.

**Model C (Fine-tune all layers)**: This model performs poorly (test accuracy of 77.20%) than Model A & Model B, but it is still better than the baseline model. It aligns with the theoretical prediction that it is not a good idea to fine-tune all layer in problems in Quadrant 3. It is also interesting that Model C has the lowest overfitting gap, since its train accuracy is 81.20% and test accuracy is 77.20%.

**Model D (Frozen Base + MLP Head)**: Adding a 2-hidden-layer MLP adds non-linearity. Because the convolutional base is frozen, the input features are still ImageNet features, not face features. No amount of complexity in the classifier head can fully compensate for a feature extractor that is looking for the wrong patterns. Model D (Freeze) failed to achieve high accuracy because the domain gap required feature adaptation.
