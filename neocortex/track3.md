# Track 3 (SDK): General purpose SDK
The SDK is general purpose and can be used for a variety of things.

**Good Match Criteria:** You would be a good match for this track if you are willing to write low-level code, similar to writing CUDA, for implementing your research.
## Pioneering algorithm coding from scratch
* Analogous to CUDA for Nvidia GPUs
* ML kernels cannot be integrated with Pytorch and/or TensorFlow
* Requires significant commitment
## Sample topics of interest for HPC applications
* Structured grid based PDE and ODE solvers
* Dense linear algebra
* Sparse linear algebra
* Particle methods with regular communication
* Monte Carlo type problems that can fill the wafer
* Towards development of HPL, HPCG type benchmarks • Custom ML kernels
## Examples included in the SDK
The following examples are available in the Neocortex system at `/ocean/neocortex/cerebras/sdk/tutorials/`.
* Basic tasks and colors
* Fabric DSDs
* Multiple source files
* Multi-PE kernel
* Basic parameters
* Wavelet-triggered Tasks
* Arrays and Pointers
* Sparse Tensors
* Memory DSDs
* Reduction
* Basic Branches • Initializers
* Modules
* Loops
* Kernel Parameterization • Fabric Switches
* GEMV
* FFT

# Track Specific Questions

If your project falls under this category, make sure to address the following questions in your application document:

1. Using the SDK requires programming in CSL, a C-like language designed specifically for the problem of massively
   parallel programming on the CS-2. What is your experience with HPC programming paradigms and languages, such as MPI,
   OpenMP, CUDA, OpenCL, etc.?
2. What are the underlying computational algorithms you’re interested in exploring? What existing software packages or
   libraries use these algorithms?
3. How is this problem bottlenecked on current hardware? Is the problem more bottlenecked by memory bandwidth, or
   communication costs associated with scaling up across distributed compute nodes?
4. What range of problem sizes are you interested in addressing? For example, how much memory does your problem use? How
   does memory usage or program run-time scale with problem size?
5. What portion of your algorithm do you plan to port to the CS-2? Why are you interested in exploring this part of your
   algorithm?
6. The CS-2 offers native half and single precision data types. What precision does your algorithm or use case need?
7. The CS-2 is a network-attached accelerator. At a high level, the CSL programming model is similar to that of CUDA, in
   which data is moved between host CPU nodes and the device (CS-2) on which computational kernels are launched. How
   often will data need to be moved between the wafer and the worker nodes?
8. Describe your general plan to map your problem onto 850,000 cores. In answering this question, it might be helpful to
   recall some details of the CS-2 architecture. The 850,000 cores are laid out in a mesh, with each core connected on
   fabric to its four nearest neighbors on the East, South, North, and West. Memory is distributed among the cores, with
   each core having 48 KB of local memory. 64-bit local memory reads and writes take roughly a cycle, as does sending
   and receiving 32-bit messages between the four neighboring cores.