

## Execution modes
On the Cerebras Wafer Scale Engine 2 (WSE2) you can run neural networks of different model sizes. Cerebras Software supports different execution modes to efficiently run such variety of models.

The execution mode refers to how the Cerebras runtime loads your neural network model onto the WSE2. Two execution modes are supported:
* Layer pipelined: In this mode all the layers of the network are loaded altogether onto the Cerebras WSE2. This mode is selected for neural network models of small to medium sized models (with less than a billion parameters).
* Weight streaming: In this mode one layer of the neural network model is loaded at a time. This layer-by-layer mode is used to run extremely large models (with billions to trillions of parameters).

You can get more information about this on the developer page section on <a href="https://docs.cerebras.net/en/1.6.0/cerebras-basics/cerebras-execution-modes.html#cerebras-execution-modes" target="_blank">Cerebras Execution Modes</a>.

