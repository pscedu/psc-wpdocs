

### Structure of the code
Examples can be found in the <a href="https://github.com/Cerebras/cerebras_reference_implementations" target="_blank">Cerebras reference implementation repository</a>, which contains examples of standard deep learning models that can be trained on Cerebras hardware and demonstrate the best practices for coding.
* `model.py`: contains the model function definition.
* `data.py`: where the input data dataloaders are defined.
* `utils.py`: contains the helper scripts.
* `configs/params.yaml`: YAML file containing the model configuration and the training hyperparameter settings.
* `run.py`: contains the training/compilation/evaluation script.

<blockquote>
  <strong>Info</strong>
  <p sstyle="white-space:nowrap;">Suggested order of files to use:<br /><code>model.py -> data.py -> utils.py -> configs/params.yaml -> run.py</code></p>
</blockquote>


