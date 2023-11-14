
**model.py**

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
 

