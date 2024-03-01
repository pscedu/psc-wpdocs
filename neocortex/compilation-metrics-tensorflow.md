
  
### TensorFlow
Change dirs into the modelzoo directory containing the MNIST TF code. The contents of this folder should be “`configs/`, `data.py`, `model.py`, `README.md`, `run.py`, and  `utils.py`”.

<pre>
cd $PROJECT/modelzoo/fc_mnist/tf
</pre>

Run compile in lightweight validation mode.

<pre>
python run.py --mode train --validate_only --params configs/params.yaml
</pre>


Run compile in full compilation mode.
<pre>
python run.py --mode train --compile_only --params configs/params.yaml
</pre>


The ratio of utilized components is the theoretical peak throughput. Grep the “total_utilization” from the JSON file:
<pre>
grep -o '"total_utilization":[0-9\.]*' model_dir/*/plan.json
</pre>


When the pipeline is fully loaded, it will process the model using this many cycles.

<pre>
grep -o '"estimated_deltat":[0-9\.]*' model_dir/*/deltat_estimate.json
    # "estimated_deltat": **674**,
</pre>

