 
### ML Workflows
#### TensorFlow or Pytorch
There are different modes in which you could run jobs with the Cerebras container on Neocortex:
* **Validation:** this process requires a single task to be running. It will check if the different things needed for compiling the code are in order, such as the YAML file with the configurations, the dataset location to use, the paths for accessing the code, etc.
* **Compilation:** this process also requires a single task to be running. It will compile the code based on the configs file, so the executable can be used for training on the CS-2 boxes. Kernel matching takes place here.
* **Training:** this process requires multiple tasks to be running. A single task will be elected as coordinator, and all of the other tasks will stream the dataset into the CS-2 as workers. This is where the actual model training takes place.
* **Evaluation:** this will make use of the trained model and evaluate the trained model.

Now, validation and compilation jobs with CPUs can be done on both Neocortex (preferred) and Bridges-2, but training jobs with CS-2s have to be run from Neocortex because Bridges-2 does not have a way to start jobs on the CS-2 boxes. It is possible to run training and model inference jobs with CPUs/GPUs with Bridges-2. Please refer to Cerebras documentation for details (e.g., for [Tensorflow](https://docs.cerebras.net/en/1.6.0/tensorflow-docs/running-a-model/train-eval-predict.html) and [general software requirements and dependencies](https://docs.cerebras.net/en/1.6.0/getting-started/software-dependencies.html)).

Then, for running any of these different modes, you will need to first connect to the Neocortex (preferred) or Bridges-2 login nodes. 

The steps are:
1. Connect via SSH.
2. Set the Neocortex grant id to be used as your primary SLURM charge id.
3. Switch to the directory with your code.
4. Customize the config parameters and example scripts according to your code.
5. Start a job via SLURM.
6. Start the Cerebras container while binding the directories that are important for your code.
7. Finally, run the tasks by using the scripts and arguments needed for the different modes.
   
Regarding those additional scripts, you can validate and compile interactively, but you should run trainings and evaluations using the batch mode.

