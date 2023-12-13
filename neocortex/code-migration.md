# Code Migration to the Cerebras Architecture
In this page:
* [Tensorflow](#tensorflow)
   * [Structure of the code](#TF-code-structure)
      * [model.py](#TF-model.py)
      * [data.py](#TF-data.py)
      * [utils.py](#TF-utils.py)
      * [configs/](#TF-configs)
      * [run.py](#TF-run.py)
* [PyTorch](#pytorch)
   * [Structure of the code](#PT-code-structure)
      * [model.py](#PT-model.py)
      * [data.py](#PT-data.py)
      * [utils.py](#PT-utils.py)
      * [configs/](#PT-configs)
      * [run.py](#PT-run.py)
     

As described in the Cerebras documentation [How Cerebras Works](https://docs.cerebras.net/en/1.6.0/cerebras-basics/how-cerebras-works.html), the first step to using the Cerebras machines is to port your code from regular TensorFlow or PyTorch to use the Cerebras libraries instead.

![flowchart showing steps to run on a CS system](https://github.com/pscedu/psc-wpdocs/blob/dev/neocortex/how_cerebras_works_workflow.jpg)

The steps to port your code are laid out in the the [Workflow for TensorFlow on CS](https://docs.cerebras.net/en/1.6.0/tensorflow-docs/cs-tf-workflow.html) documentation. This documentation also goes over the process needed for performing such code migration. This is a good starting point for understanding the process. 
1. Port to Cerebras
2. Prepare input
3. Compile on CPU
4. Run on Cerebras

<blockquote class="note">
  <strong>Note</strong>
 <p>An estimation of the required effort to get the code ready is one to four weeks for a single person performing all of the edits.</p>
<p>For track 1 users: For step 1, "Port to Cerebras,” you will only need to follow the sections related to input function or dataloader. You will not need to modify the model or the overall code structure.</p>
</blockquote>
