## Developing for the CS-2

### In this section
* ML Workflows: [TensorFlow or PyTorch](#tensorflow-or-pytorch)
* [Compatibility with the CS-2](#compatibility-with-the-cs-2)
* [Cerebras Kernal Library Support Q1 2023](#cerebras-kernel-library-support-q123)
* Supported Core Compute Kernels:  [Cerebras TensorFlow Layer API](#cerebras-tensorflow-layer-api) and [Cerebras PyTorch Layer API](#cerebras-pytorch-layer-api)
* [Execution modes](#execution-modes)

  <br />          
There are two different ways in which you can  make use of the CS-2:  
* Using the main available frameworks, such as TensorFlow and Pytorch, while leveraging the Cerebras container and libraries. 
* Running an HPC workflow.
  
### ML Workflows
#### TensorFlow or Pytorch
There are different modes in which you could run jobs with the Cerebras container on Neocortex:
* **Validation:** this process requires a single task to be running. It will check if the different things needed for compiling the code are in order, such as the YAML file with the configurations, the dataset location to use, the paths for accessing the code, etc.
* **Compilation:** this process also requires a single task to be running. It will compile the code based on the configs file, so the executable can be used for training on the CS-2 boxes. Kernel matching takes place here.
* **Training:** this process requires multiple tasks to be running. A single task will be elected as coordinator, and all of the other tasks will stream the dataset into the CS-2 as workers. This is where the actual model training takes place.
* **Evaluation:** this will make use of the trained model and evaluate the trained model.

Now, validation and compilation jobs with CPUs can be done on both Neocortex (preferred) and Bridges-2, but training jobs with CS-2s have to be run from Neocortex because Bridges-2 does not have a way to start jobs on the CS-2 boxes. It is possible to run training and model inference jobs with CPUs/GPUs with Bridges-2. Please refer to Cerebras documentation for details (e.g., for [Tensorflow](https://docs.cerebras.net/en/1.6.0/tensorflow-docs/running-a-model/train-eval-predict.html) and [general software requirements and dependencies](https://docs.cerebras.net/en/1.6.0/getting-started/software-dependencies.html)).

Then, for running any of these different modes, you will need to first connect to the Neocortex (preferred) or Bridges-2 login nodes. 

The steps are:
1. Connect via SSH.
2. Set the Neocortex grant id to be used as your primary SLURM charge id.
3. Switch to the directory with your code.
4. Customize the config parameters and example scripts according to your code.
5. Start a job via SLURM.
6. Start the Cerebras container while binding the directories that are important for your code.
7. Finally, run the tasks by using the scripts and arguments needed for the different modes.
   
Regarding those additional scripts, you can validate and compile interactively, but you should run trainings and evaluations using the batch mode.

### Compatibility with the CS-2
To execute code on the Neocortex system, you need to convert your codebase and make it compatible with the TensorFlow CerebrasEstimator API or PyTorch API. CerebrasEstimator inherits from TensorFlow’s Estimator and is the interface to train models on the CS-2. It can also train/validate/predict on the CPU/GPU.
#### How to make your code compatible with the CS-2
##### TensorFlow
Construct CerebrasEstimator with 4 main pieces of information:
* Model function (model_fn)
* Input function (input_fn)
* Config - a set of configuration options that is an extension of TF’s Config (config)
* params dictionary (params) which contains other configuration information for model_fn and input_fn

Please go through the [Port TensorFlow to Cerebras](https://docs.cerebras.net/en/1.6.0/tensorflow-docs/porting-tf-to-cs/index.html) section of the Cerebras Documentation for detailed steps.
##### PyTorch
You can either modify reference models in Cerebras modelzoo GitHub repository or leverage the common backbone in Cerebras Model Zoo Repository, the run function.

Please go through the [Port PyTorch to Cerebras](https://docs.cerebras.net/en/1.6.0/pytorch-docs/adapting-pytorch-to-cs.html) section of the Cerebras Documentation for detailed steps.

### Cerebras Kernel Library Support Q1 ‘23
Supported Network Types (Supported Models)
* MLP models (TensorFlow and PyTorch)
* 2D Unet (experimental) (TensorFlow)
* BERT-style models (TensorFlow and Pytorch)
* GPT-2 (Layer pipeline mode: TensorFlow and PyTorch, Weight-streaming: TensorFlow)
* GPT-3, GPT-J (Weight-streaming: TensorFlow)
* Transformer (TensorFlow and PyTorch)
* T5 (TensorFlow and PyTorch)
* Linformer (TensorFlow)
* RoBERTa (TensorFlow and PyTorch)

### Supported Core Compute Kernels
#### Cerebras TensorFlow Layer API
Please check the list of [supported Tensorflow layers here](https://docs.cerebras.net/en/1.6.0/tensorflow-docs/api-rst/tf.html).

#### Cerebras PyTorch Layer API
**PyTorch Layer API:** custom wrapper of several commonly used PyTorch APIs

**Supported PyTorch Operations**  Please check the list of [supported PyTorch layers and operations here](https://docs.cerebras.net/en/1.6.0/pytorch-docs/pytorch-ops.html).

### Execution modes
On the Cerebras Wafer Scale Engine 2 (WSE2) you can run neural networks of different model sizes. Cerebras Software supports different execution modes to efficiently run such variety of models.

The execution mode refers to how the Cerebras runtime loads your neural network model onto the WSE2. Two execution modes are supported:
* Layer pipelined: In this mode all the layers of the network are loaded altogether onto the Cerebras WSE2. This mode is selected for neural network models of small to medium sized models (with less than a billion parameters).
* Weight streaming: In this mode one layer of the neural network model is loaded at a time. This layer-by-layer mode is used to run extremely large models (with billions to trillions of parameters).

You can get more information about this on the developer page section on [Cerebras Execution Modes](https://docs.cerebras.net/en/1.6.0/cerebras-basics/cerebras-execution-modes.html#cerebras-execution-modes).


