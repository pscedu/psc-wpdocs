

#### Checkpointing jobs and viewing results

You can specify a directory for logs and training artifacts in the config `params.yaml` or via a command line argument (i.e. `-o` or `--model_dir`) passed to `run.py`. The default location is `model_dir` directory. This `model_dir` directory will be created in the directory that contains the `run.py` script.
```
 ./srun_train --model_dir train
```
**Viewing results on TensorBoard:**
* Allocate a node and launch within the singularity shell, with ssh tunneling.

    On sdf-1:
    ```
    salloc_node tensorboard --logdir new_dir --port 16007
    ```
    On Neocortex login node:
    ```
    ssh -L 16007:127.0.0.1:16007 sdf-1
    ```
    On your local machine:
    ```
    ssh -L 16007:127.0.0.1:16007 PSC_USERNAME@neocortex.psc.edu
    ```

Please note that you can't always use 127.0.0.1 for ssh tunneling. For example, when you run tensorboard on Bridges-2 it launches at something like `http://brxxx.ib.bridges2.psc.edu:6009`, and not `127.0.0.1:6009` so the porting command should be: `ssh -L 16007:http://brxxx.ib.bridges2.psc.edu:6009`.

