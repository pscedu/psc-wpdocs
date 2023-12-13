

## Commands for compilation (cheat sheet)
### Getting started

This training manual (cheat sheet) will make use of Neocortex resources. If you forgot your Neocortex authentication credentials, please visit the [PSC Password Change Utility webpage](https://apr.psc.edu).

Please connect to Neocortex by SSH-ing into the system using the following command:
```
ssh USERNAME@neocortex.psc.edu
```

Change your current group to your Neocortex project group. This will be very important if you happen to have access to different projects on Neocortex, so you are charging the resource usage to the correct project. 

Choose the relevant group from the list shown by this command, from the “Project” field.
```
projects | grep -A 2 Project
```

We will set the group name “cis210015p” as an example (set it in lowercase):
```
newgrp cis210015p
```

Start an interactive session on Neocortex using the group name you just identified:
```
interact --account cis210015p -n 14
```

Where
* --account: is the SLURM charge id to use.
* --mem: is the amount of memory being allocated for the job
* -n: is the number of tasks to be started for this job. It will reserve about 256GB of RAM for the job this way.
  
Set the following variables. These are going to be used for easily referring to the directories to use during the training.
```
export CEREBRAS_DIR=/ocean/neocortex/cerebras
```

This CEREBRAS_DIR folder contains two main directories of interest:
* modelzoo: contains the code for the models
* data: contains the input data for the models
  
Copy the model zoo dir to your folder inside the Cerebras scratch space. Please make sure to have changed to the correct group by using the `newgrp` command, else it will be copied somewhere else or you might get a permission error.

For the latest version:
```
rsync -PaL --chmod u+w $CEREBRAS_DIR/modelzoo $PROJECT/
```

Start the container with the development environment.

For the latest version:
```
singularity shell --bind $PROJECT,$CEREBRAS_DIR/data:/data $CEREBRAS_DIR/cbcore_latest.sif
```
Where
* --bind $DATA_DIR:/data: will mount the DATA_DIR (with ogb/ tfds/ wikitext-103/ ) on “/data” inside the container.
* cbcore-latest.sif: is the latest version of the container made available by Cerebras.
  

