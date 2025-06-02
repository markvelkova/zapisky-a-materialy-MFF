# Evaluation measures in NLP.
## Give at least two examples of situations in which measuring a percentage accuracy is not adequate. (1 point)
## Explain: precision, recall (1 point)
## What is F-measure, what is it useful for? (1 point)
## What is k-fold cross-validation ? (1 point)
## Explain BLEU (the exact formula not needed, just the main principles). (1 point)
## Explain the purpose of brevity penalty in BLEU. (1 point)
## What is Labeled Attachment Score (in parsing)? (1 point)
## What is Word Error Rate (in speech recognition)? (1 point)
## What is inter-annotator agreement? How can it be measured? (1 point)
## What is Cohen's kappa? (1 point)
# Deep learning for NLP.
## Describe the two methods for training of the Word2Vec model. (1 point) 
### CBOW
- minimize cross-entropy of the middle word of a sliding windows
![image](https://github.com/user-attachments/assets/620a7d66-fc14-42d1-afef-82bd9ea15e28)
### Skip-gram
- minimize cross-entropy of a bag of words around a word (LM other way
round)
![image](https://github.com/user-attachments/assets/fca3068a-a0be-4116-95b9-ed66d196ac12)

## Use formulas to describe how Word2Vec is trained with negative sampling. (2 poitns)
- technique used to efficiently approximate the softmax function in the Skip-gram model.
- máme tohle
- ![image](https://github.com/user-attachments/assets/2ab2f010-f2c1-4a12-9353-0143db896c77)
- ale je tam neefektivní suma přes všechny slova
- podíváme se na to jako binární klasifikaci a chceme zlepšovat rozlišování pravých samplů a špatných negativních
- $V'_{w_o}$ jsou slova z kontextu, outputový vektor
- $V'_{w_I}$ je inputové slovo
- sigma je sigmoida, je to prostě takový spojitý signum, převádí cokoli na škálu 0 až jedna tuším, takže umožnuje binární klasifikaci
- část s logaritmem **vlevo jsou reálné páry**, chcem ten **vnitřek co nejvyšší** -> z toho co **nejmenší loss** nám dá logaritmus 0 pro 1 ( a sigmoida nedá víc, než jedničku)
- suma vpravo je přes $k$ těch nesmyslných samplů
  - ro s tou vlnovkou je prostě zápis toho, že je samplujeme z nějaké distribuce
  - u **logaritmu vpravo chceme samotný součin co nejvíc záporný** minus nám to pak hodí do kladna, sigmoidaudělá jedničku -> logaritmus udělá 0 -> máme nulový loss
  - ![image](https://github.com/user-attachments/assets/d4f04549-45d6-4543-a186-cf9de7f5f64f)
- takže ta věc se trénuje, aby předpovídala nulu pro negativní random samply a jedničku pro pravé

## Explain the difference between Word2Vec and FastText embeddings. (1 point)
- Word2Vec training treats words as independent entities
- **FastText handels it by smaller units - morphology**
1. Represent each word as a bag of character 𝑛-grams
2. Keep a table of character 𝑛-grams embeddings instead of words
3. word embedding is average of character n-gram bag
## Sketch the structure of the Transformer model. (2 points)
![image](https://github.com/user-attachments/assets/f826c5f8-819d-4a6f-bb92-e65c8cb285f6)

## Why do we use positional encodings in the Transformer model. (1 point)
- model is given just words, tokens whatever and it dooes not know about the position of the word --> give it position embeddings!
## What are residual connections in neural networks? Why do we use them? (1 point)
- input of the layer is added to its output
- layer can do just nothing, **vanishing gradient no longer a problem**
## Use formulas to express the loss function for training sequence labeling tasks. (1 point)
![image](https://github.com/user-attachments/assets/25d041fd-1099-4d08-afb3-406baa773e2e)
- output vrstva softmaxem vyrobí one-hot reprezentaci - doatneme kategorii
- loss je potom krosentropie našich pravděpodobností a toho, co to mělo být
## Explain the pre-training procedure of the BERT model. (2 points)
- 12 transformer layers
- we give it changed sequence and it guesses, aht was the original
- predicts also missed words
- ![image](https://github.com/user-attachments/assets/bb80e12e-d37d-463f-9696-ae32288c2b1c)

## Explain what is the pre-train and finetune paradigm in NLP. (1 points)
![image](https://github.com/user-attachments/assets/b6bd9537-2219-45d4-a758-064d20f21bc7)

## Describe the task of named entitity recognition (NER). Explain the intution behind the CRF models compared to standard sequence labeling. (2 points)
### NER
- one of sequence labelling tasks -> person, organization, city...
- Whom does pronoun “they” refer to? Who is “the president” in a text?
### CRF (Conditional Random Fields) vs  standard sequence labeling
- standard sequence labeling **labels tokens independently**
- CRF has **label transitions**, predicts jointly whole the sentence
- it avoids weird collocations
## Explain how does the self-attention differ in encoder-only and decoder-only models. (1 point)
### encoder - BERT
- bidirectional links
- can depend on past or future tokens, even on everything
- classification, labeling
### decoder - GPT
- self attention only to past
- masking to ban the future tokens dependency
- generative tasks
# Machine translation fundamentals.
## Why is MT difficult from linguistic point of view? Provide examples and explanation for at least three different phenomena. (2 points)
## Why is MT difficult from computational point of view? (1 point)
## Briefly describe at least three methods of manual MT evaluation. (1-2 points)
## Describe BLEU. 1 point for the core properties explained, 1 point for the (commented) formula.
## Describe IBM Model 1 for word alignment, highlighting the EM structure of the algorithm. (1 point)
## Explain using equations the relation between Noisy channel model and log-linear model for classical statistical MT. (2 points)
## Describe the loop of weight optimization for the log-linear model as used in phrase-based MT. (1 point)

# Neural machine translation.
## Describe the critical limitation of PBMT that NMT solves. Provide example training data and example input where PBMT is very likely to introduce an error. (1 points)
## Use formulas to highlight the similarity of NMT and LMs. (1 point)
## Describe, how words are fed to current NMT architectures and explain why is this beneficial over 1-hot representation. (1 point)
## Sketch the structure of an encoder-decoder architecture of neural MT, remember to describe the components in the picture (2 points)
## What is the difference in RNN decoder application at training time vs. at runtime? (1 point)
## What problem does attention in NMT address? Provide the key idea of the method. (1 point)
## What problem/task do both RNN and self-attention resolve and what is the main benefit of self-attention over RNN? (1 point)
## What are the three roles each state at a Transformer encoder layer takes in self-attention. (1 point)
## What are the three uses of self-attention in the Transformer model? (1 point)
## Provide an example of NMT improvement that was assumed to come from additional linguistic information but occurred also for a simpler reason. (1 point)
## Summarize and compare the strategy of "classical statistical MT" vs. the strategy of neural approaches to MT. (1 point)
