

## HPC Workflows - Cerebras SDK
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

