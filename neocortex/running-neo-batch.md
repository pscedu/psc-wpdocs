

#### Running batch jobs
In order to validate, compile, train, or evaluate your model using a batch script, please use the following sample piece of code that you can modify accordingly:

**neocortex_model.sbatch**
```
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
```

You can save the above piece of code in a file, such as `neocortex_model.sbatch`. 

From your project code directory(same directory that has the `run.py` file), run the following command:
```
sbatch neocortex_model.sbatch
```

You can check the status of your submitted job via the `squeue` command:
```
squeue -u PSC_USERNAME
```

For more information, please refer to the [Batch jobs section of the Bridges-2 User Guide](https://www.psc.edu/resources/bridges-2/user-guide/#batch-jobs).


