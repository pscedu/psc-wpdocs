
  

### How to train your model

In order to train your model, use the following wrapper scripts with predefined worker and singularity settings. The first script is an `srun` command that takes care of setting up all of the different parameters for the container to be successfully started, while the second script has the static parts of the python command used for the training to start.

**srun_train**
```
#!/usr/bin/bash

srun --gres=cs:cerebras:1 --ntasks=7 --cpus-per-task=14  --kill-on-bad-exit singularity exec --bind /local1/cerebras/data,/local2/cerebras/data,/local3/cerebras/data,/local4/cerebras/data,$PROJECT /local1/cerebras/cbcore_latest.sif ./run_train "$@"
```

**run_train**
```
#!/usr/bin/bash
python run.py --cs_ip ${CS_IP_ADDR} --mode train "$@"
```

The first script could be saved as `srun_train`, and the second script could be saved as `run_train`.


* Make sure both of them have file executable permissions.
    ```
   chmod +x srun_train run_train
   ```

* Now run the following command from your project directory to launch training from scratch:
    ```
    ./srun_train --model_dir OUTPUT_DIR
    ```
    where `--model_dir OUTPUT_DIR` will be the location where the output from the training will be located. Also, if you want to restart from a checkpoint, just use the same output directory with this parameter. 
Note that `--model_dir` is the same as using `-o`.

    In order to launch training from pre-compiled artifacts, specify `--model_dir` with the output directory you used while compiling, `compile` in this case (refer to the subsection above).
    ```
    ./srun_train --model_dir compile
    ```

For evaluation purposes:
```
python run.py --mode eval --model_dir train
```

