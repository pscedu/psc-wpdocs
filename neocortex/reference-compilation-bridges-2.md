

## Bridges-2

Connect to the login node.
```
ssh joeuser@bridges2.psc.edu
joeuser@bridges2.psc.edu's password: ****************
********************************* W A R N I N G ********************************
You have connected to br012.ib.bridges2.psc.edu, a login node of Bridges 2.
LOG OFF IMMEDIATELY if you do not agree to the conditions stated in this warning
********************************* W A R N I N G ********************************
[---OUTPUT SNIPPED---]

Projects
------------------------------------------------------------
Project: cis210012p PI: Paola Buitrago ***** default charging project *****
  Extreme Memory             1,000 SU remain of 1,000 SU        active: Yes
  GPU AI                     2,500 SU remain of 2,500 SU        active: Yes
  Regular Memory            49,999 SU remain of 50,000 SU       active: Yes
  Ocean /ocean/projects/cis210012p 14.43G used of 1000G 
Project: cis210015p PI: Paola Buitrago
  Extreme Memory             1,000 SU remain of 1,000 SU        active: Yes
  GPU AI                     2,500 SU remain of 2,500 SU        active: Yes
  Regular Memory            50,000 SU remain of 50,000 SU       active: Yes
  Ocean /ocean/projects/cis210015p 26.97G used of 1000G

[joeuser@bridges2-login012 ~]$
```
<div class="note">
  <blockquote>
    <strong>Note</strong>
<p>Please keep in mind that your Neocortex allocation/account has access to more than one partition, RM for Regular Memory and EM for Extreme Memory. The default one to use should be the RM one, since there are more SUs available there, and you should only switch to the EM one if needed after testing everything on RM so your SUs don't run out prematurely. Additionally, the EM partition does not allow you to run commands interactively via the `interact` command, thus you will be required to either submit in batch mode, or to run the `srun` command shown below.</p>
  </blockquote>
</div>

Take a look at the project grants available. Joeuser has two different grants available, one for a different research project, and one for Neocortex. We want to use the Neocortex grant for the following work, so we will specify it.
```
[joeuser@bridges2-login012 ~]$ projects | grep "Project\|Title"
    Project: CIS210012P
      Title: A Very Important Project

    Project: CIS210015P                             # << This one
      Title: P99-Neocortex Research Project         # << This one
```

Let's take a look at the output of the `groups` command, since the groups are usually all lowercase but the `projects` command output isn't.
```
[joeuser@bridges2-login012 ~]$ groups
    cis210012p hum180001p ast190019p cis200012p cis210015p
```

"cis210012p" is showing as the first in that line (leftmost). That means that it's the primary group. What we want is to have the P## group to be the primary for all of the following commands, so let's run the `newgrp` command specifying it so that happens.
```
[joeuser@bridges2-login012 ~]$ newgrp cis210015p
```

Now, by running the `groups` command one more time we see that the "cis210015p" group is showing as primary, just like we need.
```
[joeuser@bridges2-login012 ~]$ groups
    cis210015p hum180001p ast190019p cis200012p cis210012p
```

Since we have the correct group showing as primary, we can now proceed to start a job for copying files and running the actual compilation steps. This will start the SLURM job using the correct SLURM charge id (`--account=GROUPID`).

We should start by running a simple interact job while specifying the allocation to use, like this:
```
[joeuser@bridges2-login012 ~]$ CEREBRAS_DIR=/ocean/neocortex/cerebras/
[joeuser@bridges2-login012 ~]$ interact -A cis210015p -p RM

    A command prompt will appear when your session begins
    
    "Ctrl+d" or "exit" will end your session

    --partition=RM
    salloc -J Interact --partition=RM
    salloc: Pending job allocation 319524
    salloc: job 319524 queued and waiting for resources
    salloc: job 319524 has been allocated resources
    salloc: Granted job allocation 319524
    salloc: Waiting for resource configuration
    salloc: Nodes r051 are ready for job

[joeuser@r051 ~]$
```

<div class="note">
  <blockquote>
    <strong>Note</strong>
    <p>lease remember that the interactive mode can only be used for RM nodes. For EM nodes, the batch mode has to be used.</p>
  </blockquote>
</div>

As seen from the previous output, the prompt changed from saying that we were in a the "bridges2-login012" node to "r051" on the RM partition. It's now time to set some variables for copying the data.
```
[joeuser@r051 ~]$ CEREBRAS_DIR=/ocean/neocortex/cerebras/
[joeuser@r051 ~]$ echo $PROJECT
    /ocean/projects/cis210015p/joeuser
```


In this case, we are copying the files by using rsync. The rsync command will update the target directory with any changes/updates from the origin path. That is not the case with cp. The cp command will complain if the target directory already exists. Also, if there are no new files under the `$CEREBRAS_DIR/modelzoo` directory, the output will only have "sending incremental file list" and nothing else will be transferred since the updated files would already be in place. Additionally, please bear in mind that the "modelzoo" folder being copied should belong to the correct group after running the following commands. For this specific case, to "cis210015p" and not to "cis210012p".
```
[joeuser@r051 ~]$ rsync -PaL --chmod u+w $CEREBRAS_DIR/modelzoo $PROJECT/
    sending incremental file list
    modelzoo/
    modelzoo/LICENSE

    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

[joeuser@r051 ~]$ ls $PROJECT/
    modelzoo
```


Since the information is already in place, we should exit that simple interactive mode and start the actual compilation with more resources. We can exit that interactive mode by typing `exit` or pressing `Ctrl+D`.
```
[joeuser@r051 ~]$ exit
    exit
    salloc: Relinquishing job allocation 319526

[joeuser@bridges2-login012 ~]$
```
<br />
Move into the modelzoo folder of the model we want to evaluate/compile/train:
```
[joeuser@bridges2-login012 ~]$ cd $PROJECT/modelzoo/fc_mnist/tf
```

This command will start a shell using the latest Cerebras container. Please bear in mind that it might take a while for the job to start:
```
[joeuser@bridges2-login012 tf]$ srun --pty --cpus-per-task=28 --account=cis210015p --partition=RM --kill-on-bad-exit singularity shell --cleanenv --bind $CEREBRAS_DIR/data,$PROJECT $CEREBRAS_DIR/cbcore_latest.sif
    srun: job 319618 queued and waiting for resources
    srun: job 319618 has been allocated resources

Singularity>
```

Inside that shell, you will get to run the different validation and compilation commands. For example, for running a `validate_only` process:
```
Singularity> python run.py --mode train --validate_only --model_dir validate

    INFO:tensorflow:TF_CONFIG environment variable: {}
    Downloading and preparing dataset mnist (11.06 MiB) to cerebras/data/tfds/mnist/1.0.0...
    Dl Completed...: 100%|?????????????????????????????????????????????????????????????????| 4/4 [00:00<00:00, 23.65 url/s]
    Extraction completed...: 100%|?????????????????????????????????????????????????????????| 4/4 [00:00<00:00,  6.29 file/s]
    Extraction completed...: 100%|?????????????????????????????????????????????????????????| 4/4 [00:00<00:00,  5.81 file/s]
    Dl Size...: 100%|??????????????????????????????????????????????????????????????????????| 10/10 [00:00<00:00, 15.72 MiB/s]
    Dl Completed...: 100%|?????????????????????????????????????????????????????????????????| 4/4 [00:00<00:00,  6.28 url/s]
    0 examples [00:00, ? examples/s]2021-03-01 17:53:53.757037: I tensorflow/core/platform/profile_utils/cpu_utils.cc:102] CPU Frequency: 2245750000 Hz

    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

    XLA Extraction Complete
    =============== Starting Cerebras Compilation ===============                                                                                                                                                   
    Cerebras compilation completed: 100%|??????????????????????????????????????????????????| 2/2 [00:04s,  2.03s/stages]
    =============== Cerebras Compilation Completed ===============

Singularity>
```

In the same way, a `compile_only` process looks like this:
```
Singularity> python run.py --mode train --compile_only --model_dir compile

    INFO:tensorflow:TF_CONFIG environment variable: {}
    WARNING:root:[input_fn] - flat_map(): use map() instead of flat_map() to improve performance and parallelize reads. If you are not calling `flat_map` directly, check if you are using: from_generator, TextLineDataset, TFRecordDataset, or FixedLenthRecordDataset. If so, set `num_parallel_reads` to > 1 or tf.data.experimental.AUTOTUNE, and TF will use map() automatically.
    WARNING:tensorflow:From /cb/toolchains/buildroot/monolith-default/202010061651-75-61959232/rootfs-x86_64/usr/lib/python3.7/site-packages/tensorflow/python/ops/resource_variable_ops.py:1666: calling BaseResourceVariable.__init__ (from tensorflow.python.ops.resource_variable_ops) with constraint is deprecated and will be removed in a future version.
    Instructions for updating:
    If using Keras pass *_constraint arguments to layers.
    2021-03-01 17:56:29.146050: I tensorflow/core/platform/profile_utils/cpu_utils.cc:102] CPU Frequency: 2245750000 Hz
    2021-03-01 17:56:29.151928: I tensorflow/compiler/xla/service/service.cc:168] XLA service 0x6308140 initialized for platform Host (this does not guarantee that XLA will be used). Devices:
    2021-03-01 17:56:29.151990: I tensorflow/compiler/xla/service/service.cc:176]   StreamExecutor device (0): Host, Default Version
    2021-03-01 17:56:29.182298: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:267] number of function defs:1
    2021-03-01 17:56:29.182327: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:268] cluster_9063863211648629377
    2021-03-01 17:56:29.182337: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:269] xla args number:23
    2021-03-01 17:56:29.182344: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:270] fdef_args number:23
    2021-03-01 17:56:29.182350: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:275] fdef output mapping signature -> node_def: 
    2021-03-01 17:56:29.182357: I tensorflow/tools/xla_extract/tf_graph_to_xla_lib.cc:277]  "mean_1_0_retval" -> "Mean_1:output:0"
    2021-03-01 17:56:29.187951: W tensorflow/compiler/tf2xla/kernels/random_ops.cc:52] Warning: Using tf.random.uniform with XLA compilation will ignore seeds; consider using tf.random.stateless_uniform instead if reproducible behavior is desired.
    XLA Extraction Complete
    INFO:tensorflow:Cached compilation found for this model configuration

Singularity>
```
<br /><br />
Now, the different parameter files used for the validation/compilation/training processes can be specified. Let's say that you dont want to use the default "configs/params.yaml" file but one in a different (custom) directory (`--params custom_configs/params.yaml`). This can be done by copying the original "params.yaml" file and editing the values in your copy. The contents of the output can also be written into a different path (`--model_dir custom_output_dir`):
```
Singularity> cp -r configs custom_configs

Singularity> vi custom_configs/params.yaml

Singularity> python run.py --mode train --compile_only --params custom_configs/params.yaml --model_dir custom_output_dir

    INFO:tensorflow:TF_CONFIG environment variable: {}

    [--- OUTPUT SNIPPED FOR KEEPING THIS EXAMPLE SHORT ---]

    XLA Extraction Complete
    =============== Starting Cerebras Compilation ===============                                                                            
    Cerebras compilation completed: |                                                                                                                                                 | 19/? [00:31s,  1.63s/stages]
    =============== Cerebras Compilation Completed ===============

Singularity>
```

The contents of the `custom_configs` and `custom_output_dir` have the parameters used and the output for this example compilation process. Please note that the group ownership is still pointing to the correct group ("cis210015p" for this example), since the account information to use was automatically passed to SLURM.
```
Singularity> ls -lash | grep custom
    4.0K drwxr-sr-x  2 joeuser cis210015p 4.0K Mar  1 17:57 custom_configs
    4.0K drwxr-sr-x  3 joeuser cis210015p 4.0K Mar  1 17:58 custom_output_dir

Singularity> ls -lsh custom*
    custom_configs:
    total 4.0K
    4.0K -rw-r--r-- 1 joeuser cis210015p 1.3K Mar  1 17:57 params.yaml

    custom_output_dir:
    total 16K
     12K drwxr-sr-x 4 joeuser cis210015p 12K Mar  1 17:58 cs_518e82fcc3928d8e9da4ffc039506e6f0019b41b46bc53085af34c080de4054e
    4.0K -rw-r--r-- 1 joeuser cis210015p 534 Mar  1 17:58 params.txt
```

<br />
Now you are ready to the model (since it's compiling without issues). However, **this training cannot be done using Bridges-2**. You will have to connect to Neocortex and follow the steps shown for training in the [Reference Compilation Example: Using Neocortex](https://www.psc.edu/resources/neocortex/docs/reference-compilation-commands/#neocortex) section.

Finally, if you want to perform these steps in batch mode instead of interactively via `srun`, you can ran all of them from a single sbatch file. This will allow us to use the Extreme Memory nodes in the EM partition, like this:
```
 git clone git@github.com:Cerebras/modelzoo.git
[joeuser@neocortex-login023 tf]$ vim mnist.sbatch
    #!/usr/bin/bash
    #SBATCH --cpus-per-task=28
    #SBATCH --account=cis210015p
    #SBATCH --partition=EM
    #SBATCH --time=60:00

    newgrp cis210015p
    cp ${0} slurm-${SLURM_JOB_ID}.sbatch

    CEREBRAS_DIR=/ocean/neocortex/cerebras/
    rsync -PaL --chmod u+w $CEREBRAS_DIR/modelzoo $PROJECT/

    YOUR_DATA_DIR=$CEREBRAS_DIR/data
    YOUR_MODEL_ROOT_DIR=${PROJECT}/
    YOUR_ENTRY_SCRIPT_LOCATION=${YOUR_MODEL_ROOT_DIR}/modelzoo/fc_mnist/tf
    BIND_LOCATIONS=${YOUR_DATA_DIR},${YOUR_MODEL_ROOT_DIR},/local
    CEREBRAS_CONTAINER=$CEREBRAS_DIR/cbcore_latest.sif
    cd ${YOUR_ENTRY_SCRIPT_LOCATION}

    srun --ntasks=1 --kill-on-bad-exit singularity exec --bind ${BIND_LOCATIONS} ${CEREBRAS_CONTAINER} python run.py --mode train --validate_only --model_dir validate
    srun --ntasks=1 --kill-on-bad-exit singularity exec --bind ${BIND_LOCATIONS} ${CEREBRAS_CONTAINER} python run.py --mode train --compile_only --model_dir compile
```

<div class="note">
  <blockquote>
    <strong>Note</strong>
    <p>If you run into problems when running jobs on Bridges-2, please remember to also take a look at the <a href="https://www.psc.edu/resources/bridges-2/user-guide">Bridges-2 User Guide</a>.</p>
  </blockquote>
</div>
