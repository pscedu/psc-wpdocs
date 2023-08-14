## Software Stack
This section describes how to load/create the packages required for specific computing environments for performing your runs. Researchers are expected to work with a particular singularity container for their code runs.

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

### HPC Workflows - Cerebras SDK
The required variables should be already present in the system if you started a Slurm job. If not, you can set them like this:
```
SDK_INSTALL_LOCATION=/ocean/neocortex/cerebras
SDK_INSTALL_PATH=$SDK_INSTALL_LOCATION/sdk
```
Then, the commands required for setting the path to the different tools to be used with the SDK are defined like this:
```
PATH_CMD='export PATH='$SDK_INSTALL_PATH':$PATH'
eval $PATH_CMD
```
And if you want the SDK configuration to persist across sessions, run the following command to update the PATH variable in your .bashrc file:
```
echo $PATH_CMD >> ~/.bashrc
```
Now, for starting the process, you can take a look at the contents under the $SDK_INSTALL_PATH folder: `csl_examples/code-samples`. It contains multiple simple examples (20). For example:
```
cd ${SDK_INSTALL_PATH}/csl_examples/code-samples/01-tasks-and-colors
```

#### Compile
For the CS-2s, the flags should be set to `--arch=wse2` and `--fabric-dims=757,996` like the following:
```
cslc --arch=wse2 ./code.csl --fabric-dims=757,996 --fabric-offsets=1,1 --colors=x_in:1,b_in:2,y_out:3,Ax_out:4,sentinel:43 -o out
```
#### Run
For passing the CS-2 IP addresses, please use the variable `CS_IP_ADDR`, along with `--fabdims 757,996` like the following:
```
cs_python run.py --name out --cmaddr ${CS_IP_ADDR}:9000 --fabdims 757,996
```
