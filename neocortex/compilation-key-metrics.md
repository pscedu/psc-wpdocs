## Compilation Key Metrics to Share

### In this section
* Commands for compilation (cheat sheet)
*    * [Getting started](#commands-for-compilation-cheat-sheet)
     * [Tensorflow](#tensorflow)
     * [Pytorch](*pytorch)
* [Additional notes for generating the key metrics](#additional-notes-for-generating-the-key-metrics)
* [GitHub repostory URLS](github-repository-urls)
* [Common Errors](#common-errors)


### Commands for compilation (cheat sheet)
#### Getting started

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
  
#### TensorFlow
Change dirs into the modelzoo dir, navigate to the MNIST TF code. The contents of this folder should be “`configs/`, `data.py`, `model.py`, `README.md`, `run.py`, `utils.py`”.
```
cd $PROJECT/modelzoo/fc_mnist/tf
```

Run compile in lightweight validation mode.
```
python run.py --mode train --validate_only --params configs/params.yaml
```

Run compile in full compilation mode.
```
python run.py --mode train --compile_only --params configs/params.yaml
```

The ratio of utilized components is the theoretical peak throughput. Grep the “total_utilization” from the JSON file:
```
grep -o '"total_utilization":[0-9\.]*' model_dir/*/plan.json
```

When the pipeline is fully loaded, it will process the model using this many cycles.
```
grep -o '"estimated_deltat":[0-9\.]*' model_dir/*/deltat_estimate.json
    # "estimated_deltat": **674**,
```

#### PyTorch
Navigate into the modelzoo directory to where the MNIST PyTorch codes are located.
```
cd $PROJECT/modelzoo/fc_mnist/pytorch
python-pt run.py --mode train --validate_only --params configs/params.yaml
```

To run full compilation mode:

For the latest version:
```
python-pt run.py --mode train --compile_only --params configs/params.yaml
```
The ratio of utilized components is the theoretical peak throughput. Grep the “total_utilization” from the JSON file:

For the latest version:
```
grep -o '"total_utilization":[0-9\.]*' model_dir/*/plan.json
```

Example: This means 5.8% of the WSE2 chip is going to be used for running this code:
```
"total_utilization": 0.0588006479419213
```

When the pipeline is fully loaded, it will process the model using the number of cycles as the value returned by `estimated_deltat`.

For the latest version:
```
grep -o '"estimated_deltat":[0-9\.]*' model_dir/*/deltat_estimate.json
```

Example: This means a total of 674 cycles are estimated to be required for the WSE2 to process one sample:
```
"estimated_deltat": 674
```

### Additional notes for generating the key metrics

You need to be located in the same directory in which you compile **your code** with `python run.py [...]`, as shown in the “Reference Compilation for the CS-2 Commands section”. For the example instructions, that would be `$PROJECT/modelzoo/fc_mnist/tf`.
1. Get the **ratio of utilized components**, that is, the theoretical peak usage from the total WSE2 in the CS-2.
2. Get the **total number of cycles per sample**: when the pipeline is fully loaded, it will process the model using this many cycles.
3. **Size of the input dataset**: how big is the total set of files for the dataset?
   * For example: 1GB composed of 1024 individual files
4. **Size of each dataset sample to be processed**: what is the size of each of the dataset samples that integrate the complete dataset.
   * For the same example, if there are 1024 individual files for a total of 1GB, the sample size is 1MB. (1024MB / 1024 files) = 1MB.
5. What is the **maximum expected batch size to use**?
   * This value depends on the dataset you are using.

  
**Note:** Please, use the [Neocortex Project Key Metrics file](https://portal.neocortex.psc.edu/home) and submit it to the [Key-compilation-metrics Submit Box link](https://cmu.app.box.com/f/2e34313bdacf410a96b28e34b7d8f4b3).

### GitHub Repository URLs
* [https://github.com/Cerebras/modelzoo](https://github.com/Cerebras/modelzoo)
* [https://github.com/drivendata/cookiecutter-data-science](https://github.com/drivendata/cookiecutter-data-science)
  
### Common Errors
* FATAL: could not open image /path/to/cbcore-latest.sif
   * Make sure you are pointing to the correct container location. The full path is “`/ocean/neocortex/cerebras/cbcore-latest.sif`"
* srun: error: Unable to allocate resources: Invalid qos specification
   * This will usually happen when the project allocation id in Neocortex has not been specified, or a different one (the default one) was used. For this not to happen, make sure to add the `--account cis210012p` to the `interact` command, or to set your primary group with `newgrp cis210012p`.
* 404 Error when accessing the GitHub Cerebras/modelzoo repository
   * Please make sure you are logged into GitHub. If you are already logged in and it doesn’t work, please check your email and make sure to accept the invite to join the repository. If you have not gotten an invite, please let us know by emailing us.
* Can't login to Neocortex.psc.edu
   * Please make sure you are using the correct username. If needed, please visit the [PSC web password change site](https://apr.psc.edu/).
* Permission denied error when trying to copy the code from the $CEREBRAS_DIR location.
   * Make sure your primary group is set correctly. You can check that by running the `groups` command and checking that “cis210012p” is showing first on the left. If it’s not showing in the first position, run the following command: `newgrp cis210012p`. If it’s not showing at all, please make sure you logged in using the correct account, or email us if everything looks good on your end.
* Warning! ***HDF5 library version mismatched error***
   * Please make sure you are trying to run the code using the software environment inside the container and that you are not using any other external software stacks, such as an external module or Conda environment.
* Can’t login to the Neocortex portal page.
   * Please make sure that you are using your PSC Neocortex credentials. If you are still getting authentication errors but your account is new, please try again the next day since the initial process takes a while to be reflected all throughout Neocortex; if you have been using your Neocortex account for some days now but the Neocortex Portal login doesn’t work, please email us.

