

#### /local{1,2,3,4}
This storage is visible only from the SDFlex compute nodes. Using this space benefits from an extremely fast data transfer speed.

There are four local folders on each SDFlex node: /local1, /local2, /local3, and /local4, on each of the two SDF socket partitions. This space can be accessed via the $LOCAL environment variable.

The idea of the $LOCAL space is to locally cache specific contents of the Ocean shared file system so the tasks performed on the SDF hosts use as many local resources as possible for the best performance.

Since each job task started via SLURM will have different NUMA-to-storage affinity based on the CPU cores allocated, it could be that using /local*X* instead of /local*Y* for your specific task could produce better performance. Having this in mind, a dynamic affinity mapping script is automatically started at the beginning of each job to set the $LOCAL environment variable to point to the /local{1..4} location that is expected to be fastest every time.

Example: 
```
echo $LOCAL
/local3
```

These storage spaces are referred to as /local{1..4}, /local[1..4], or /local{1,2,3,4}; and /localX or $LOCAL. The difference between them is that when using /local and a number from 1 through 4, all of the 4 different disk spaces are being referred to; and when using /localX or $LOCAL, only one of the spaces is being addressed.

**Note:** The $HOME and $PROJECTS spaces are persistent, but the /local{1,2,3,4} spaces (/local1/, /local2/, /local3/, /local4/) are more ephemeral.

It is recommended that you move your dataset from Bridges-2 (ocean or jet) to one of the locals (/local[1-4]) on the SDF node to avoid data I/O bottleneck from Bridges-2. The `mcp` command should be run from inside the SDF as anywhere else it will not have access to the /local disks. In order to do that, ssh into Neocortex and start an interact session like this:

<pre>ssh <em>username</em>@neocortex.psc.edu
 srun --pty bash -i
 /ocean/neocortex/tests/tools/mcp -r --direct-read --direct-write --print-stats --threads=120 /path/to/dataset/on/ocean/or/jet /local[1-4]/<i>groupname</i>
</pre>
Exit the interactive session when the data transfer is finished.

You can use any of the /locals for this step. 

> **Important**
>
>  Transfer your dataset to both SDFs (sdf-1 and sdf-2) as when you run a SLURM job to train your model, it will pick up any of the available partitions (sdf-1 or sdf-2).

