## Software Stack
This section describes how to load/create the packages required for specific computing environments for performing your runs. Researchers are expected to work with a particular singularity container for their code runs.

**In this section:**
* [HPC Workflows - Cerebras SDK](#hpc-workflows-cerebras-sdk)
   * [Compile](#compile)
   * [Run](#run)

### What are these Singularity containers?
These are stand-alone packages holding the software needed to create a very specific computing environment.

**What container should be used?**

We recommend using the latest version of the main Cerebras container to advance the code compilation. It can be found in the following paths:
* Inside the reference folder on the Ocean shared file system:
`/ocean/neocortex/cerebras/cbcore_latest.sif`
* Inside the Neocortex SDFs:
`/local[1-4]/cerebras/cbcore_latest.sif`

Please note that this is a symlink and not the actual container file. If you want to check the file path or container version, you can use the `ll` command.

**What software is compatible?**
* The TensorFlow version certified as compatible with the modelzoo is TensorFlow version 2.2.
* The PyTorch version certified as compatible with the modelzoo is version 1.11.

