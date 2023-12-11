<h3 id="tensorflow">TensorFlow</h3>
In more detail, the steps needed for porting an existing TensorFlow codebase to run on Neocortex are:

1. Modify `model_fn()` in `model.py` to build the layers of your model function, return a `tf.estimator.EstimatorSpec` object
1. Modify `input_fn()` in `data.py`, return a `tf.data.Dataset` object
1. Implement custom helper logic in `utils.py`, as needed
1. Set dataset path and model parameters in `configs/params.yaml`
1. Run `run.py` to validate and compile your code

The following diagram shows the suggested order in which the modifications should be performed for porting the code. The arrows represent the suggested order for the modification process to perform. This diagram should be read from left to right.

![code migration workflow](https://github.com/pscedu/psc-wpdocs/blob/dev/neocortex/code_migration_workflow.svg)

The following diagram uses arrows to represent the logical flow of information and arguments passed when running the code. This diagram should be read from left to right and from top to bottom.

<img src="https://github.com/pscedu/psc-wpdocs/blob/dev/neocortex/neocortex_code_conversion.svg" alt="diagram showing flow of information and arguments when running code" width=60% />

