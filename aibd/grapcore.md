# System configuration
## Server

[Exxact TS2-158632687-AES](https://www.exxactcorp.com/Exxact-TS2-158632687-E158632687)
- 2x AMD EPYC Milan 7543 - 32-Core, 64-Threads, 2.80GHz Base, 256MB Cache - 225w
- 16x 32GB (512GB total) DDR4 3200MHz ECC REG Memory Module
- 1x 240GB 2.5in SATA SSD (OS)
- 2x 3.84TB U.2 2.5in NVMe PCIe 3.0 SSD
- 2x AMD 100-506116 Instinct MI100 Graphic Card - 32 GB HBM2 - PCIe 4 
- 1x NVIDIA ConnectX-6 VPI adapter card HDR IB (200Gb/s) and 200GbE single-port QSFP56 PCIe4.0 x16 tall bracket ROHS R6

## Accelerator
[1U BOW-2000 IPU System](https://www.graphcore.ai/products/bow-2000)
- 4x BOW IPUs - 1.4 PetaFLOPS FP16.16 AI Compute
- 5,888 processor cores
- 35,000 independent parallel threads
- Up to ~450GB Exchange Memory - Up to 448GB Streaming Memory, 3.6GB In-Processor-Memory
- IPU-Fabric for compiled in network comprised of
- IPU-LinkTM - 512Gbps for intra IPU-POD64 communication
- GW-Link - 2x 100Gbps Gateway-Links for inter IPU-POD64 communication
- Sync-Link - dedicated hardware signalling for BSP, low jitter on IPU to IPU synchronisation
- Host-Link - PCIe Gen4 RoCEv2 NIC/SmartNIC Interface for IPU-M2000 to server communication

# Considerations
## Storage locations
* The `/local` drive should have about 7TB of storage for placing datasets.
* `$HOME` (25GB quota) and $PROJECT (project quota) should be accessible.

## Graphcore hardware configuration
Regular POD size configurations are on the lines of 16, 54, or 256. Our system is a subset of a base POD-16 
configuration as a POD-4, and thus most of the examples and models have to be adapted for running on this 
smaller-than-usual hardware configuration.

# Ways to run Graphcore jobs
Jobs can be executed directly from containers created by Graphcore, but then they also provide wheel files (.whl)
for installing on Python environments. It's up to you as researcher to choose what way is the one that fits best your 
development environment and is algo performant.

Independently of the path you take, it is first necessary for you to make sure the necessary environment
variables for running jobs on the Graphcore equipment are set in your environment. More details in the next section.

# Set up the base environment variables for running Graphcore jobs
The following environment variables should be in your session, and the scripts sourced, for getting to run jobs on the
Graphcore hardware:

```shell
export GRAPHCORE_SDK_PATH=/opt/graphcore/poplar/poplar_sdk-centos_7_6-2.6.0+1074-33d3efd05d
source ${GRAPHCORE_SDK_PATH}/poplar-centos_7_6-2.6.0+4610-a313c81b39/enable.sh
source ${GRAPHCORE_SDK_PATH}/popart-centos_7_6-2.6.0+4610-a313c81b39/enable.sh
```

The system has already been configured for setting the variables in this specific order as soon as your session in the
system starts, but if that is not happening, please feel free to add them to your `$HOME/.bashrc` file.

You can verify the (Poplar) SDK env has been successfully set up by running the `popc --version` command:

```shell
popc --version
    POPLAR version 2.6.0 (e0ab3b4f12)
    clang version 15.0.0 (765f0d82adc5d9261ca9d1f86d11b594a33bd784)
```

## Run jobs using the Graphcore container

The current Graphcore containers available in the system can be found under the `/opt/graphcore/containers/` directory.
For getting new containers, it's necessary to pull them for [TensorFlow](https://hub.docker.com/r/graphcore/tensorflow) 
or [PyTorch](https://hub.docker.com/r/graphcore/pytorch) from their respective DockerHub pages, and also convert them 
to Singularity/AppTainer format first. 

The "latest" tag will allow to pull the latest container, although the versioning of the file name is manual.

For example:
```shell
SINGULARITY_CACHEDIR=$(pwd) singularity pull --name=pytorch_22.10_3.0.0.sif docker://graphcore/pytorch:latest
```

Then, it's necessary to bind a specific Graphcore folder (`--bind /etc/ipuof.conf.d`), which contains the system configuration for Singularity/AppTainer to know
how to communicate with the IPU:


#### MNIST Job Using Container Example

Example output for an MNIST running on a Singularity container. Following the code from [Graphcore SDK 2.6 example](https://github.com/graphcore/tutorials/tree/sdk-release-2.6/tutorials/tensorflow2/keras): 

__Note:__ Please make sure the output says "running on IPU". For example: "Keras MNIST example, running on IPU".
```shell
time singularity exec --bind /etc/ipuof.conf.d /opt/graphcore/containers/tensorflow_22.10_3.0.0.sif python3 mnist.py
    2023-02-01 16:08:38.500699: I tensorflow/compiler/plugin/poplar/driver/poplar_platform.cc:43] Poplar version: 3.0.0 (fa83d31c56) Poplar package: 1e179b3b85
    Keras MNIST example, running on IPU
    Model: "model"
    _________________________________________________________________
    Layer (type)                 Output Shape              Param #   
    =================================================================
    input_1 (InputLayer)         [(None, 28, 28, 1)]       0         
    _________________________________________________________________
    conv2d (Conv2D)              (None, 26, 26, 32)        320       
    _________________________________________________________________
    max_pooling2d (MaxPooling2D) (None, 13, 13, 32)        0         
    _________________________________________________________________
    conv2d_1 (Conv2D)            (None, 11, 11, 64)        18496     
    _________________________________________________________________
    max_pooling2d_1 (MaxPooling2 (None, 5, 5, 64)          0         
    _________________________________________________________________
    flatten (Flatten)            (None, 1600)              0         
    _________________________________________________________________
    dropout (Dropout)            (None, 1600)              0         
    _________________________________________________________________
    dense (Dense)                (None, 10)                16010     
    =================================================================
    Total params: 34,826
    Trainable params: 34,826
    Non-trainable params: 0
    _________________________________________________________________
    
    Training
    2023-02-01 16:08:41.281171: I tensorflow/compiler/mlir/mlir_graph_optimization_pass.cc:185] None of the MLIR Optimization Passes are enabled (registered 2)
    Epoch 1/3
    2023-02-01 16:08:41.579597: W tensorflow/compiler/tf2xla/kernels/random_ops.cc:54] Warning: Using tf.random.uniform with XLA compilation will ignore seeds; consider using tf.random.stateless_uniform instead if reproducible behavior is desired. while/model/dropout/dropout/random_uniform/RandomUniform
    2023-02-01 16:08:41.610702: I tensorflow/compiler/mlir/tensorflow/utils/dump_mlir_util.cc:210] disabling MLIR crash reproducer, set env var `MLIR_CRASH_REPRODUCER_DIRECTORY` to enable.
    Compiling module a_inference_train_function_806__XlaMustCompile_true_config_proto___n_007_n_0...02_001_000__executor_type____.626:
    [##################################################] 100% Compilation Finished [Elapsed: 00:00:28.8]
    2023-02-01 16:09:10.645083: I tensorflow/compiler/jit/xla_compilation_cache.cc:376] Compiled cluster using XLA!  This line is logged at most once for the lifetime of the process.
    2023-02-01 16:09:11.010259: I tensorflow/compiler/plugin/poplar/driver/poplar_executor.cc:1619] TensorFlow device /device:IPU:0 attached to 1 IPU with Poplar device ID: 0
    937/937 [==============================] - 32s 2ms/step - loss: 0.9223 - accuracy: 0.7053
    Epoch 2/3
    937/937 [==============================] - 1s 1ms/step - loss: 0.2949 - accuracy: 0.9122
    Epoch 3/3
    937/937 [==============================] - 1s 1ms/step - loss: 0.2191 - accuracy: 0.9345
    
    Evaluation
    WARNING:tensorflow:x is of type `np.ndarray`. This will be cast to `tf.Tensor` during every call to: `fit()`, `predict()` and `evaluate()`. If you plan to call any of these functions multiple times in your program, it is recommended to pre-emptively cast to `tf.Tensor` to avoid the repeated computation.
    WARNING:tensorflow:y is of type `np.ndarray`. This will be cast to `tf.Tensor` during every call to: `fit()` and `evaluate()`. If you plan to call any of these functions multiple times in your program, it is recommended to pre-emptively cast to `tf.Tensor` to avoid the repeated computation.
    Compiling module a_inference_test_function_37603__XlaMustCompile_true_config_proto___n_007_n_0...02_001_000__executor_type____.293:
    [##################################################] 100% Compilation Finished [Elapsed: 00:00:12.5]
    156/156 [==============================] - 14s 1ms/step - loss: 0.1297 - accuracy: 0.9640
    Program ran successfully
    
    real	0m53.130s
    user	6m57.052s
    sys	0m49.462s
```
## Configuring Python environments

### PyTorch

Create a directory for storing your Pythong virtual environments
```shell
ENVIRONMENTS_PATH=$HOME/development
mkdir ${ENVIRONMENTS_PATH}
cd ${ENVIRONMENTS_PATH}
```

Define a name for your environment
```shell
ENVIRONMENT_NAME=poptorch
virtualenv -p python3 ${ENVIRONMENTS_PATH}/${ENVIRONMENT_NAME}
```

Activate your environment
```shell
source ${ENVIRONMENTS_PATH}/${ENVIRONMENT_NAME}/bin/activate
```

First update your package manager
```shell
python3 -m pip install -U pip
```

Then install the custom Graphcore PyTorch package via wheel file they provide (poptorch) 
```shell
python3 -m pip install ${GRAPHCORE_SDK_PATH}/poptorch*.whl
```

Finally, add additional libraries as needed.
```shell
python3 -m pip install numpy
```

Additionally, check the PyTorch installation worked by importing the library:
```shell
python3 -c "import poptorch; print(poptorch.__version__)"
    2.6.0-5cd2f910b9
```

### TensorFlow

Create a directory for storing your Pythong virtual environments
```shell
ENVIRONMENTS_PATH=$HOME/development
mkdir ${ENVIRONMENTS_PATH}
cd ${ENVIRONMENTS_PATH}
```

Define a name for your environment
```shell
ENVIRONMENT_NAME=tensorflow
virtualenv -p python3 ${ENVIRONMENTS_PATH}/${ENVIRONMENT_NAME}
```

Activate your environment
```shell
source ${ENVIRONMENTS_PATH}/${ENVIRONMENT_NAME}/bin/activate
```

First update your package manager and a dependency
```shell
python3 -m pip install -U pip
python3 -m pip install "protobuf>=3.9.2,<3.20" --force-reinstall
```

Then install the custom Graphcore PyTorch package via wheel file they provide (poptorch)

If using TensorFlow 2:
```shell
python3 -m pip install ${GRAPHCORE_SDK_PATH}/tensorflow*-2*amd*.whl
python3 -m pip install --force-reinstall --no-deps ${GRAPHCORE_SDK_PATH}/keras*.whl
python3 -m pip install ${GRAPHCORE_SDK_PATH}/ipu_tensorflow_addons*-2*.whl
```
If using TensorFlow 1:
```shell
python3 -m pip install ${GRAPHCORE_SDK_PATH}/tensorflow*-1*amd*.whl
python3 -m pip install --force-reinstall --no-deps ${GRAPHCORE_SDK_PATH}/keras*.whl
python3 -m pip install ${GRAPHCORE_SDK_PATH}/ipu_tensorflow_addons*-1*.whl
```

Finally, add additional libraries as needed.
```shell
python3 -m pip install numpy
```

Additionally, check the PyTorch installation worked by importing the library:
```shell
python3 -c "from tensorflow.python import ipu"
    2022-09-15 11:39:02.054462: I tensorflow/compiler/plugin/poplar/driver/poplar_platform.cc:43]
    Poplar version: 2.6.0 (e0ab3b4f12) Poplar package: a313c81b39

python3 -c "import keras"
    2022-09-15 11:39:06.863003: I tensorflow/compiler/plugin/poplar/driver/poplar_platform.cc:43]
    Poplar version: 2.6.0 (e0ab3b4f12) Poplar package: a313c81b39
```

#### MNIST Job on Python environment Example
Example output for an MNIST running on a Python virtual environment. Following the code from [Graphcore SDK 2.6 example](https://github.com/graphcore/tutorials/tree/sdk-release-2.6/tutorials/tensorflow2/keras):

__Note:__ Please make sure the output says "running on IPU". For example: "Keras MNIST example, running on IPU".
```shell
ENVIRONMENTS_PATH=$HOME/development
ENVIRONMENT_NAME=tensorflow
source ${ENVIRONMENTS_PATH}/${ENVIRONMENT_NAME}/bin/activate

time python3 mnist_1_tpu.py (https://github.com/graphcore/tutorials/blob/sdk-release-2.6/tutorials/tensorflow2/keras/completed_demos/completed_demo_ipu.py)
    Running mnist_1_tpu.py…
    
    2022-09-20 14:18:15.297403: I tensorflow/compiler/plugin/poplar/driver/poplar_platform.cc:43] Poplar version: 2.6.0 (e0ab3b4f12) Poplar package: a313c81b39
    Keras MNIST example, running on IPU
    Model: "model"
    _________________________________________________________________
    Layer (type)             	Output Shape          	Param #   
    =================================================================
    input_1 (InputLayer)     	[(None, 28, 28, 1)]   	0    	 
    _________________________________________________________________
    conv2d (Conv2D)          	(None, 26, 26, 32)    	320  	 
    _________________________________________________________________
    max_pooling2d (MaxPooling2D) (None, 13, 13, 32)    	0    	 
    _________________________________________________________________
    conv2d_1 (Conv2D)        	(None, 11, 11, 64)    	18496	 
    _________________________________________________________________
    max_pooling2d_1 (MaxPooling2 (None, 5, 5, 64)      	0    	 
    _________________________________________________________________
    flatten (Flatten)        	(None, 1600)          	0    	 
    _________________________________________________________________
    dropout (Dropout)        	(None, 1600)          	0    	 
    _________________________________________________________________
    dense (Dense)            	(None, 10)            	16010	 
    =================================================================
    Total params: 34,826
    Trainable params: 34,826
    Non-trainable params: 0
    _________________________________________________________________
    
    Training
    2022-09-20 14:18:16.750430: I tensorflow/compiler/mlir/mlir_graph_optimization_pass.cc:185] None of the MLIR Optimization Passes are enabled (registered 2)
    Epoch 1/3
    2022-09-20 14:18:16.988291: W tensorflow/compiler/tf2xla/kernels/random_ops.cc:54] Warning: Using tf.random.uniform with XLA compilation will ignore seeds; consider using tf.random.stateless_uniform instead if reproducible behavior is desired. while/model/dropout/dropout/random_uniform/RandomUniform
    2022-09-20 14:18:16.994788: I tensorflow/compiler/mlir/tensorflow/utils/dump_mlir_util.cc:210] disabling MLIR crash reproducer, set env var `MLIR_CRASH_REPRODUCER_DIRECTORY` to enable.
    Compiling module a_inference_train_function_740__XlaMustCompile_true_config_proto___n_007_n_0...02_001_000__executor_type____.558:
    [##################################################] 100% Compilation Finished [Elapsed: 00:00:30.1]
    2022-09-20 14:18:47.386947: I tensorflow/compiler/jit/xla_compilation_cache.cc:376] Compiled cluster using XLA!  This line is logged at most once for the lifetime of the process.
    2022-09-20 14:18:47.704567: I tensorflow/compiler/plugin/poplar/driver/poplar_executor.cc:1618] TensorFlow device /device:IPU:0 attached to 1 IPU with Poplar device ID: 0
    937/937 [==============================] - 34s 2ms/step - loss: 1.0556 - accuracy: 0.6609
    Epoch 2/3
    937/937 [==============================] - 2s 2ms/step - loss: 0.3034 - accuracy: 0.9072
    Epoch 3/3
    937/937 [==============================] - 2s 2ms/step - loss: 0.2186 - accuracy: 0.9340
    
    Evaluation
    WARNING:tensorflow:x is of type `np.ndarray`. This will be cast to `tf.Tensor` during every call to: `fit()`, `predict()` and `evaluate()`. If you plan to call any of these functions multiple times in your program, it is recommended to pre-emptively cast to `tf.Tensor` to avoid the repeated computation.
    WARNING:tensorflow:y is of type `np.ndarray`. This will be cast to `tf.Tensor` during every call to: `fit()` and `evaluate()`. If you plan to call any of these functions multiple times in your program, it is recommended to pre-emptively cast to `tf.Tensor` to avoid the repeated computation.
    Compiling module a_inference_test_function_37537__XlaMustCompile_true_config_proto___n_007_n_0...02_001_000__executor_type____.293:
    [##################################################] 100% Compilation Finished [Elapsed: 00:00:14.4]
    156/156 [==============================] - 16s 2ms/step - loss: 0.1333 - accuracy: 0.9625
    Program ran successfully
    
    real    0m55.160s
    user    7m32.789s
    sys    0m53.482s
```