
### Neocortex
Connect to the login node.
```
ssh joeuser@neocortex.psc.edu
joeuser@neocortex.psc.edu's password: ****************

********************************* W A R N I N G ********************************
You have connected to one of the Neocortex login nodes.
LOG OFF IMMEDIATELY if you do not agree to the conditions stated in this warning
********************************* W A R N I N G ********************************
For documentation on Neocortex, please see https://portal.neocortex.psc.edu/docs/
Please contact neocortex@psc.edu with any comments/concerns.

[joeuser@neocortex-login023 ~]$
```


Take a look at the project grants available. Joeuser has two different grants available, one for a different research project, and one for Neocortex. We want to use the Neocortex grant for the following work, so we will specify it.

```
[joeuser@neocortex-login023 ~]$ projects | grep "Project\|Title"

    Project: CIS210012P 
      Title: A Very Important Project

    Project: CIS210015P                             # << This one
      Title: P99-Neocortex Research Project         # << This one
```

Let's take a look at the output of the `groups` command, since the groups are usually all lowercase but the `projects` output isn't.
```
[joeuser@neocortex-login023 ~]$ groups

    cis210012p cis210015p
```

"cis210012p" is showing as the first in that line (leftmost). That means that it's the primary group. We want the P## group to be used for all of the following commands, so let's run the `newgrp` command specifying it so that happens.
```
[joeuser@neocortex-login023 ~]$ newgrp cis210015p
```

Now, by running the `groups` command one more time we see that the "cis210015p" group is showing as primary, just like we need.
```
[joeuser@neocortex-login023 ~]$ groups
    cis210015p cis210012p
```

Since we have the correct group showing as primary, we can now proceed to start a job for copying files and running the actual compilation steps. This will start the SLURM job using the correct SLURM charge id (--account=GROUPID). 

We should start by setting some variables for copying the data.
```
[joeuser@neocortex-login023 ~]$ export CEREBRAS_DIR=/ocean/neocortex/cerebras/
[joeuser@neocortex-login023 ~]$ echo $PROJECT
    /ocean/projects/cis210015p/joeuser
```

In this case, we are copying the files by using `rsync`. The `rsync` command will update the target directory with any changes/updates from the origin path. That is not the case with `cp`. The `cp` command will complain if the target directory already exists. Also, if there are no new files under the `$CEREBRAS_DIR/modelzoo` directory, the output will only have "sending incremental file list" and nothing else will be transferred since the updated files are already in place. Additionally, please bear in mind that the "modelzoo" folder being copied should belong to the correct group after running the following commands. For this specific case, the modelzoo folder will belong to "cis210015p" and not to "cis210012p".
```
[joeuser@sdf-1 ~]$ rsync -PaL --chmod u+w $CEREBRAS_DIR/modelzoo $PROJECT/
    sending incremental file list
    modelzoo/
    modelzoo/LICENSE

    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

[joeuser@sdf-1 ~]$ ls $PROJECT/
    modelzoo
```

Move into the modelzoo folder of the model we want to evaluate/compile/train:
```
[joeuser@neocortex-login023 ~]$ cd $PROJECT/modelzoo/fc_mnist/tf
```

This command will start a shell using the latest Cerebras container.
```
[joeuser@neocortex-login023 tf]$ srun --pty --cpus-per-task=28 --kill-on-bad-exit singularity shell --cleanenv --bind /local1/cerebras/data,/local2/cerebras/data,/local3/cerebras/data,/local4/cerebras/data,$PROJECT /local1/cerebras/cbcore_latest.sif

Singularity>
```

Inside that shell, you will get to run the different validation and compilation commands. For example, for running a `validate_only` process:
```
Singularity> python run.py --mode train --validate_only --model_dir validate

    INFO:tensorflow:TF_CONFIG environment variable: {}
    Downloading and preparing dataset mnist (11.06 MiB) to cerebras/data/tfds/mnist/1.0.0...
    Dl Completed...: 100%|??????????????????????????????????????????????????????????????????????????????????????| 4/4 [00:00&lt;00:00, 12.50 url/s]
    Extraction completed...: 100%|?????????????????????????????????????????????????????????????????????????????| 4/4 [00:00&lt;00:00,  6.24 file/s]
    Extraction completed...: 100%|?????????????????????????????????????????????????????????????????????????????| 4/4 [00:00&lt;00:00,  5.84 file/s]
    Dl Size...: 100%|?????????????????????????????????????????????????????????????????????????????????????????| 10/10 [00:00&lt;00:00, 15.59 MiB/s]
    Dl Completed...: 100%|??????????????????????????????????????????????????????????????????????????????????????| 4/4 [00:00&lt;00:00,  6.23 url/s]
    0 examples [00:00, ? examples/s]2021-02-17 15:54:14.174234: I tensorflow/core/platform/cpu_feature_guard.cc:143] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX512F

    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

    XLA Extraction Complete
    =============== Starting Cerebras Compilation ===============                                                                            
    Cerebras compilation completed: 100%|??????????????????????????????????????????????????????????????????????????| 2/2 [00:02s,  1.12s/stages]
    =============== Cerebras Compilation Completed ===============

Singularity>
```

In the same way, a `compile_only` process looks like this:
```
Singularity> python run.py --mode train --compile_only --model_dir compile

    INFO:tensorflow:TF_CONFIG environment variable: {}

    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

    XLA Extraction Complete
    =============== Starting Cerebras Compilation ===============                                                                            
    Cerebras compilation completed: |                                                                           | 19/? [00:26s,  1.37s/stages]
    =============== Cerebras Compilation Completed ===============

Singularity>
```


Now the different parameter files used for the validation/compilation/training processes can be specified. Let's say that you don't want to use the default "configs/params.yaml" file but instead one in a different (custom) directory (`--params custom_configs/params.yaml`). This can be done by copying the original "params.yaml" file and editing the values in your copy. The contents of the output can also be written into a different path (`--model_dir custom_output_dir`):

```
Singularity> cp -r configs custom_configs

Singularity> vi custom_configs/params.yaml

Singularity> python run.py --mode train --compile_only --params custom_configs/params.yaml --model_dir custom_output_dir

    INFO:tensorflow:TF_CONFIG environment variable: {}

    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

    XLA Extraction Complete
    =============== Starting Cerebras Compilation ===============                                                                            
    Cerebras compilation completed: |                                                                           | 19/? [00:25s,  1.34s/stages]
    =============== Cerebras Compilation Completed ===============

Singularity>
```

The contents of the `custom_configs` and `custom_output_dir` have the parameters used and the output for this example compilation process. Please note that the group ownership is still pointing to the correct group ("cis210015p" for this example), since the account information to use was automatically passed to SLURM.
```
Singularity> ls -lash | grep custom
    4.0K drwxr-sr-x 2 joeuser cis210015p 4.0K Feb 17 17:07 custom_configs
    4.0K drwxr-sr-x 3 joeuser cis210015p 4.0K Feb 17 17:07 custom_output_dir

Singularity> ls -lsh custom*
    custom_configs:
    total 4.0K
    4.0K -rw-r--r-- 1 joeuser cis210015p 1.3K Feb 17 17:07 params.yaml

    custom_output_dir:
    total 16K
     12K drwxr-sr-x 4 joeuser cis210015p 12K Feb 17 17:08 cs_518e82fcc3928d8e9da4ffc039506e6f0019b41b46bc53085af34c080de4054e
    4.0K -rw-r--r-- 1 joeuser cis210015p 534 Feb 17 17:07 params.txt
```


Now to train the model (since it's compiling without issues), we can create the wrapper scripts that save time and also make sure the right syntax is used for SLURM, Singularity, and the Python command for training inside the container.

This wrapper is tailored for the setup of Neocortex:

```
[joeuser@neocortex-login023 tf]$ vim srun_train
    #!/usr/bin/bash
    srun --account=cis210015p --gres=cs:cerebras:1 --ntasks=7 --cpus-per-task=14 --kill-on-bad-exit singularity exec --bind /local1/cerebras/data,/local2/cerebras/data,/local3/cerebras/data,/local4/cerebras/data,$PROJECT /local1/cerebras/cbcore_latest.sif ./run_train "$@"

[joeuser@neocortex-login023 tf]$ vim run_train
    #!/usr/bin/bash
    python run.py --cs_ip ${CS_IP_ADDR} --mode train "$@"

[joeuser@neocortex-login023 tf]$ chmod +x srun_train run_train

[joeuser@neocortex-login023 tf]$ ./srun_train --model_dir training_example
    INFO:tensorflow:TF_CONFIG environment variable: {'cluster': {'chief': ['sdf-1:23111'], 'worker': ['sdf-1:23112', 'sdf-1:23113', 'sdf-1:23114', 'sdf-1:23115', 'sdf-1:23116', 'sdf-1:23117']}, 'task': {'type': 'chief', 'index': 0}}
    WARNING:tensorflow:From /cb/toolchains/buildroot/monolith-default/202010061651-75-61959232/rootfs-x86_64/usr/lib/python3.7/site-packages/tensorflow/python/ops/resource_variable_ops.py:1666: calling BaseResourceVariable.__init__ (from tensorflow.python.ops.resource_variable_ops) with constraint is deprecated and will be removed in a future version.
    Instructions for updating:
    If using Keras pass *_constraint arguments to layers.
    WARNING:tensorflow:From /cb/toolchains/buildroot/monolith-default/202010061651-75-61959232/rootfs-x86_64/usr/lib/python3.7/site-packages/tensorflow/python/training/training_util.py:236: Variable.initialized_value (from tensorflow.python.ops.variables) is deprecated and will be removed in a future version.
    Instructions for updating:
    Use Variable.read_value. Variables in 2.X are initialized automatically both in eager and graph (inside tf.defun) contexts.
    2021-02-20 17:58:56.115455: I tensorflow/core/platform/cpu_feature_guard.cc:143] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX512F
    2021-02-20 17:58:56.132002: I tensorflow/core/platform/profile_utils/cpu_utils.cc:102] CPU Frequency: 2700000000 Hz
    2021-02-20 17:58:56.133795: I tensorflow/compiler/xla/service/service.cc:168] XLA service 0x4800490 initialized for platform Host (this does not guarantee that XLA will be used). Devices:
    2021-02-20 17:58:56.133815: I tensorflow/compiler/xla/service/service.cc:176]   StreamExecutor device (0): Host, Default Version
    2021-02-20 17:58:56.133907: I tensorflow/core/common_runtime/process_util.cc:147] Creating new thread pool with default inter op setting: 2. Tune using inter_op_parallelism_threads for best performance.
    WARNING:root:[input_fn] - flat_map(): use map() instead of flat_map() to improve performance and parallelize reads. If you are not calling `flat_map` directly, check if you are using: from_generator, TextLineDataset, TFRecordDataset, or FixedLenthRecordDataset. If so, set `num_parallel_reads` to > 1 or tf.data.experimental.AUTOTUNE, and TF will use map() automatically.
    2021-02-20 17:58:57.534881: I tensorflow/core/platform/cpu_feature_guard.cc:143] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX512F
    2021-02-20 17:58:57.561731: I tensorflow/core/platform/profile_utils/cpu_utils.cc:102] CPU Frequency: 2700000000 Hz
    2021-02-20 17:58:57.563606: I tensorflow/compiler/xla/service/service.cc:168] XLA service 0x6af0720 initialized for platform Host (this does not guarantee that XLA will be used). Devices:
    2021-02-20 17:58:57.563627: I tensorflow/compiler/xla/service/service.cc:176]   StreamExecutor device (0): Host, Default Version
    2021-02-20 17:58:57.622282: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:267] number of function defs:1
    2021-02-20 17:58:57.622307: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:268] cluster_9063863211648629377
    2021-02-20 17:58:57.622313: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:269] xla args number:23
    2021-02-20 17:58:57.622317: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:270] fdef_args number:23
    2021-02-20 17:58:57.622321: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:275] fdef output mapping signature -> node_def: 
    2021-02-20 17:58:57.622325: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:277]  "mean_1_0_retval" -> "Mean_1:output:0"
    2021-02-20 17:58:57.688709: W tensorflow/compiler/tf2xla/kernels/random_ops.cc:52] Warning: Using tf.random.uniform with XLA compilation will ignore seeds; consider using tf.random.stateless_uniform instead if reproducible behavior is desired.
    XLA Extraction Complete
    =============== Starting Cerebras Compilation ===============
    Cerebras compilation completed: |          | 19/? [00:25s,  1.35s/stages]2021-02-20 17:59:25.699705: I tensorflow/core/common_runtime/process_util.cc:147] Creating new thread pool with default inter op setting: 2. Tune using inter_op_parallelism_threads for best performance.

    WARNING:tensorflow:From /cbcore/py_root/cerebras/tf/cs_estimator.py:558: Variable.load (from tensorflow.python.ops.variables) is deprecated and will be removed in a future version.
    Instructions for updating:
    Prefer Variable.assign which has equivalent behavior in 2.X.
    INFO:tensorflow:Create CheckpointSaverHook.
    INFO:tensorflow:Graph was finalized.
    2021-02-20 17:59:25.862027: I tensorflow/core/common_runtime/process_util.cc:147] Creating new thread pool with default inter op setting: 2. Tune using inter_op_parallelism_threads for best performance.
    INFO:tensorflow:Running local_init_op.
    INFO:tensorflow:Done running local_init_op.
    INFO:tensorflow:Calling checkpoint listeners before saving checkpoint 0...
    INFO:tensorflow:Saving checkpoints for 0 into training_example/model.ckpt.
    INFO:tensorflow:Calling checkpoint listeners after saving checkpoint 0...
    INFO:tensorflow:Programming CS-2 fabric. This may take couple of minutes - please do not interrupt.
    INFO:tensorflow:Fabric programmed
    INFO:tensorflow:Coordinator fully up. Waiting for Streaming (using 0.42% out of 308274 cores on the fabric)
    INFO:tensorflow:Graph was finalized.
    INFO:tensorflow:Running local_init_op.
    INFO:tensorflow:Done running local_init_op.
    INFO:tensorflow:Waiting for 6 streamer(s) to prime the data pipeline
    INFO:tensorflow:Streamers are ready
    INFO:tensorflow:global step 1: loss = 2.3671875 (1.19 steps/sec)
    INFO:tensorflow:global step 100: loss = 0.2467041015625 (89.75 steps/sec)
    INFO:tensorflow:global step 200: loss = 0.1527099609375 (167.0 steps/sec)

    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

    INFO:tensorflow:global step 99700: loss = 0.0003674030303955078 (471.25 steps/sec)
    INFO:tensorflow:global step 99800: loss = 0.038543701171875 (471.75 steps/sec)
    INFO:tensorflow:global step 99900: loss = 0.0 (472.0 steps/sec)
    INFO:tensorflow:Training finished with 25600000 samples in 211.863 seconds, 120832.56 samples / second
    INFO:tensorflow:Calling checkpoint listeners before saving checkpoint 100000...
    INFO:tensorflow:Saving checkpoints for 100000 into training_example/model.ckpt.
    INFO:tensorflow:Calling checkpoint listeners after saving checkpoint 100000...
    INFO:tensorflow:global step 100000: loss = 0.0823974609375 (471.75 steps/sec)
    INFO:tensorflow:global step 100000: loss = 0.0823974609375 (471.75 steps/sec)
    INFO:tensorflow:Loss for final step: 0.0824.
    =============== Cerebras Compilation Completed ===============
    [joeuser@neocortex-login023 tf]$
```


Finally, if you want to perform these steps in batch mode instead of interactively, you can run all of them from a single sbatch file, like this:
```
[joeuser@neocortex-login023 tf]$ vim mnist.sbatch
    #!/usr/bin/bash
    #SBATCH --gres=cs:cerebras:1
    #SBATCH --ntasks=7
    #SBATCH --cpus-per-task=14
    #SBATCH --account=cis210015p

    newgrp cis210015p 
    cp ${0} slurm-${SLURM_JOB_ID}.sbatch

    YOUR_DATA_DIR=${LOCAL}/cerebras/data
    YOUR_MODEL_ROOT_DIR=${PROJECT}/modelzoo/
    YOUR_ENTRY_SCRIPT_LOCATION=${YOUR_MODEL_ROOT_DIR}/fc_mnist/tf
    BIND_LOCATIONS=/local1/cerebras/data,/local2/cerebras/data,/local3/cerebras/data,/local4/cerebras/data,${YOUR_DATA_DIR},${YOUR_MODEL_ROOT_DIR}
    CEREBRAS_CONTAINER=/ocean/neocortex/cerebras/cbcore_latest.sif
    cd ${YOUR_ENTRY_SCRIPT_LOCATION}

    srun --ntasks=1 --kill-on-bad-exit singularity exec --bind ${BIND_LOCATIONS} ${CEREBRAS_CONTAINER} python run.py --mode train --validate_only --model_dir validate
    srun --ntasks=1 --kill-on-bad-exit singularity exec --bind ${BIND_LOCATIONS} ${CEREBRAS_CONTAINER} python run.py --mode train --compile_only --model_dir compile
    srun --kill-on-bad-exit singularity exec --bind ${BIND_LOCATIONS} ${CEREBRAS_CONTAINER} python run.py --mode train --model_dir training_example --cs_ip ${CS_IP_ADDR}

[joeuser@neocortex-login023 tf]$ sbatch mnist.sbatch 
    Submitted batch job 633

[joeuser@neocortex-login023 tf]$ squeue
     JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
       633       sdf mnist.sb    joeuser  R       0:02      1 sdf-1

[joeuser@neocortex-login023 tf]$ tail -f slurm-633.out
    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

    INFO:tensorflow:Cached compilation found for this model configuration

    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

    INFO:tensorflow:Running local_init_op.
    INFO:tensorflow:Done running local_init_op.
    INFO:tensorflow:Calling checkpoint listeners before saving checkpoint 100000...
    INFO:tensorflow:Saving checkpoints for 100000 into training_example/model.ckpt.
    INFO:tensorflow:Calling checkpoint listeners after saving checkpoint 100000...
    INFO:tensorflow:Programming CS-2 fabric. This may take couple of minutes - please do not interrupt.
    INFO:tensorflow:Fabric programmed
    INFO:tensorflow:Coordinator fully up. Waiting for Streaming (using 0.42% out of 308274 cores on the fabric)
    INFO:tensorflow:Graph was finalized.
    INFO:tensorflow:Running local_init_op.
    INFO:tensorflow:Done running local_init_op.
    INFO:tensorflow:Waiting for 6 streamer(s) to prime the data pipeline
    INFO:tensorflow:Streamers are ready
    INFO:tensorflow:global step 100001: loss = 0.0 (0.43 steps/sec)
    INFO:tensorflow:global step 100100: loss = 0.0 (37.72 steps/sec)
    INFO:tensorflow:global step 100200: loss = 0.0019931793212890625 (72.94 steps/sec)

    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

    INFO:tensorflow:global step 199700: loss = 0.0 (470.75 steps/sec)
    INFO:tensorflow:global step 199800: loss = 3.814697265625e-06 (471.25 steps/sec)
    INFO:tensorflow:global step 199900: loss = 3.814697265625e-06 (469.0 steps/sec)
    INFO:tensorflow:Training finished with 25600000 samples in 213.044 seconds, 120162.88 samples / second
    INFO:tensorflow:Calling checkpoint listeners before saving checkpoint 200000...
    INFO:tensorflow:Saving checkpoints for 200000 into training_example/model.ckpt.
    INFO:tensorflow:Calling checkpoint listeners after saving checkpoint 200000...
    INFO:tensorflow:global step 200000: loss = 0.0 (469.0 steps/sec)
    INFO:tensorflow:global step 200000: loss = 0.0 (469.0 steps/sec)
    INFO:tensorflow:Loss for final step: 0.0.
```

As it can be seen on the output above, the previous training was resumed since the initial checkpoint finished at step 100000 and we ran the batch job specifying the same model output directory (`--model_dir training_example`).

