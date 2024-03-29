

## Commands for compilation (cheat sheet)
### Getting started

This training manual (cheat sheet) will make use of Neocortex resources. If you forgot your Neocortex authentication credentials, please visit the [PSC Password Change Utility webpage](https://apr.psc.edu).

Please connect to Neocortex by SSH-ing into the system using the following command:

<pre>
ssh USERNAME@neocortex.psc.edu
</pre>


Change your current group to your Neocortex project group. This will be very important if you happen to have access to different projects on Neocortex, so you are charging the resource usage to the correct project. 

Choose the relevant group from the list shown by this command, from the “Project” field.
<pre>
projects | grep -A 2 Project
</pre>


We will set the group name “cis210015p” as an example (set it in lowercase):
<pre>
newgrp cis210015p
</pre>

Start an interactive session on Neocortex using the group name you just identified:

<pre>
interact --account cis210015p -n 14
</pre>

Where
* --account: is the SLURM charge id to use.
* --mem: is the amount of memory being allocated for the job
* -n: is the number of tasks to be started for this job. It will reserve about 256GB of RAM for the job this way.
  
Set the following variables. These are going to be used for easily referring to the directories to use during the training.

<pre>
export CEREBRAS_DIR=/ocean/neocortex/cerebras
</pre>

This CEREBRAS_DIR folder contains two main directories of interest:
* modelzoo: contains the code for the models
* data: contains the input data for the models
  
Copy the modelzoo directory to your folder inside the Cerebras scratch space. Please make sure to change to the correct group by using the `newgrp` command, else it will be copied somewhere else or you might get a permission error.

For the latest version:
<pre>
rsync -PaL --chmod u+w $CEREBRAS_DIR/modelzoo $PROJECT/
</pre>

Start the container with the development environment.

For the latest version:
<pre>
singularity shell --bind $PROJECT,$CEREBRAS_DIR/data:/data $CEREBRAS_DIR/cbcore_latest.sif
</pre>
Where
* --bind $DATA_DIR:/data: will mount the DATA_DIR (with ogb/ tfds/ wikitext-103/ ) on “/data” inside the container.
* cbcore-latest.sif: is the latest version of the container made available by Cerebras.
  

