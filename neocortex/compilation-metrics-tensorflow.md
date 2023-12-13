
  
### TensorFlow
Change dirs into the modelzoo directory containing the MNIST TF code. The contents of this folder should be “`configs/`, `data.py`, `model.py`, `README.md`, `run.py`, and  `utils.py`”.
```
cd $PROJECT/modelzoo/fc_mnist/tf
```

Run compile in lightweight validation mode.
```
python run.py --mode train --validate_only --params configs/params.yaml
```

Run compile in full compilation mode.
```
python run.py --mode train --compile_only --params configs/params.yaml
```

The ratio of utilized components is the theoretical peak throughput. Grep the “total_utilization” from the JSON file:
```
grep -o '"total_utilization":[0-9\.]*' model_dir/*/plan.json
```

When the pipeline is fully loaded, it will process the model using this many cycles.
```
grep -o '"estimated_deltat":[0-9\.]*' model_dir/*/deltat_estimate.json
    # "estimated_deltat": **674**,
```

