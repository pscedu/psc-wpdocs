## Data Management and Storage 
This section talks about the various storage spaces on Neocortex and how to use them.
### Common Storage Spaces
#### $HOME
Your $HOME directory is <code>/jet/home/<i>username</i></code>, where *username* is your PSC username. This is your personal storage space and has a cap of 25 GB. This should be used for personal files only, such as system configurations or maybe private files like experimental Conda environments.

**Note:** The $HOME and $PROJECTS spaces are persistent, but the /local{1,2,3,4} spaces (/local1/, /local2/, /local3/, /local4/) are more ephemeral.

**Warning:** This storage space is subject to the [Bridges-2 storage inode quota](https://www.psc.edu/resources/bridges-2/user-guide/#inode-quota).

**Danger:** 
Please have in mind that maxing out your $HOME quota (storing 25GB+ under $HOME) will lock you out of the system, and you might not be able to login anymore.

#### $PROJECT
Your $PROJECT directory is at /ocean/projects/*groupname*/*username*, where *groupname* is the Unix group id associated with your allocation, and *username* is your PSC username. 

This storage space is for storing large amounts of data and bigger files (>25GB) that are not required by the other team members. This space is the main userspace (private to each user) that charges against the group quota instead of the limited personal quota.

**Warning:**  This storage space is subject to the [Bridges-2 storage inode quota](https://www.psc.edu/resources/bridges-2/user-guide/#inode-quota).

If you have access to multiple projects on PSC systems, please make sure to execute the `newgrp` command before using the $PROJECT variable, as described in the Accounts section.

##### $PROJECT/../shared
This is a space inside the project that is writable by everyone in your specific group. This location is created by default, and it is the same type of space that was available on Phase 0 with the P## folders (also referred to as team_folder).

This directory can also be referred to as <code>/ocean/projects/<em>groupname</em>/shared</code>, where *groupname* is the Unix group id associated with your allocation.

**Note:** The $HOME and $PROJECTS spaces are persistent, but the /local{1,2,3,4} spaces (/local1/, /local2/, /local3/, /local4/) are more ephemeral.

#### /local{1,2,3,4}
This storage is visible only from the SDFlex compute nodes. Using this space benefits from an extremely fast data transfer speed.

There are four local folders on each SDFlex node: /local1, /local2, /local3, and /local4, on each of the two SDF socket partitions. The path to these locations can also be accessed via the $LOCAL environment variable.

The idea of the $LOCAL space is to locally “cache” specific contents of the Ocean shared file system so the tasks performed on the SDF hosts use as many local resources as possible for the best performance.

Since each job task started via SLURM will have different NUMA-to-storage affinity based on the CPU cores allocated, it could be that using /localX instead of /localY for your specific task could have better performance. Having this in mind, a dynamic affinity mapping script is automatically started at the beginning of each job for setting the $LOCAL environment variable to point to the /local{1..4} location that is expected to be fastest every time.

Example: 
```echo $LOCAL
/local3
```

These storage spaces are referred to as /local{1..4}, /local[1..4], or /local{1,2,3,4}; and /localX or $LOCAL. The difference between them is that when using /local and a number from 1 through 4, all of the 4 different disk spaces are being referred to; and when using /localX or $LOCAL, only one of the spaces is being addressed.

**Note:** The $HOME and $PROJECTS spaces are persistent, but the /local{1,2,3,4} spaces (/local1/, /local2/, /local3/, /local4/) are more ephemeral.

It is recommended that you move your dataset from Bridges-2 (ocean or jet) to one of the locals (/local[1-4]) on the SDF node to avoid data I/O bottleneck from Bridges-2. The `mcp` command should be run from inside the SDF as anywhere else it will not have access to the /local disks. In order to do that, you would have to ssh into Neocortex and start an interact session like this:

<pre>ssh <em>username</em>@neocortex.psc.edu
 srun --pty bash -i
 /ocean/neocortex/tests/tools/mcp -r --direct-read --direct-write --print-stats --threads=120 /path/to/dataset/on/ocean/or/jet /local[1-4]/<i>groupname</i>
</pre>
Exit the interactive session when the data transfer is finished.

You can use any of the /locals for this step. An important note is to transfer your dataset to both SDFs (sdf-1 and sdf-2) as when you run a SLURM job to train your model, it will pick up any of the available partitions (sdf-1 or sdf-2)

### Reference resources
**/ocean/neocortex/cerebras/**

This space is the location in the shared filesystem in which the different reference resources will be placed. It contains the “modelzoo” directory from Cerebras, the datasets required for running those models, the Cerebras container, example scripts, and other helpful resources.

This will be the primary location of these resources.

**$LOCAL/cerebras**

Since the main reference resources (`/ocean/neocortex/cerebras`) have to be accessed over the network, local copies were placed in the local storage available inside the SDF hosts. You can access the contents using this `$LOCAL/cerebras` location, as it will access the local folder that is expected to provide the highest performance for your job.

**Cerebras container**

This is a Singularity container created by Cerebras that contains all of the different resources required for running your code on the CS-2 boxes. The container is updated regularly and the latest version is always available in the reference folder as `/ocean/neocortex/cerebras/cbcore_latest.sif`.

Please see the “Software Stack” section for more details.

**Cerebras Datasets**

There are some common datasets available that are used by the sample models in the Cerebras modelzoo. To find those datasets, please refer to the locations `/ocean/neocortex/cerebras/` and `$LOCAL/cerebras/data`.

**Community datasets**

Additionally to the datasets provided by Cerebras, there are other datasets that are made available via Ocean from the Bridges-2 supercomputer. You can find: COCO, genomics, ImageNet, MNIST, nltk, prevent_a, and other under `/ocean/datasets/community`.

**Where to keep your custom datasets**

It is advisable to store datasets under the $PROJECT/../shared folder in the Ocean shared file system, and then to copy those that are required to the /local{1,2,3,4} spaces in the SDFlex when training on the CS-2. Please remember you can access the /local/{1..4} spaces via the $LOCAL/ variable.

For more details regarding how to run various jobs on the system, refer to the Running jobs section of the User Guide.

### Transferring Data
#### How to move datasets into the Neocortex system and back
You can use the Neocortex Data Transfer Nodes (DTN) for transferring data to and from the Neocortex system. You can use rsync (preferred) or sftp commands for the same. Using scp is not recommended.

Every grant folder `/ocean/projects/*groupname*/` has a shared file space (for everyone in the group to use) and personal user space. To use the shared space for storing datasets and files you want to share with your team, look for the shared folder under your group folder ($PROJECT/../shared/).

There are three ways to transfer files to/from Neocortex: rsync (preferred), sftp or scp (not recommended). Below are some sample commands you can refer to.

**rsync**
<pre>rsync -PaL --chmod u+w /local-path/to/dataset <em>username</em>@data.neocortex.psc.edu:/ocean/projects/<em>groupname</em>/shared/</pre>
where *groupname* is your grant id and *username* is your PSC username.

Note that by default, rsync will not copy older files with the same name in place of newer files in the target directory.

You can find the grant id by running the `projects` command. Please refer to [https://www.psc.edu/resources/bridges-2/user-guide/#account-administration](https://www.psc.edu/resources/bridges-2/user-guide/#account-administration) for more information.

**sftp**

sftp file transfer is also supported. For more details, please visit https://www.psc.edu/resources/bridges-2/user-guide/#transferring-files.

