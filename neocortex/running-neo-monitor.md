

### Monitoring and interpreting results on CS-2s

In order to view performance results, you can refer to the `performance.json` inside the model log directory file. Here is a sample entry from this file:
```
{
     "total_time": 261.82,
     "samples_per_sec": 146665.4,
     "est_samples_per_sec": 1831896.55,
     "total_samples": 38400000,
     "fabric_cores": 266207,
     "fabric_utilization": 0.011419684681469684,
     "delta_t": 464,
     "Frequency": 850000000.0
 }
```

where you are expected to replace the following elements in the left column with those in the right one:
<table>
  <thead>
    <tr>
      <th>Sample</th><th>Customize with</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/local1/cerebras/data,<br />
/local2/cerebras/data,<br />
/local3/cerebras/data,<br />
/local4/cerebras/data</td>
       <td>Your dataset location</td>
    </tr>
   <tr>
     <td>$HOME/modelzoo</td>
     <td>Your storage space where your code resides</td>
   </tr>
    <tr>
      <td>$HOME/modelzoo/fc_mnist/tf/run.py</td>
      <td>The main run file that you use for starting the training</td>
    </tr>
  </tbody>
</table>

