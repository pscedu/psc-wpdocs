
Running Jobs¶
Neocortex¶
This subsection provides a brief description about how you can run and monitor your jobs on the Neocortex system. Sample commands are as follows:
* First, you need to login to the system via ssh. Use your PSC credentials.
ssh PSC_USERNAME@neocortex.psc.edu
How to pre-compile your model?¶
* You can pre-compile your model on the CPU while CS-2 is busy with another job. Navigate to your model directory and follow these instructions:
* Reserve CPU node and run Cerebras singularity container in interactive mode.
* srun --pty --cpus-per-task=28 --kill-on-bad-exit singularity shell --cleanenv --bind /local1/cerebras/data,/local2/cerebras/data,/local3/cerebras/data,/local4/cerebras/data,$PROJECT /ocean/neocortex/cerebras/cbcore_latest.sif
To avoid typing this long command every time on the terminal, you can save it in a file, such as, salloc_node and run that. This commands starts the shell with:
* 1 sdf node: 28 cores, 2 threads per core
* --bind is for binding the folders so that they are accessible from inside the singularity shell.
* The .sif container here is the symlink to the latest version of the container provided by the Cerebras team. Please use ll for more details about this container.
* From inside the singularity shell, for validation only mode:
* python run.py --mode train --validate_only --model_dir validate
where -o is the output directory, --mode is the mode i.e. compile_only, validate_only, train, eval.
* From inside the singularity shell, for compile only mode:
* python run.py --mode train --compile_only --model_dir compile
Note
You can also start an interactive session on the SDF nodes with the following command:
interact
"interact" will start an interactive job on one of the available SDF nodes (sdf-1 or sdf-2). You can also specify which node to log into with the srun command and --nodelist parameter:
srun --nodelist=sdf-1 --pty bash -i
How to train your model?¶
* In order to train your model, use the following wrapper scripts with predefined worker and singularity settings. The first script is an srun command that takes care of setting up all of the different parameters for the container to be successfully started, while the second script has the static parts of the python command used for the training to start.
srun_train
#!/usr/bin/bash

srun --gres=cs:cerebras:1 --ntasks=7 --cpus-per-task=14  --kill-on-bad-exit singularity exec --bind /local1/cerebras/data,/local2/cerebras/data,/local3/cerebras/data,/local4/cerebras/data,$PROJECT /local1/cerebras/cbcore_latest.sif ./run_train "$@"
run_train
#!/usr/bin/bash

python run.py --cs_ip ${CS_IP_ADDR} --mode train "$@"
The first script could be saved as srun_train, and the second script could be saved as run_train.
* Make sure both of them have file executable permissions.
* chmod +x srun_train run_train
* Now run the following command from your project directory to launch training from scratch:
* ./srun_train --model_dir OUTPUT_DIR
where --model_dir OUTPUT_DIR will be the location where the output from the training will be located. Also, if you want to restart from a checkpoint, just use the same output directory with this parameter.
--model_dir is the same as using -o
In order to launch training from pre-compiled artifacts, specify --model_dir with the output directory you used while compiling, compile in this case (refer to the subsection above).
./srun_train --model_dir compile
* For evaluation purposes:
* python run.py --mode eval --model_dir train
Checkpointing jobs and viewing results¶
* You can specify a directory for logs and training artifacts in the config params.yaml or via a command line argument (i.e. -o or --model_dir) passed to run.py. The default location is model_dir directory. This model_dir directory will be created in the directory that contains the run.py script.
* ./srun_train --model_dir train
* Viewing results on TensorBoard:
* Allocate a node and launch within the singularity shell, with ssh tunneling.
On sdf-1:
salloc_node tensorboard --logdir new_dir --port 16007
On Neocortex login node:
ssh -L 16007:127.0.0.1:16007 sdf-1
On your local machine:
ssh -L 16007:127.0.0.1:16007 PSC_USERNAME@neocortex.psc.edu
Please note that you can't always use 127.0.0.1 for ssh tunneling. For example, when you run tensorboard on bridges-2 it launches at something like http://brxxx.ib.bridges2.psc.edu:6009, and not 127.0.0.1:6009 so the porting command should be: ssh -L 16007:http://brxxx.ib.bridges2.psc.edu:6009
Monitoring and interpreting results on CS-2s¶
* In order to view performance results, you can refer to the performance.json inside the model log directory file. Next, there is a sample entry from this file:
* {
*     "total_time": 261.82,
*     "samples_per_sec": 146665.4,
*     "est_samples_per_sec": 1831896.55,
*     "total_samples": 38400000,
*     "fabric_cores": 266207,
*     "fabric_utilization": 0.011419684681469684,
*     "delta_t": 464,
*     "Frequency": 850000000.0
* }
where you are expected to replace the following elements in the left column with those in the right one:
Sample

Customize with
/local1/cerebras/data,
/local2/cerebras/data,
/local3/cerebras/data,
/local4/cerebras/data
to
Your dataset location
$HOME/modelzoo
to
Your storage space where your code resides.
$HOME/modelzoo/fc_mnist/tf/run.py
to
The main run file that you use for starting the training.
Running batch jobs¶
* In order to validate, compile, train, or evaluate your model using a batch script, please use the following sample piece of code that you can modify accordingly:
neocortex_model.sbatch
#!/usr/bin/bash
#SBATCH --gres=cs:cerebras:1
#SBATCH --ntasks=7
#SBATCH --cpus-per-task=14

newgrp GRANT_ID
cp ${0} slurm-${SLURM_JOB_ID}.sbatch

# This should be the path in which you are storing your own dataset, if applicable. For example, ${PROJECT}/shared/dataset (that would point to your shared folder under /ocean/project/GRANT_ID/)
YOUR_DATA_DIR=${LOCAL}/cerebras/data

# This should be the path in which you are storing your own model.
YOUR_MODEL_ROOT_DIR=${PROJECT}/modelzoo/

# This should be the place in which the run.py file is located.
YOUR_ENTRY_SCRIPT_LOCATION=${YOUR_MODEL_ROOT_DIR}/fc_mnist/tf

# These paths are the ones that contain the input dataset and the code files required for your model to run.
BIND_LOCATIONS=/local1/cerebras/data,/local2/cerebras/data,/local3/cerebras/data,/local4/cerebras/data,${YOUR_DATA_DIR},${YOUR_MODEL_ROOT_DIR}

CEREBRAS_CONTAINER=/ocean/neocortex/cerebras/cbcore_latest.sif
cd ${YOUR_ENTRY_SCRIPT_LOCATION}

# This should be a single process (1 total number of tasks).
srun --ntasks=1 --kill-on-bad-exit singularity exec --bind ${BIND_LOCATIONS} ${CEREBRAS_CONTAINER} python run.py --mode train --validate_only --model_dir validate
# This should be a single process (1 total number of tasks).
srun --ntasks=1 --kill-on-bad-exit singularity exec --bind ${BIND_LOCATIONS} ${CEREBRAS_CONTAINER} python run.py --mode train --compile_only --model_dir compile
# This command will use the default guidance used at the top of this file. In this case, 7 tasks.
srun --kill-on-bad-exit singularity exec --bind ${BIND_LOCATIONS} ${CEREBRAS_CONTAINER} python run.py --mode train --model_dir train --cs_ip ${CS_IP_ADDR}
You can save the above piece of code in a file, such as, neocortex_model.sbatch. _From your project code directory(same directory that has the _run.py file), run the following command:
sbatch neocortex_model.sbatch
You can check the status of your submitted job via the squeue command:
squeue -u PSC_USERNAME
For more information, please refer to the Bridges-2 user-guide https://www.psc.edu/resources/bridges-2/user-guide-2/#batch-jobs
Bridges-2¶
To compile your code using the Bridges-2 system, you have been assigned to a Neocortex allocation (Refer to Allocations section).
How to run an interactive session on Bridges2?¶
* Use the following sample command to start an interactive session on a Bridges-2 node for 8 GPUs for 30 minutes:
interact -p GPU --gres=gpu:8 -N 1 -t 30:00
How to run a batch job on Bridges2?¶
* Save the following script in a file, such as, jobname.
* #!/bin/bash
* #SBATCH -N 1
* #SBATCH -p GPU
* #SBATCH -t 5:00:00
* #SBATCH --gpus=8
* 
* #type 'man sbatch' for more information and options
* #this job will ask for 1 full GPU node(8 V100 GPUs) for 5 hours
* 
* #echo commands to stdout
* set -x
* 
* # move to working directory
* # this job assumes:
* # - all input data is stored in this directory
* # - all output should be stored in this directory
* # - please note that GRANT_ID should be replaced by your GRANT_ID
* # - PSC_USERNAME should be replaced by your PSC_USERNAME
* # - path-to-directory should be replaced by the path to your directory where the executable is
Then change to the directory from which you will be running the code.
cd /ocean/projects/GRANT_ID/PSC_USERNAME/path-to-directory

#run pre-compiled program which is already in your project space

./gpua.out
* Run the following command for starting the batch job using 8 GPUs on a single node for 5 hours:
* sbatch -p GPU -N 1 --gpus=8 -t 5:00:00 jobname
To find out more, please check out the instructions here: https://www.psc.edu/resources/bridges-2/user-guide-2/#running-jobs

