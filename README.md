# bilm-tf
Tensorflow implementation of contextualized word representations from bi-directional language models

## Installing
Install python version 3.5 or later, tensorflow version 1.2 and h5py:

```
pip install tensorflow-gpu==1.2 h5py
python setup.py install
```

Finally, ensure the tests pass in your environment by running:
```
python -m unittest discover tests/
```

## Usage overview
There are two main public classes: `Batcher` and `BidirectionalLanguageModel`.

The `Batcher` is used to convert lists of tokenized sentences to numpy
arrays of character ids for the model to run inference.

`BidirectionalLanguageModel` loads the pre-trained model, creates the
computational graph and returns ops that run inference.

See `usage.py` for a detailed usage example.

## Shape conventions
Each tokenized sentence is a list of `str`, with a batch of sentences
a list of tokenized sentences (`List[List[str]]`).

The `Batcher` packs these into a shape
`(n_sentences, max_sentence_length + 2, 50)` numpy array of character
ids, padding on the right with 0 ids for sentences less then the maximum
length.  The first and last tokens for each sentence are special
begin and end of sentence ids added by the `Batcher`.

The input character id placeholder can be dimensioned `(None, None, 50)`,
with both the batch dimension (axis=0) and time dimension (axis=1) determined
for each batch, up the the maximum batch size specified in the
`BidirectionalLanguageModel` constructor.

After running inference with the batch, the return biLM embeddings are
a numpy array with shape `(n_sentences, 3, max_sentence_length, 1024)`,
after removing the special begin/end tokens.

## Vocabulary file
The `Batcher` takes a vocabulary file as input for efficency.  This is a
text file, with one token per line, separated by newlines (`\n`).
Each token in the vocabulary is cached as the appropriate 50 character id
sequence once.  Since the model is completely character based, tokens not in
the vocabulary file are handled appropriately at run time, with a slight
decrease in run time.  It is recommended to always include the special
`<S>` and `</S>` tokens (case sensitive) in the vocabulary file.

