# Track 2 (ML): Models similar to the Cerebras Model Zoo models
Track 2 is for ML models that are a **combination of the building blocks** used by Model Zoo models and/or the layers supported by Cerebras as listed in their documentation.

**Good Match Criteria:** You would be a good match for this track if your research already uses, or could potentially use, the specific TensorFlow or PyTorch layers listed below for implementing the models.
## Supported Core Compute Kernels
### Cerebras TensorFlow Layer API
Please check the [list of supported Tensorflow layers here](https://docs.cerebras.net/en/1.6.0/tensorflow-docs/api-rst/tf.html).
### Cerebras PyTorch Layer API
* PyTorch Layer API: custom wrapper of several commonly used PyTorch APIs

Supported PyTorch Operations: Please check the [list of supported PyTorch layers and operations here](https://docs.cerebras.net/en/1.6.0/pytorch-docs/pytorch-ops.html).

## Track Specific Questions

If your project falls under this category, make sure to address the following questions in your application document:

1. Please indicate which building blocks listed
   in <a href="https://docs.cerebras.net/en/1.6.0/tensorflow-docs/api-rst/tf.html">Tensorflow layers supported</a>
   or <a href="https://docs.cerebras.net/en/1.6.0/pytorch-docs/pytorch-ops.html">PyTorch functionality supported</a> you
   propose to use.
2. Please attach a diagram that describes your proposed model, along the lines illustrated in this example:
   ![Figure: The Transformer, model architecture (Vaswani A. et. al. 2017).](https://github.com/pscedu/psc-wpdocs/blob/dev/neocortex/img/track_2_example.png)
3. How big is the model you intend to train on Neocortex? Please describe in terms of the number of layers or kernels
   that comprise it. Include size in terms of MB as well.
4. How big is the dataset (number of samples, MB per sample) you intend to use?
5. Please specify the shapes of the input and output tensors for your model/s.
6. If possible, please specify the name of the dimensions for your input and output tensors from the previous question.
   E.g. (batch, input channels, height, width)
7. Please specify the loss function that you would like to use.
8. Please list the libraries complementary to standard PyTorch and/or TensorFlow distributions that you would need to
   train your model(s).
9. Please list the key libraries that you would need for data preprocessing.
