## Code Migration to the Cerebras Architecture
In this section:
* [Tensorflow](https://github.com/pscedu/psc-wpdocs/edit/dev/neocortex/code-migration.md#tensorflow)
   * Structure of the code
      * model.py
      * data.py
      * utils.py
      * configs/
      * run.py
     

As described in the Cerebras documentation [How Cerebras Works](https://docs.cerebras.net/en/1.6.0/cerebras-basics/how-cerebras-works.html), the first step to using the Cerebras machines is to port your code from regular TensorFlow or PyTorch to use the Cerebras libraries instead.

![flowchart showing steps to run on a CS system](https://github.com/pscedu/psc-wpdocs/blob/dev/neocortex/how_cerebras_works_workflow.jpg)

The steps to port your code are laid out in the the [Workflow for TensorFlow on CS](https://docs.cerebras.net/en/1.6.0/tensorflow-docs/cs-tf-workflow.html) documentation. This documentation also goes over the process needed for performing such code migration. This is a good starting point for understanding the process. 
1. Port to Cerebras
2. Prepare input
3. Compile on CPU
4. Run on Cerebras

**Note**

 * An estimation of the required effort to get the code ready is one to four weeks for a single person performing all of the edits.

 * **For track one users:** For step 1, "Port to Cerebras,” you will only need to follow the sections related to input function or dataloader. You will not need to modify the model or the overall code structure.

### TensorFlow {#tensorflow}
In more detail, the steps needed for porting an existing TensorFlow codebase to run on Neocortex are:
1. Modify `model_fn()` in `model.py` to build the layers of your model function, return a `tf.estimator.EstimatorSpec` object
2. Modify `input_fn()` in `data.py`, return a `tf.data.Dataset` object
3. Implement custom helper logic in `utils.py`, as needed
4. Set dataset path and model parameters in `configs/params.yaml`
5. Run `run.py` to validate and compile your code
   
The following diagram shows the suggested order in which the modifications should be performed for porting the code. The arrows represent the suggested order for the modification process to perform. This diagram should be read from left to right.

![code migration workflow](https://github.com/pscedu/psc-wpdocs/blob/dev/neocortex/code_migration_workflow.svg)

The following diagram uses arrows to represent the logical flow of information and arguments passed when running the code. This diagram should be read from left to right and from top to bottom.

<img src="https://github.com/pscedu/psc-wpdocs/blob/dev/neocortex/neocortex_code_conversion.svg" alt="diagram showing flow of information and arguments when running code" width=60% />

#### Structure of the code
Examples can be found in the cerebras_reference_implementation repository, which contains examples of standard deep learning models that can be trained on Cerebras hardware and demonstrate the best practices for coding.
* `model.py`: contains the model function definition. For information about the layers supported, please visit the Cerebras Documentation.
* `data.py`: calls the input data pipeline. Additional data processor modules may be defined elsewhere (e.g., in the input folder) for data pipeline implementation.
* `utils.py`: contains the helper scripts.
* `configs/params.yaml`: YAML file containing the model configuration and the training hyperparameter settings.
* `run.py`: contains the training/compilation/evaluation script.

**Info**

 - Suggested order of files to use: `model.py -> data.py -> utils.py -> configs/params.yaml -> run.py`

##### model.py

The model function that defines your neural network model. Contains the model function definition and model implementation. 

It supports the usage of the Tensorflow Keras Layers API, but the Tensorflow Metrics API is not supported.

These files get things ready for the Estimator API. The code is divided between what is run in the host (I/O functions, support server, SDF), and what will run in the wafer.

**Note**

Please have in mind that your code will not call "train" or "fit" as the Estimator does that (takes care of that responsibility).

For information about the layers supported, please visit the [Cerebras Documentation](https://docs.cerebras.net/en/1.6.0/tensorflow-docs/api-rst/tf.html#submodules).

`def model_fn(features, labels, mode, params) -> tf.estimator.EstimatorSpec/common.tf.estimator.cs_estimator_spec.CSEstimatorSpec:`

This method should have the logic to build the layers of the model function.

**Input:**

These input arguments (features, labels) are taken automatically from the values returned from the `input_fn` method, the run configuration set by `run.py` (mode) and the `configs/params.yaml` file.
* `features`: This is the first item returned from the `input_fn`. It should be a single `tf.Tensor` or `dict`.
* `labels`: This is the second item returned from the `input_fn`. It should be a single `tf.Tensor` or `dict`. If mode is `tf.estimator.ModeKeys.PREDICT`, `labels=None` will be passed.
* `mode`: it specifies if this is running in training, evaluation, or prediction mode.
*`params`: additional configuration as a `dict` of hyperparameters loaded from the `configs/params.yaml` file to configure Estimators for tuning

**Output:**
* `tf.estimator.EstimatorSpec`: it fully defines the model to be run by an estimator.

The EstimatorSpec takes:
* `mode`: passed automatically from the parent model_fn function.
* `loss`: model loss
* `train_op`: optimizer
* `host_call (dict)`:
``` { "accuracy": tf.compat.v1.metrics.accuracy() } ```
 
##### data.py

Input data pipeline implementation: the input pipeline must be very fast, you must ensure you preprocess the input data by sharding, shuffling, prefetching, interleaving, repeating, batching, etc., in proper order.
The input function builds the input pipeline and yields the batched data in the form of (features, labels) pairs, where:
* `features` can be a tensor or dictionary of tensors, and
* `labels` can be a tensor, a dictionary of tensors, or None.

``` def input_fn(params: dict, mode=tf.estimator.ModeKeys.TRAIN) -> tf.data.Dataset: ```

This method should have the input function and run any preprocessing that might be needed before returning a TF dataset object.

**Input**
* params (`dict`):
```{
    train_input: dict = {
        data_dir: str,
        num_parallel_calls: int = 0,
        train_batch_size: int, eval_batch_size: int, batch_size: int,
        shuffle: bool
    }
}
```

* `mode` (str): One of the `tf.estimator.ModeKeys.*` strings. The available options are: TRAIN, EVAL, TRAIN_AND_EVAL, and INFERENCE
**Output**
* tf.data.Dataset`: "features" and "labels" will be inside this Dataset object as the handles to the batched data that the model will use.

##### utils.py

It contains the helper scripts, including get_params which parses the params dictionary from the YAML file. You can also include the user-defined helper functions here or elsewhere.

**configs/**

Directory of YAML files containing model configurations and training hyperparameters (params.yaml).

If you would like to utilize the models in the examples directly but just change a few settings such as the number of layers, dimension, and hyperparameters, the easiest way is to change parameter values in a YAML config file. The parameter values will be read when the program starts and passed to the code function as a dictionary.

A few examples of content in the params.yaml file:
* train_input:
   * `data_dir`: './tfds' # Place to store data
   * `batch_size`: 256
* model:
   * `activation_fn`: 'relu' # Other options: relu, tanh, elu, selu, linear (identity)
* optimizer:
   * `learning_rate`: 0.001
* runconfig:
   * `max_steps`: 100000
   * `save_checkpoints_steps`: 10000
   * `keep_checkpoint_max`: 2
   * `model_dir`: 'model_dir'
For more details regarding the parameter files and arguments that can be specified, please refer to the [Pytorch Create Params YAML](https://docs.cerebras.net/en/1.6.0/pytorch-docs/adapting-pytorch-to-cs.html#step-4-create-params-yaml-file) file Cerebras Documentation page.

##### run.py
It contains the training script, performs train and eval. This file contains the general framework to build the estimator and most of the time it should not be modified.
For more information please visit the [Porting TensorFlow to Cerebras](https://docs.cerebras.net/en/1.6.0/tensorflow-docs/porting-tf-to-cs/index.html) Cerebras documentation page.

### PyTorch
This is an overview of the steps needed for porting an existing PyTorch codebase to run on Neocortex:
1. Modify the data loaders for training (`get_train_dataloader`) and evaluation (`get_eval_dataloader`) in `data.py`, return a `torch.utils.data.DataLoader` object
2. Modify `model.py` to define the model using a `PyTorchBaseModel` class.
3. Modify `configs/params.yaml` to set the dataset path and model definition parameters.
4. Modify `run.py` to validate and compile your code.
   
The following diagram shows the suggested order in which the modifications should be performed for porting the code. The arrows represent the suggested order for the modification process to perform. This diagram should be read from left to right.
![Suggested order for modifications to port PyTorch code](https://github.com/pscedu/psc-wpdocs/blob/dev/neocortex/neocortex_code_migration_pytorch_suggested_order.svg)

The following diagram uses arrows to represent the logical flow of information and arguments passed when running the code. This diagram should be read from top to bottom.

<img alt="diagram showing flow of information and arguments when running PyTorch code" src="https://github.com/pscedu/psc-wpdocs/blob/dev/neocortex/neocortex_code_migration_pytorch.svg" width=60% />

#### Structure of the code
Examples can be found in the [cerebras_reference_implementation repository](https://github.com/Cerebras/cerebras_reference_implementations), which contains examples of standard deep learning models that can be trained on Cerebras hardware and demonstrate the best practices for coding.
* `model.py`: contains the model function definition.
* `data.py`: where the input data dataloaders are defined.
* `utils.py`: contains the helper scripts.
* `configs/params.yaml`: YAML file containing the model configuration and the training hyperparameter settings.
* `run.py`: contains the training/compilation/evaluation script.

**Info**

Suggested order of files to use: `model.py -> data.py -> utils.py -> configs/params.yaml -> run.py`

##### model.py
The model definition for the model. Contains the model definition and model implementation.

The goal is to define a `torch.nn.Module` class and use it as input for a `PyTorchBaseModel` class, that will later be used as input for the `run()`` method inrun.py` ` (more details down below in this same document).
```
class Model(torch.nn.Module):
    def __init__(self, params):
        ...
    def forward(inputs):
        ...
        return outputs
    ...

class BaseModel(PyTorchBaseModel):
    def __init__(self, params, device = None)
        self.model = Model(params)  # Use the Model class defined above.
        self.loss_fn = ...
        ...
        super().__init__(params=params, model=self.model, device=device)
    def __call__(self, data):
        ...
        inputs, targets = data
        outputs = self.model(inputs)
        loss = self.loss_fn(outputs, targets)
        return loss
```

##### data.py
Input data pipeline implementation: the input pipeline must be very fast, you must ensure you preprocess the input data by sharding, shuffling, prefetching, interleaving, repeating, batching, etc., in proper order.

```
def get_train_dataloader(params):
    ...
    loader = torch.utils.data.DataLoader(...)
    return loader

def get_eval_dataloader(params):
    ...
    loader = torch.utils.data.DataLoader(...)
    return loader
```
These methods should have the input function and run any preprocessing that might be needed before returning a dataloader object.

**Inputs**
* params: (dict)

  ```
  {
      train_input: dict = {
          data_dir: str,
          num_examples: int,
          train_batch_size: int, eval_batch_size: int, batch_size: int,
          shuffle: bool
      }
      eval_input: dict = {
          …
      }
  }
  ```
  
**Output**

* `torch.utils.data.DataLoader`: This object takes a torch.utils.data.TensorDataset object with the images and labels to use, as well as the batch_size, shuffle, and any other arguments defined in the params file.

##### utils.py
It contains the helper scripts, including get_params which parses the params dictionary from the YAML file. You can also include the user-defined helper functions here or elsewhere.

**configs/**

Directory of YAML files containing model configurations and training hyperparameters (params.yaml).

If you would like to utilize the models in the examples directly but just change a few settings such as the number of layers, dimension, and hyperparameters, the easiest way is to change parameter values in a YAML config file. The parameter values will be read when the program starts and passed to the code function as a dictionary.

The main information to be defined in the config files is:
1. The optimizer defines learning rates and optimizer details. (required)
2. The model section defines architecture hyperparameters. (optional)
   
A few examples of contents in the params.yaml:
* train_input:
   * `data_dir:` './tfds' # Place to store data
   * `batch_size`: 256
* model:
   * `mixed_precision`: bool
   * `input_size`: int
* optimizer:
   * `learning_rate`: 0.001
* runconfig:
   * `max_steps`: 100000
   * `checkpoints_steps`: 2000
     
##### run.py
It contains the training script, performs train and eval. This file contains the general framework to build the estimator and most of the time it should not be modified.
An example invocation looks like this:
```
run(model_fn=MNISTModel,
    train_data_fn=get_train_dataloader,
    eval_data_fn=get_eval_dataloader,
    default_params_fn=set_defaults)
```
Input:
* `model_fn`: Required. A callable that takes in a dictionary of parameters. Returns a `PyTorchBaseModel`.
* `train_data_fn`: Required during training run.
* `eval_data_fn`: Required during evaluation run.
* `default_params_fn`: Optional. A callable that takes in a dictionary of parameters. Sets default parameters.

For more information please visit the [Porting PyTorch Model to CS](https://docs.cerebras.net/en/1.6.0/pytorch-docs/adapting-pytorch-to-cs.html) Cerebras documentation page.

