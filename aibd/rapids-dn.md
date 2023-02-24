[//]: # (Status: Draft)

# RAPIDS

RAPIDS is a data science framework which bundles a collection of
libraries for executing end-to-end data science pipelines completely
on top of GPUs. It uses optimized NVIDIA CUDA® primitives and
high-bandwidth GPU memory to accelerate data preparation and machine
learning tasks. For example, it can be used for [ETL and preprocessing
of deep learning
workflows](https://towardsdatascience.com/tagged/rapids-ai) .

This content is based on the [original
documentation](https://rapids.ai/start.html).  

## How to use RAPIDS on Bridges-2

### Request a node

Start an [interactive
session](https://www.psc.edu/resources/bridges-2/user-guide-2-2#interactive-sessions)
on Bridges-2 with the [interact
command](https://www.psc.edu/resources/bridges-2/user-guide-2-2#interact). You
can use a regular or a [GPU
node](https://www.psc.edu/resources/bridges-2/user-guide-2-2/#gpu-partitions),
as needed for your code.

```shell
interact --gpu # Start a session in a node with a GPU.
````

### Start Python in a RAPIDS Singularity/Apptainer container

Once your interactive session has begun, execute Python using the
latest RAPIDS Singularity/Apptainer container. You can see all of the
containers that PSC has created on Bridges-2 in the directory
<code>/ocean/containers/ngc/</code>. The file <code>latest.sif</code>
is a link to the most recently updated container.

Use a command like:
```shell
singularity exec --nv /ocean/containers/ngc/rapidsai/latest.sif python3
```

## Examples

This is a step-by-step guide of how to run [the quick-start
examples from the](https://colab.research.google.com/drive/1rY7Ln6rEE1pOlfSHCYOVaqt8OvDO35J0#forceEdit=true&offline=true&sandboxMode=true)

### Descriptive statistics example

Here’s a code snippet which reads in a CSV file and outputs some descriptive statistics:

First, download a sample dataset:
```shell
wget https://people.sc.fsu.edu/~jburkardt/data/csv/hw_25000.csv
# hw_25000.csv, height and weight for 25000 individuals Each record includes 3 values: index, height (inches), weight (pounds). There is also an initial header line.
``` 

Create a file named statistics.py with the following content:
```python
# statistics.py

import cudf

gdf = cudf.read_csv('hw_25000.csv')
for column in gdf.columns:
    print(gdf[column].mean())
```

Run it via the RAPIDS container:
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

The original can be found here: [https://github.com/rapidsai/cudf](https://github.com/rapidsai/cudf).

This example loads a public dataset, from a CSV file on GitHub, into a
GPU memory-resident DataFrame and performs a basic calculation.

All of the CSV parsing and the operations for calculating the tip percentage and average are done on the GPU.

Create a file name cuDF.py with the folowing content:
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

Run it via the RAPIDS container
```shell
singularity exec --nv /ocean/containers/ngc/rapidsai/latest.sif python3 cuDF.py
````

It should return the following output:
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

The original can be found here: [https://github.com/rapidsai/cuml](https://github.com/rapidsai/cuml). 

Create a file named cuML.py which loads a small sample data frame and computes DBSCAN clusters:

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
