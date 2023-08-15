## Track 4 (WFA): WFA, WSE Field-equation API
Field equations, includes ML inference.

**Good Match Criteria:** You would be a good match for this track if your research can integrate and make use of the WFA library.

WFA: API recently used for advancing CFP simulations at unprecedented resolution and speed ([more info](https://www.cmu.edu/psc/aibd/neocortex/2023-02-netl-psc-pioneer-first-ever-computational-fluid-dynamics-simulation-on-cerebras-wse.html)).
* Pioneering work - Beta testing of the WFA library.
* Only a few groups would be welcomed.
* Close collaboration with Dr. Dirk Van Essendelft's team, PSC, and Cerebras.
* Field equations, includes ML inference
### Project Guidelines
#### Problem Requirements
* Must lay out on a Hex grid (3d or many 2d parallel)
* Should involve Spatial Locality
* Should be Data Intense
* Single Precision, &lt;40GB
#### Problem Examples
* Computational Fluid Dynamics (FVM, FDM, FEM, LBM)
* Structural Mechanics
* Geomechanics
* Weather/Climate
* Materials – Ising Model, Density Functional Theory
* CNN/RNN inference
#### Project Requirements
* Build a Python class that imports the WFA and contains a “Library” to solve your scientific problem
* Post on a public GitHub
#### What to expect
* Development at the high-level Python interface that is similar to Numpy
* A container (provided by Cerebras) to compile and generate binaries and run on the WSE
* An unpolished product (Beta level) that will require some hand holding from our team on slack
   * Be prepared for bugs and unpolished documentation
* Exceptional speed if successful, strong scaling that can’t be matched elsewhere
  
Please visit the [official WFA documentation](https://dirk-netl.github.io/WSE_FE/) for more information.

