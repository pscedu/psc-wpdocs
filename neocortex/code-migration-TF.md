<h3 id="tensorflow">TensorFlow</h3>
In more detail, the steps needed for porting an existing TensorFlow codebase to run on Neocortex are:
<ol>
<li>Modify `model_fn()` in `model.py` to build the layers of your model function, return a `tf.estimator.EstimatorSpec` object</li>
<li>Modify `input_fn()` in `data.py`, return a `tf.data.Dataset` object</li>
<li>Implement custom helper logic in `utils.py`, as needed</li>
<li>Set dataset path and model parameters in `configs/params.yaml`</li>
<li>Run `run.py` to validate and compile your code</li>
</ol>

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


For more information please visit the [Porting PyTorch Model to CS](https://docs.cerebras.net/en/1.6.0/pytorch-docs/adapting-pytorch-to-cs.html) Cerebras documentation page.
