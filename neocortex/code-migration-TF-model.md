
**model.py**

The model function that defines your neural network model. Contains the model function definition and model implementation. 

It supports the usage of the Tensorflow Keras Layers API, but the Tensorflow Metrics API is not supported.

These files get things ready for the Estimator API. The code is divided between what is run in the host (I/O functions, support server, SDF), and what will run in the wafer.

<blockquote>
 <strong>Note</strong>

<p>Please have in mind that your code will not call "train" or "fit" as the Estimator does that (takes care of that responsibility).</p>
</blockquote>

<blockquote>
 <strong>Note</strong> 
 
<p>For information about the layers supported, please visit the <a href="https://docs.cerebras.net/en/1.6.0/tensorflow-docs/api-rst/tf.html#submodules">Cerebras Documentation</a>.</p>
</blockquote>

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
<ul>
 <li><code>mode</code>: passed automatically from the parent model_fn function.</li>
 <li><code>loss</code>: model loss</li>
 <li><code>train_op</code>: optimizer</li>
 <li><code>host_call (dict)</code>:<br />
  <span style="padding-left:15px;">
   <code>{ "accuracy": tf.compat.v1.metrics.accuracy() }</code>
  </span>
 </li>
</ul> 



  

 

