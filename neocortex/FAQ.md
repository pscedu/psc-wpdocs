# FAQ (general)


<ol>
 <li><strong>Is it possible to use Keras functions inside the TF estimator?</strong><br />
Yes, we support the TensorFlow Keras Layers API in our model function.
<br /> <br />
 </li>

<li><strong>What are the resource limits for Neocortex? I can see that under this project, I only have 5TB storage and 1000 large regular mem CPU hours.</strong><br />
   Please refer to the <a href="https://www.psc.edu/resources/neocortex/docs/allocations">Allocations section of this document</a> for more information.
<br /><br />
</li>

<li><strong>I see ogbg_molchembl. How do we get the other ogb datasets to the machine? Should we store them in our allocations or will you put the entire ogb collection in that location?</strong><br />
   On the Ocean shared-filesystem, the path of some Model Zoo datasets is `/ocean/neocortex/cerebras/data/`.<br /><br />
 On the SDF, these datasets are available under $LOCAL at `$LOCAL/cerebras/data/`.
<br /><br />
</li>

<li><strong>Are RSA keys supported?</strong><br />
   RSA keys are supported. You can manage your key pairs and propogate them to PSC machines. See https://www.psc.edu/types-of-ssh-authentication/ for more information.
<br /><br />
   </li>

<li><strong>Is `newgrp` to be run on the login node or the interact node? or either?</strong><br />
   If you have multiple projects on PSC systems, you need to use the <code>newgrp</code> command to switch between them and assign your usage to your Neocortex project. You will not be able to get an interact session until you are on the right project. It's best to run it on the login node, but you can also run it on the interact node if you specify the group allocation account id (<code>--account</code>) to use in the interact command.
<br /><br />
</li>

<li><strong>Do we have an option to run using a batch job?</strong><br />
   Yes, you can also run a batch job. Please review the <a href="https://www.psc.edu/resources/neocortex/docs/running-jobs">Running jobs section</a> section in this documentation for more details.
<br /><br />
</li>

<li><strong>What does it mean when the interact command hangs?</strong><br />
This could mean that the queue is busy and not ready for any more jobs at that time. Another possibility is that you are not in the right project allocation. If you have multiple projects on PCS systems, please use `newgrp` to switch to the appropiate Neocortex project. Please contact us if you come across any specific error or it hangs for a long time.
<br /><br />
</li>

<li><strong>Which queue should I use to submit batch jobs?</strong><br />
Feel free to use the Neocortex (preferred) default queue, as well Bridges-2 RM, EM, and GPU partitions as needed. Refer to the <a href="https://www.psc.edu/resources/neocortex/docs/allocations">Allocations section</a> of this documentation for more details.
<br /><br />
</li>

<li><strong>Do you expect us to compile our model?</strong><br />
   You are expected to compile and run your model.
   <br /><br />
</li>

<li><strong>Is it mandatory to use Keras API for defining the Network Architecture? Can we use Keras model APIs? Or it should be only Keras layers?</strong><br />
 Currently, we support Tensorflow Keras Layers API in our model function.
 <br /><br />
</li>

<li><strong>How can I verify correctness before access to Neocortex?</strong><br />
    <code>compile()</code> will perform full compilation through all stages of the Cerebras Software Stack and generate a CS-2 executable. If this is successful, your model is guaranteed to run on CS-2. Refer to ‘_Cerebras-ML-User-Guide’ _ for more information.
    <br /><br />
</li>

<li><strong>Does CS1 support profiling with TensorBoard?</strong><br />
    Yes, you can save summaries via Tensorboard. Please check the ‘Cerebras-ML-User-Guide’ for detailed steps.
<br /><br />
</li>
<li><strong>Is 3D convolution supported (for 3D U-Net)?</strong><br />
    Not as of now. We will update once they are ready to run.
<br /><br />
</li>
<li><strong>Are the limitations on SRAM allocation to compute nodes? For example, if we use only 5.8% of the nodes (WSE2), can we still use all 18 GB of SRAM for a larger mini-batch?</strong><br />
    No, the CS-2 can only run one job at a time. Parallel runs are not supported as of now.
<br /><br />
</li>
<li><strong>Can we create parallel implementations to increase core usage? E.g., If we use 5.8% of nodes (WSE2), can we create 20 copies of our network for a 20x speedup?</strong>s<br />
    Parallel runs are not supported as of now.
<br /><br />
</li>
<li><strong>Along the same lines, what about tanh activation, in place of ReLU?</strong><br />
    Tanh activation function is supported.
<br /><br />
</li>
<li><strong>Is there a list of supported operations?</strong><br />
    Yes, please refer to the <a href="https://www.psc.edu/resources/neocortex/docs/developing-for-cs">Developing for the CS-2</a> section in this document.
</li>
</ol>

