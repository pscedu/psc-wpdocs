

### How to run a batch job on Bridges2

1. Save the following script in a file.
<pre>
 #!/bin/bash
 #SBATCH -N 1
 #SBATCH -p GPU
 #SBATCH -t 5:00:00
 #SBATCH --gpus=8
 
 #type 'man sbatch' for more information and options
 #this job will ask for 1 full GPU node(8 V100 GPUs) for 5 hours
 
 #echo commands to stdout
 set -x
 
 # move to working directory
 # this job assumes:
 # - all input data is stored in this directory
 # - all output should be stored in this directory
 # - please note that GRANT_ID should be replaced by your GRANT_ID
 # - PSC_USERNAME should be replaced by your PSC_USERNAME
 # - path-to-directory should be replaced by the path to your directory where the executable is
</pre>


2. Change to the directory from which you will be running the code.
<pre>
cd /ocean/projects/GRANT_ID/PSC_USERNAME/path-to-directory

#run pre-compiled program which is already in your project space

./gpua.out
</pre>

 
3. Run the following command for starting the batch job using 8 GPUs on a single node for 5 hours:
<pre>
  sbatch -p GPU -N 1 --gpus=8 -t 5:00:00 jobname
</pre>


To find out more, please check out the instructions in the [Running jobs section of the Bridges-2 User Guide](https://www.psc.edu/resources/bridges-2/user-guide/#running-jobs).
