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
