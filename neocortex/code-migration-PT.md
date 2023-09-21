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


