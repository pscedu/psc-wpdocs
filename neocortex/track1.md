# Track 1 (ML): Cerebras Model Zoo ML models 
Corresponds to models already present in version R1.6.0 of the Cerebras modelzoo ML models software.

**Good Match Criteria:** You would be a good match for this track if your research already uses, or could potentially
use, any of the following models, supported via TensorFlow and PyTorch:

<table>
  <thead>
    <tr>
      <th>Model</th><th>Layer Pipeline mode</th><th>Weight Streaming mode</th>
    </tr>
  </thead>
<tbody>
<tr>
  <td>BERT</td>
  <td>     

<a href="https://github.com/cerebras/modelzoo/blob/R_1.6.0/modelzoo/transformers/tf/bert" target="_blank">TensorFlow code</a>  <br />
[PyTorch code](https://github.com/cerebras/modelzoo/blob/R_1.6.0/modelzoo/transformers/pytorch/bert)
</td>
  <td>-</td>
</tr>
<tr>
<td>BERT (fine-tuning) Classifier</td>
<td>
  
[TensorFlow code](https://github.com/cerebras/modelzoo/blob/R_1.6.0/modelzoo/transformers/tf/bert/fine_tuning/classifier)  <br />
[PyTorch code](https://github.com/cerebras/modelzoo/blob/R_1.6.0/modelzoo/transformers/pytorch/bert/fine_tuning/classifier)</td>
<td>-</td>  
</tr>

<tr>
  <td>BERT (fine-tuning) Named Entity Recognition</td><td>
    
[TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/bert/fine_tuning/token_classifier)  <br />
[PyTorch code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/pytorch/bert/fine_tuning/token_classifier)</td>
<td>-</td>
</tr>

<tr>
  <td>BERT (fine-tuning) Summarization</td>
<td> 
  
  [TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/bert/fine_tuning/extractive_summarization)  <br />
  [PyTorch code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/pytorch/bert/fine_tuning/extractive_summarization)</td>
<td>-</td>
</tr>

<tr>
<td>BERT (fine-tuning) Question Answering</td>
<td> 
  
[TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/bert/fine_tuning/qa) <br />
[PyTorch code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/pytorch/bert/fine_tuning/qa)</td>
<td>-</td>
</tr>

<tr>
  <td>GPT-2</td>
<td>
  
[TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/gpt2) <br />
[PyTorch code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/pytorch/gpt2)</td>

<td>
  
  [TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/gpt2)</td>
</tr>

<tr> 
  <td>GPT-3</td>
<td>-</td>
<td>
  
  [TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/gpt3)</td>
</tr>

<tr> 
  <td>GPT-J</td>
<td>-</td>
<td>
  
  [TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/gptj)</td>
</tr>

<tr>
<td>Linformer</td>
<td>
  
  [TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/linformer)</td>
<td>-</td>
</tr>

<tr>
<td>RoBERTa</td>
<td>
  
[TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/bert)<br />
[PyTorch code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/pytorch/bert)</td>
<td>-</td>
</tr>

<tr>
<td>T5</td>
<td>

[TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/t5)<br />
[PyTorch code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/pytorch/t5)</td>
<td>-</td>
</tr>

<tr><td>Transformer</td>
<td>
  
[TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/tf/transformer)<br />
[PyTorch code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/transformers/pytorch/transformer)</td>
<td>-</td>
</tr>

<tr>
  <td>MNIST (fully connected)</td>
<td>
  
[TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/fc_mnist/tf)<br />
[PyTorch code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/fc_mnist/pytorch)</td>
<td>-</td>
</tr>

<tr>
  <td>2D UNet (experimental)</td>
<td>
  
[TensorFlow code](https://github.com/cerebras/zoo/blob/R_1.6.0/zoo/unet/tf)</td>
<td>-</td>
</tbody>
</table>

<br />
<p>Based on the [Cerebras Model Zoo R1.6.0 GitHub page](https://github.com/cerebras/modelzoo/tree/R_1.6.0#models-in-this-repository).</p>

# Track Specific Questions

If your project falls under this category, make sure to address the following questions in your application document:

1. Please, indicate which model(s) from the modelzoo you intend to use. Do you anticipate being interested in adjusting
   the model architecture?
2. Please, describe the dataset you are intending to use.
3. How big is the dataset of interest (total dataset size, number of samples, and sample size in MB)?
4. Please, elaborate on the readiness of the dataset of interest. Is it fully available at this time? If not, how soon
   would it be fully available?
5. Please specify the shapes of the input and output tensors for your model/s.
6. If possible, please specify the name of the dimensions for your input and output tensors from the previous question.
   E.g. (batch, input channels, height, width)
7. Please specify the loss function that you would like to use.
8. Please, list the libraries complementary to standard PyTorch and/or TensorFlow distributions that you would need to
   train your model(s).
9. Please, list the key libraries that you would need for data preprocessing.