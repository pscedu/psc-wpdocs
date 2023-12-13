
**data.py**

Input data pipeline implementation: the input pipeline must be very fast, you must ensure you preprocess the input data by sharding, shuffling, prefetching, interleaving, repeating, batching, etc., in proper order.

The input function builds the input pipeline and yields the batched data in the form of (features, labels) pairs, where:
* `features` can be a tensor or dictionary of tensors, and
* `labels` can be a tensor, a dictionary of tensors, or None.

``` def input_fn(params: dict, mode=tf.estimator.ModeKeys.TRAIN) -> tf.data.Dataset: ```

This method should have the input function and run any preprocessing that might be needed before returning a TF dataset object.

**Input**
* params (`dict`):
```
{
    train_input: dict = {
        data_dir: str,
        num_parallel_calls: int = 0,
        train_batch_size: int, eval_batch_size: int, batch_size: int,
        shuffle: bool
    }
}
```

* `mode` (str): One of the `tf.estimator.ModeKeys.*` strings. The available options are: `TRAIN`, `EVAL`, `TRAIN_AND_EVAL`, and `INFERENCE`.
  
**Output**
* tf.data.Dataset`: "features" and "labels" will be inside this Dataset object as the handles to the batched data that the model will use.


