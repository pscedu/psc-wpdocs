

**model.py**

The model definition for the model. Contains the model definition and model implementation.

The goal is to define a `torch.nn.Module` class and use it as input for a `PyTorchBaseModel` class, that will later be used as input for the `run()` method in `run.py`  (more details below in this same document).
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


