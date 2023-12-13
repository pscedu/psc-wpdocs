

## Reference resources
**/ocean/neocortex/cerebras/**

This space is the location in the shared filesystem in which the different reference resources will be placed. It contains the “modelzoo” directory from Cerebras, the datasets required for running those models, the Cerebras container, example scripts, and other helpful resources.

This will be the primary location of these resources.

**$LOCAL/cerebras**

Since the main reference resources (`/ocean/neocortex/cerebras`) have to be accessed over the network, copies were placed in the local storage available inside the SDF hosts. You can access the contents using this `$LOCAL/cerebras` location, as it will access the local folder that is expected to provide the highest performance for your job.

**Cerebras container**

This is a Singularity container created by Cerebras that contains all of the different resources required for running your code on the CS-2 boxes. The container is updated regularly and the latest version is always available in the reference folder as `/ocean/neocortex/cerebras/cbcore_latest.sif`.

Please see the [Software Stack section](https://www.psc.edu/resources/neocortex/docs/software-stack) for more details.

**Cerebras Datasets**

There are some common datasets available that are used by the sample models in the Cerebras modelzoo. To find those datasets, please refer to the locations `/ocean/neocortex/cerebras/` and `$LOCAL/cerebras/data`.

**Community datasets**

Additionally to the datasets provided by Cerebras, there are other datasets that are made available via Ocean from the Bridges-2 supercomputer. You can find: COCO, genomics, ImageNet, MNIST, nltk, prevent_a, and others under `/ocean/datasets/community`.

**Where to keep your custom datasets**

It is advisable to store datasets under the $PROJECT/../shared folder in the Ocean shared file system, and then to copy those that are required to the /local{1,2,3,4} spaces in the SDFlex when training on the CS-2. Please remember you can access the /local/{1..4} spaces via the $LOCAL variable.

For more details regarding how to run various jobs on the system, refer to the [Running jobs section](https://www.psc.edu/resources/neocortex/docs/running-jobs) of the User Guide.

