
Load the Conda module by loading either the Community or the Enterprise version commands. After that, activate the
base conda environment if you want to use the base packages included with Anaconda, or create a new
environment yourself.

The specific instructions for activating each module can be found when running the command "`module help MODULENAME`".


```shell
# Python 3
module load anaconda3
conda activate
```

Note: The `"anaconda3"` module makes use of Python 3. To use Python 2, load `"anaconda2"` instead. However, we recommend that you convert the project to Python 3 instead. 

