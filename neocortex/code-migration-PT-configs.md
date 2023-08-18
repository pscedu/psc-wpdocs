
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
     
