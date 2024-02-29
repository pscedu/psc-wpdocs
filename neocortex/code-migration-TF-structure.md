
### Structure of the code
Examples can be found in the [cerebras_reference_implementation repository](https://github.com/Cerebras/cerebras_reference_implementations), which contains examples of standard deep learning models that can be trained on Cerebras hardware and demonstrate the best practices for coding.
* `model.py`: contains the model function definition. For information about the layers supported, please visit the [Cerebras documentation](https://docs.cerebras.net/en/1.6.0/tensorflow-docs/api-rst/tf.html#submodules).
* `data.py`: calls the input data pipeline. Additional data processor modules may be defined elsewhere (e.g., in the `input` folder) for data pipeline implementation.
* `utils.py`: contains the helper scripts.
* `configs/params.yaml`: YAML file containing the model configuration and the training hyperparameter settings.
* `run.py`: contains the training/compilation/evaluation script.

<blockquote>
<strong>Info</strong>
<p style="white-space:nowrap;">Suggested order of files to use:<br /><code>model.py -> data.py -> utils.py -> configs/params.yaml -> run.py</code></p>
</blockquote>
