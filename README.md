
TinyModel is a 44M parameter model trained on [TinyStories V2](https://arxiv.org/abs/2305.07759) for mechanistic interpretability.

It can be installed with `pip install tinystoriesmodel`

It has 4 layers, uses ReLU activations, and has no layernorms.

It was trained for 3 epochs on a [preprocessed version of TinyStoriesV2](https://huggingface.co/datasets/noanabeshima/TinyStoriesV2).


```
from tiny_model import TinyModel, tokenizer

lm = TinyModel()

# for inference
tok_ids, attn_mask = tokenizer(['Once upon a time', 'In the forest'])
logprobs = lm(tok_ids)

# or
lm.generate('Once upon a time, Ada was happily walking through a magical forest with')

# To decode tok_ids you can use
tokenizer.decode(tok_ids)
```

# Sparse MLP/SAE
Some sparse MLPs/SAEs are provided along with the model.

For example, `acts = lm['M2N100'](tok_ids)`

To get sparse acts, choose which part of the transformer block you want to look at (currently [sparse MLP](https://www.lesswrong.com/posts/MXabwqMwo3rkGqEW8/sparse-mlp-distillation)/[transcoder](https://www.alignmentforum.org/posts/YmkjnWtZGLbHRbzrP/transcoders-enable-fine-grained-interpretable-circuit) and SAE on attention out are available, under the tags 'M' and 'A' respectively.

Then, add the layer. A sparse MLP at layer 2 would be 'M2'.
Finally, optionally add a particular neuron. For example 'M2N100'.

Tokenization is done as follows:
- the top-10K most frequent tokens using the GPT-NeoX tokenizer are selected and sorted by frequency.
- To tokenize a document, first tokenize with the GPT-NeoX tokenizer. Then replace tokens not in the top 10K tokens with a special \[UNK\] token id. All token ids are then mapped to be between 1 and 10K, roughly sorted from most frequent to least.
- Finally, prepend the document with a [BEGIN] token id.

