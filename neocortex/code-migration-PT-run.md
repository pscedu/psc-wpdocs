

     
**run.py**

It contains the training script, performs train and eval. This file contains the general framework to build the estimator and most of the time it should not be modified.

An example invocation looks like this:
<pre>
run(model_fn=MNISTModel,
    train_data_fn=get_train_dataloader,
    eval_data_fn=get_eval_dataloader,
    default_params_fn=set_defaults)
</pre>

Input:
* `model_fn`: Required. A callable that takes in a dictionary of parameters. Returns a `PyTorchBaseModel`.
* `train_data_fn`: Required during training run.
* `eval_data_fn`: Required during evaluation run.
* `default_params_fn`: Optional. A callable that takes in a dictionary of parameters. Sets default parameters.

For more information please visit the <a href="https://docs.cerebras.net/en/1.6.0/pytorch-docs/adapting-pytorch-to-cs.html" target="_blank">"Porting PyTorch Model to CS Cerebras"</a> documentation page.

