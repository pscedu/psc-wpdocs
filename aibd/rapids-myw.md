[//]: # (Status: Draft)

# Introduction

The goal of this document is to provide a short and simple way to let users go through steps required to load the RAPIDS
module on Bridges and start using it.

### What is RAPIDS:

RAPIDS is a data science framework that bundles a collection of libraries for executing end-to-end data science
pipelines completely on top of GPUs, and it uses optimized NVIDIA CUDA® primitives and high-bandwidth GPU memory to
accelerate data preparation and machine learning tasks. For example, it can be used
for [ETL and preprocessing of deep learning workflows](https://towardsdatascience.com/tagged/rapids-ai)
.

This content is based on the [original documentation](https://rapids.ai/start.html), and it is a step-by-step guide of
how to  run [their quick-start examples](https://colab.research.google.com/drive/1rY7Ln6rEE1pOlfSHCYOVaqt8OvDO35J0#forceEdit=true&offline=true&sandboxMode=true)
.

### How to use RAPIDS on Bridges-2

#### Request a node 

Request a regular node or [GPU node](https://www.psc.edu/resources/bridges-2/user-guide-2-2/#gpu-partitions), as needed
for your code, then execute Python using the latest RAPIDS Singularity/Apptainer container.

For example:
```shell
interact --gpu # Start a session in a node with a GPU.
singularity exec --nv /ocean/containers/ngc/rapidsai/latest.sif python3
```

### Descriptive statistics example

Here’s a code snippet where we read in a CSV file and output some descriptive statistics:

First, download a sample dataset:
```shell
wget https://people.sc.fsu.edu/~jburkardt/data/csv/hw_25000.csv
# hw_25000.csv, height and weight for 25000 individuals Each record includes 3 values: index, height (inches), weight (pounds). There is also an initial header line.
``` 

Then, create paste the following code:
```python
# statistics.py

import cudf

gdf = cudf.read_csv('hw_25000.csv')
for column in gdf.columns:
    print(gdf[column].mean())
```

And run it via the RAPIDS container:
```shell
singularity exec --nv /ocean/containers/ngc/rapidsai/latest.sif python3 statistics.py
```

It should return the following output:
```shell
python statistics.py

    12500.5
    67.9931135968
    127.07942116080001
```

### cuDF statistics example

The original can be found here: https://github.com/rapidsai/cudf

Load a public dataset, from a CSV file on GitHub, into a GPU memory-resident DataFrame and perform a basic calculation.

All of the CSV parsing and operations for calculating the tip percentage and average is done on the GPU.

```python
# cuDF.py

import cudf
import io, requests

# Download the CSV file from GitHub.
url = "https://github.com/plotly/datasets/raw/master/tips.csv"
content = requests.get(url).content.decode('utf-8')

# Read the CSV into memory.
tips_df = cudf.read_csv(io.StringIO(content))
tips_df['tip_percentage'] = tips_df['tip'] / tips_df['total_bill'] * 100

# Display the average tip amount by dining party size.
print(tips_df.groupby('size').tip_percentage.mean())
```

Running the code should return the following output:

```shell
python cuDF.py
    size
    1    21.729202
    2    16.571919
    3    15.215685
    4    14.594901
    5    14.149549
    6    15.622920
    Name: tip_percentage, dtype: float64
```

### cuML example

The original can be found here: https://github.com/rapidsai/cuml

Load a small sample data frame and compute DBSCAN clusters:

```python
# cuML.py

import cudf
import cuml

# Create and populate a GPU DataFrame
df_float = cudf.DataFrame()
df_float['0'] = [1.0, 2.0, 5.0]
df_float['1'] = [4.0, 2.0, 1.0]
df_float['2'] = [4.0, 2.0, 1.0]

# Setup and fit clusters
dbscan_float = cuml.DBSCAN(eps=1.0, min_samples=1)
dbscan_float.fit(df_float)

print(dbscan_float.labels_)
```

Running the code should return the following output:

```shell
python cuML.py

    0    0
    1    1
    2    2
    dtype: int32
```