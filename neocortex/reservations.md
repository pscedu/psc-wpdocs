## Running Jobs via Reservations

### What is a reservation?
As you are already familiar with the interactive and batch mode for running jobs on Neocortex, there is one more way to do so. That is via reservations. The reservation created against your grant id will make sure that the resource is explicitly reserved for you during the specified time duration. You can use the reserved resource for your jobs without any waiting around for the approved time window.

### When do I need a reservation?
You can use reservations when you want to actively debug the code and do not want to wait for the resource everytime you start an interactive or a batch job. The wait time depends on the number and the duration of jobs in the queue. You can use the following command to check the pending jobs in the queue:
```
$ squeue
```

### How is a reservation different from normal interactive or batch mode jobs?
Interactive mode jobs can fail if there is a network connection issue. They also have a wall-time restriction (default is 4 hours, maximum is 48 hours). If your interactive session is ending because the time is up, you can’t extend it and hence you would have to start another interactive session. There could be other pending jobs in the queue delaying your job execution, whereas a reservation guarantees that the resource is explicitly assigned for your usage regardless of the job queue.

### How can I create a reservation?
For creating a reservation, please send an email to neocortex@psc.edu with the following details:
* Username
* Grant ID
* Desired start and end date and time.
* Number of SDF nodes (SDF-1, SDF-2), and SDF node preferred, if any.
* Explain why you need a reservation (why the job can’t be executed with batch or interactive mode).
  
You can use the `scontrol` command to check booked slots to ensure that you are not asking for an already reserved time slot:
```
$ scontrol show reservations
```

Please note that asking for a reservation does not guarantee you the reserved time slot. We will try our best to grant it.

### How do I run a job with a reservation?
Once your reservation is confirmed, you will receive a confirmation with details including the *reservation-name* (we will provide you *reservation-name* upon confirmation). In order to start a job, use the parameter `--reservation` with `srun` or `sbatch`.
<pre></pre>
$ sbatch --reservation <em>reservation-name</em> --account <em>SLURM-charge-id</em> mnist.batch
</pre>

If your reservation is not assigned to your default SLURM charge id, then you will need to use the `--account` option that is your SLURM charge id.

Please note that using the `--reservation` flag does not automatically override the default interact or batch parameters, such as job run time. Please, explicitly specify the wall-time, SDF node (if one is preferred), etc, to override the defaults.

### Comparison table for various modes in which jobs can be run
Please note that this comparison is between jobs asking for the same number and type of resources, duration, etc.
<table>
  <thead>
    <tr>
      <th><th>Interactive</th><th>Batch</th><th>Reservation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Network connectivity</td>
      <td>The interactive jobs are not robust to network connections issues. The resources will be relinquished and the job will be interrupted if there are any network connectivity issues. If the queue is busy, there could be a significant wait time to get the resources to resume debugging/training in case the resources got acquired by other jobs meanwhile.</td>
      <td>The batch jobs are robust to network connections issues. The jobs will maintain their job status even if there are network jitters.</td>
      <td>Even if the network jitters, you can quickly acquire the resources without any wait time via interactive or batch job and resume your work.</td>
    </tr>
    <tr>
      <td>Time limit</td>
      <td>Default: 4 hours<br />Maximum: 48 hours</td>
      <td>Default: 4 hours<br />
Maximum: 48 hours</td>
      <td>As per your request and confirmation by PSC.</td>
    </tr>
    <tr>
     <td>Priority</td>
      <td>Interact jobs have more priority than batch jobs.</td>
      <td>Batch jobs have low priority as compared to interactive jobs.</td>
      <td>The resource is reserved for your usage explicitly.</td>
    </tr>
    <tr>
      <td>How to?</td>
      <td>Issue an interact command such as:<br />
<code>$ srun --pty bash -i</code><br />
Wait times depend on how busy the queue is.</td>
      <td>Create a batch (or job) script which contains the commands to be run, then submit the job to be run. It will be in the queue until the resources become available.</td>
      <td>Send an email to neocortex@psc.edu with details (see <strong>How can I create a reservation</strong> above). Both interactive and batch jobs can be run under reservation mode (for the confirmed duration).</td>
    </tr>
  </tbody>
</table>
