
   

## Compatibility with the CS-2
To execute code on the Neocortex system, you need to convert your codebase and make it compatible with the TensorFlow CerebrasEstimator API or PyTorch API. CerebrasEstimator inherits from TensorFlow’s Estimator and is the interface to train models on the CS-2. It can also train/validate/predict on the CPU/GPU.
### How to make your code compatible with the CS-2
#### TensorFlow
Construct CerebrasEstimator with 4 main pieces of information:
* Model function (model_fn)
* Input function (input_fn)
* Config - a set of configuration options that is an extension of TF’s Config (config)
* params dictionary (params) which contains other configuration information for model_fn and input_fn

Please go through the <a href="https://docs.cerebras.net/en/1.6.0/tensorflow-docs/porting-tf-to-cs/index.html" target="_blank">Port TensorFlow to Cerebras</a> section of the Cerebras Documentation for detailed steps.
#### PyTorch
You can either modify reference models in Cerebras modelzoo GitHub repository or leverage the common backbone in Cerebras Model Zoo Repository, the run function.

Please go through the <a href="https://docs.cerebras.net/en/1.6.0/pytorch-docs/adapting-pytorch-to-cs.html" target="_blank">Port PyTorch to Cerebras</a> section of the Cerebras Documentation for detailed steps.

