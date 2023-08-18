

#### Structure of the code
Examples can be found in the [cerebras_reference_implementation repository](https://github.com/Cerebras/cerebras_reference_implementations), which contains examples of standard deep learning models that can be trained on Cerebras hardware and demonstrate the best practices for coding.
* `model.py`: contains the model function definition.
* `data.py`: where the input data dataloaders are defined.
* `utils.py`: contains the helper scripts.
* `configs/params.yaml`: YAML file containing the model configuration and the training hyperparameter settings.
* `run.py`: contains the training/compilation/evaluation script.

**Info**

Suggested order of files to use: `model.py -> data.py -> utils.py -> configs/params.yaml -> run.py`

