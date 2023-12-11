

### Additional notes for generating the key metrics

You need to be located in the same directory in which you compile **your code** with `python run.py [...]`, as shown in the “Reference Compilation for the CS-2 Commands section”. For the example instructions, that would be `$PROJECT/modelzoo/fc_mnist/tf`.
1. Get the **ratio of utilized components**, that is, the theoretical peak usage from the total WSE2 in the CS-2.
2. Get the **total number of cycles per sample**: when the pipeline is fully loaded, it will process the model using this many cycles.
3. **Size of the input dataset**: how big is the total set of files for the dataset?
   * For example: 1GB composed of 1024 individual files
4. **Size of each dataset sample to be processed**: what is the size of each of the dataset samples that integrate the complete dataset.
   * For the same example, if there are 1024 individual files for a total of 1GB, the sample size is 1MB. (1024MB / 1024 files) = 1MB.
5. What is the **maximum expected batch size to use**?
   * This value depends on the dataset you are using.

  <div class="note">
    <blockquote>
      <strong>Note</strong>
      <p>Please, use the <a href="https://portal.neocortex.psc.edu/home">Neocortex Project Key Metrics file</a> and submit it to the <a href="https://cmu.app.box.com/f/2e34313bdacf410a96b28e34b7d8f4b3">Key-compilation-metrics Submit Box link</a></p>
    </blockquote>
  </div>
