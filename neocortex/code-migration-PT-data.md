

**data.py**

Input data pipeline implementation: the input pipeline must be very fast, you must ensure you preprocess the input data by sharding, shuffling, prefetching, interleaving, repeating, batching, etc., in proper order.

```
def get_train_dataloader(params):
    ...
    loader = torch.utils.data.DataLoader(...)
    return loader

def get_eval_dataloader(params):
    ...
    loader = torch.utils.data.DataLoader(...)
    return loader
```
These methods should have the input function and run any preprocessing that might be needed before returning a dataloader object.

**Inputs**
* params: (dict)

  ```
  {
      train_input: dict = {
          data_dir: str,
          num_examples: int,
          train_batch_size: int, eval_batch_size: int, batch_size: int,
          shuffle: bool
      }
      eval_input: dict = {
          …
      }
  }
  ```
  
**Output**

* `torch.utils.data.DataLoader`: This object takes a torch.utils.data.TensorDataset object with the images and labels to use, as well as the batch_size, shuffle, and any other arguments defined in the params file.

