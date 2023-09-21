

#### PyTorch
Navigate into the modelzoo directory to where the MNIST PyTorch codes are located.
```
cd $PROJECT/modelzoo/fc_mnist/pytorch
python-pt run.py --mode train --validate_only --params configs/params.yaml
```

To run full compilation mode:

For the latest version:
```
python-pt run.py --mode train --compile_only --params configs/params.yaml
```
The ratio of utilized components is the theoretical peak throughput. Grep the “total_utilization” from the JSON file:

For the latest version:
```
grep -o '"total_utilization":[0-9\.]*' model_dir/*/plan.json
```

Example: This means 5.8% of the WSE2 chip is going to be used for running this code:
```
"total_utilization": 0.0588006479419213
```

When the pipeline is fully loaded, it will process the model using the number of cycles as the value returned by `estimated_deltat`.

For the latest version:
```
grep -o '"estimated_deltat":[0-9\.]*' model_dir/*/deltat_estimate.json
```

Example: This means a total of 674 cycles are estimated to be required for the WSE2 to process one sample:
```
"estimated_deltat": 674
```

