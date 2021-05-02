# pyconllup

*Easily work with [**CoNLL-U Plus**](https://universaldependencies.org/ext-format.html) files using the familiar syntax of **python**.*

Forked from [pyconll](https://github.com/pyconll/pyconll).

## Overview
`pyconllup` adds support for [CoNLL-U Plus](https://universaldependencies.org/ext-format.html) files, allowing users to specify annotations on a per file basis to be loaded. Each annotation is then stored on each `Token` and accessible as attributes. For instance:

```python
>>> import pyconllup
>>>
>>> data: Conll = pyconllup.load_from_file('./mwe/de/train.conllup')
>>> print(data.conll())
# global.columns = ID FORM UPOS HEAD DEPREL MISC PARSEME:MWE
# source_sent_id = conllu http://hdl.handle.net/11234/1-2837 UD_German-GSD/de_gsd-ud-train.conllu train-s1682
# sent_id = train-s1682
# text = Der CDU-Politiker strebt einen einheitlichen Wohnungsmarkt an, auf dem sich die Preise an der ortsüblichen Vergleichsmiete orientieren.
1	Der	DET	2	det	_	*
2	CDU	PROPN	4	compound	SpaceAfter=No	*
3	-	PUNCT	2	punct	SpaceAfter=No	*
4	Politiker	NOUN	5	nsubj	_	*
5	strebt	VERB	0	root	_	2:VPC.full
6	einen	DET	8	det	_	*
7	einheitlichen	ADJ	8	amod	_	*
8	Wohnungsmarkt	NOUN	5	obj	_	*
9	an	ADP	5	compound:prt	SpaceAfter=No	2
10	,	PUNCT	5	punct	_	*
11	auf	ADP	12	case	_	*
12	dem	PRON	20	obl	_	*
13	sich	PRON	20	obj	_	1:IRV
14	die	DET	15	det	_	*
15	Preise	NOUN	20	nsubj	_	*
16	an	ADP	19	case	_	*
17	der	DET	19	det	_	*
18	ortsüblichen	ADJ	19	amod	_	*
19	Vergleichsmiete	NOUN	20	obl	_	*
20	orientieren	VERB	8	acl	SpaceAfter=No	1
21	.	PUNCT	5	punct	_	*
>>> result = []
>>> # Conll objects are iterable over their sentences, and sentences are iterable
>>> # over their tokens. Sentences also de/serialize comment information.
>>> for sentence in data:
>>>     for token in sentence:
>>>
>>>         # Tokens have attributes such as upos, head, id, deprel, etc, and sentences
>>>         # can be indexed by a token's id. Extended attributes are stored under their
>>>         # namespace, which is also an attribute.
>>>         if token.upos == 'VERB' and 'VPC' in token.parseme.mwe:
>>>             review_sentences.append(sentence)
>>>
>>> print('Review the following sentences:')
>>> for sent in review_sentences:
>>>     print(sent.id)
['train-s1682']
```

## Uses and Limitations

This package edits CoNLL-U Plus annotations. This does not include the annotated text itself. Word forms on Tokens are not editable and Sentence Tokens cannot be reassigned or reordered. `pyconllup` focuses on editing CoNLL-U Plus annotation rather than creating it or changing the underlying text that is annotated.

Lastly, linguistic data can often be very large and this package attempts to keep that in mind. pyconll provides methods for creating in memory conll objects along with an iterate only version in case a corpus is too large to store in memory (the size of the memory structure is several times larger than the actual corpus file). The iterate only version can parse upwards of 100,000 words per second on a 16gb ram machine, so for most datasets to be used on a local dev machine, this package will perform well. The 2.2.0 release also improves parse time and memory footprint by about 25%!
