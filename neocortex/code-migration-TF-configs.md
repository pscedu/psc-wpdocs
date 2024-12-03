
**configs/**

Directory of YAML files containing model configurations and training hyperparameters (params.yaml).

If you would like to utilize the models in the examples directly but just change a few settings such as the number of layers, dimension, and hyperparameters, the easiest way is to change parameter values in a YAML config file. The parameter values will be read when the program starts and passed to the code function as a dictionary.

A few examples of content in the `params.yaml` file:
* train_input:
   * `data_dir`: './tfds'  &nbsp;&nbsp;&nbsp;&nbsp;   # Place to store data
   * `batch_size`: 256
* model:
   * `activation_fn`: 'relu'    &nbsp;&nbsp;&nbsp;&nbsp; # Other options: relu, tanh, elu, selu, linear (identity)
* optimizer:
   * `learning_rate`: 0.001
* runconfig:
   * `max_steps`: 100000
   * `save_checkpoints_steps`: 10000
   * `keep_checkpoint_max`: 2
   * `model_dir`: 'model_dir'
     
For more details regarding the parameter files and arguments that can be specified, please refer to the <a href="https://docs.cerebras.net/en/1.6.0/pytorch-docs/adapting-pytorch-to-cs.html#step-4-create-params-yaml-file" target="_blank">Pytorch Create Params YAML</a> file Cerebras documentation page.


