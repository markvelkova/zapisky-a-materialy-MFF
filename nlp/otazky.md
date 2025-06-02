# Evaluation measures in NLP.
## Give at least two examples of situations in which measuring a percentage accuracy is not adequate. (1 point)
- translation - not exactly and only one slution correct
- NER - kdyby bylo 90% univerzálních entit, dávalo by to všude univerzální popisek
## Explain: precision, recall (1 point)
![image](https://github.com/user-attachments/assets/3bb3c04f-1ebe-4e89-bdc1-b67230a58fdd)

## What is F-measure, what is it useful for? (1 point)
- weighted harmonic mean of P and R
- ![image](https://github.com/user-attachments/assets/aab28e90-bc37-4ad7-919d-c64a47c0d9eb)
- just harmonic mean
- ![image](https://github.com/user-attachments/assets/6e32d19d-a31b-4949-96d8-461f58fa366e)
- různý poměr váhy recall a recision
## What is k-fold cross-validation ? (1 point)
- split dataset into k roughly equal parts (folds).
1. For each fold $i=1,2,...,k$:
2. Use fold $i$ as the test set.
3. Use the remaining $k−1$ folds as the training set.
4. Train the model on the training folds and evaluate on the test fold.
- aggregate the performance metrics (accuracy, F1, etc.) across all $k$ runs by averaging
- all data is used like testion and trainign
## Explain BLEU (the exact formula not needed, just the main principles). (1 point)
- n-grams of words from 1-4 that are same with the reference transl.
- celková chyba je z logaritmů poměru, co máme dobře
- BP is brevity penalty - moc krátké věty
- ![image](https://github.com/user-attachments/assets/fe7cbed2-2042-42f2-988a-99a918115235)
## Explain the purpose of brevity penalty in BLEU. (1 point)
- u moc krátkých vět by se v precision blížil jmenovatel nule
## What is Labeled Attachment Score (in parsing)? (1 point)
- ![image](https://github.com/user-attachments/assets/2a27bcb6-8628-44a1-8564-a1fcb6e86a7a)
- head je, že se to vztahuje ke spravnemu rodici ve stromove struktuře, label je POS třeba
## What is Word Error Rate (in speech recognition)? (1 point)
- ![image](https://github.com/user-attachments/assets/d2f58abc-3149-41b9-9f22-77ff99e38f5b)

## What is inter-annotator agreement? How can it be measured? (1 point)
Example:
- two annotators making classifications into two classes, A and B
- 1st annotator: 80% A, 20% B
- 2nd annotator 85% A, 15% B
- probability of agreement by chance: 0.8*0.85 + 0.2*0.15 = 71%
- desired measure: 1 if they agree in all decisions, 0 if their agreement is equal to agreement by chance

## What is Cohen's kappa? (1 point)
- ![image](https://github.com/user-attachments/assets/a19735e8-9921-41bb-a862-4074367c3989)
- measure of agreement
- $P_e$ je agreement by chance, $P_a$ is relative observed agreement betwen two anotaors
- scale from −1 to +1 (negative kappa unexpected but possible)
- interpretation still unclear, but at least we abstracted from the by-chance agreement baseline
- conventional interpretation: ..0.40-0.59 weak agreement, 0.60-79 moderate agreement, 0.80-0.90 strong agreements

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
- Ambiguity and word senses- plant is next to the bank, ženu holí stroj, Spal celou Petkevičovu přednášku.
- Target word forms - cases, genders - různé pády kočky jsou v angličtině stejné
- Negation - ve francouzštině je okolo slova, v češtině může být vícenásobná a rušit se navzájem
- Pronouns - english explicit, czcech slavný nevyjádřený podmět
- Co-ordination and apposition; word order - Předseda vlády, Petr Nečas , a Martin Lhota přednesli příspěvky **přístavek**
- Space of possible translations - je hrozná spousta validních překladů
- idioms
## Why is MT difficult from computational point of view? (1 point)
## Briefly describe at least three methods of manual MT evaluation. (1-2 points)
### ranking
- sometimes hard to rank - everything equally poor
### blind editing 
- you dont see the source, but can correct the translation
### judging
- yes/no

## Describe BLEU. 1 point for the core properties explained, 1 point for the (commented) formula.
- n-grams of words from 1-4 that are same with the reference transl.
- celková chyba je z logaritmů poměru, co máme dobře
- BP is brevity penalty - moc krátké věty
- ![image](https://github.com/user-attachments/assets/fe7cbed2-2042-42f2-988a-99a918115235)

## Describe IBM Model 1 for word alignment, highlighting the EM structure of the algorithm. (1 point)
- words are aligned one to one independently
- learns the probabilities of $P(target| source)$
### EM = expectaton maximzation
- to stejný jako policy a value iteration v ai
- určím si pravděpodobnosti, spočítám podle nich počty výskytů, minimalizuju rozdíl oproti skutečnosti
## Explain using equations the relation between Noisy channel model and log-linear model for classical statistical MT. (2 points)
![image](https://github.com/user-attachments/assets/49ee643e-71d0-4bec-80fe-4b50a193b2df)
- f je foreign, e je english
- přistupujeme k překladu jako k dekodovani corrupted signalu
- hledám v+tu, která maximalizuje tu pravděpodobnost, klidn+ je možný přeskošit odvození, říká to poslední řádek
## Describe the loop of weight optimization for the log-linear model as used in phrase-based MT. (1 point)
![image](https://github.com/user-attachments/assets/0352d776-e019-40da-b4a3-325e9fc65442)
- zase value/policy iteration
- nastavám váhy, přeložím, porovnám, upravím váhy

# Neural machine translation.
## Describe the critical limitation of PBMT that NMT solves. Provide example training data and example input where PBMT is very likely to introduce an error. (1 points)
### Phrase-based MT:
- is a log-linear model
- **assumes phrases relatively independent** of each other
- decomposes sentence into contiguous phrases
- search has two parts:
- training
1. Align words.
2. Extract (and score) phrases consistent with word alignment.
3. Optimize weights (MERT).
### critical limitation of phrase based MT
- phrases ntot independent, "nemám" "žádného" splou souvisí
- ![image](https://github.com/user-attachments/assets/e90ad431-b162-4a6c-999b-dae8c069ede3)
## Use formulas to highlight the similarity of NMT and LMs. (1 point)
### language modelling
![image](https://github.com/user-attachments/assets/618e3042-2146-4264-b2ab-7521dd7cda02)
### nerual MT
![image](https://github.com/user-attachments/assets/f9f94408-cd59-44dd-84cd-4da4a295a8e8)

## Describe, how words are fed to current NMT architectures and explain why is this beneficial over 1-hot representation. (1 point)
### embeddings
- maps word to a vector
- dimension do not have clear interpretation
- similar words close
- Word2Vec
### one-hot drawbacks 
- huge sparse matrix
- everything equally far
## Sketch the structure of an encoder-decoder architecture of neural MT, remember to describe the components in the picture (2 points)
### encoder
- ![image](https://github.com/user-attachments/assets/6978664c-8c75-42df-9187-35b7321dd35c)
### decoder
- ![image](https://github.com/user-attachments/assets/7e197932-2334-4417-a7c1-984621add42c)
### en-dec
- ![image](https://github.com/user-attachments/assets/b0e2aa9f-2637-4313-a7ae-073297fbb95e)
- ![image](https://github.com/user-attachments/assets/e5653b54-27fc-4f5d-9a97-c8c6b69a6bc1)

## What is the difference in RNN decoder application at training time vs. at runtime? (1 point)
### RNN - recurrent neural networks
![image](https://github.com/user-attachments/assets/24546fdb-92e2-438b-be07-3ef84821f2a7)
### difference
- ![image](https://github.com/user-attachments/assets/121c7637-540d-4e6d-9b54-200f33b34611)
- při tréninku používáme pravdu na výpočet chyby
- při běhu už nevíme správná řešení
## What problem does attention in NMT address? Provide the key idea of the method. (1 point)
- Arbitrary-length sentences fit badly into a fixed vector.
- Reading input backward works better, because early words will be more salient.
⇒ Use Bi-directional RNN and “attend” to all states $h_i$
- ![image](https://github.com/user-attachments/assets/23fbe65f-2068-4058-aed7-e2169b2200fc)

## What problem/task do both RNN and self-attention resolve and what is the main benefit of self-attention over RNN? (1 point)
- sequences of arbitrary length
- SAN can acccess any position in constant time, paralelizable
- RRN ong-range dependencies harder to capture and slower to compute.
## What are the three roles each state at a Transformer encoder layer takes in self-attention. (1 point)
### Query (Q)
– Asks: "What am I looking for?"
### Key (K)
– Answers: "What do I contain?"
### Value (V)
– Provides the actual information to be passed forward.
### Mechanism
- each token computes attention to all other tokens by comparing its query to their keys, and then aggregates their values accordingly:
## What are the three uses of self-attention in the Transformer model? (1 point)
###  Encoder-Decoder Attention:
- Q: previous decoder layers; K = V: outputs of encoder
⇒ Decoder positions attend to all positions of the input.
### Encoder Self-Attention:
- Q = K = V: outputs of the previous layer of the encoder
⇒ Encoder positions attend to all positions of previous layer.
### Decoder Self-Attention:
- Q = K = V: outputs of the previous decoder layer.
- Masking used to prevent depending on future outputs.

## Provide an example of NMT improvement that was assumed to come from additional linguistic information but occurred also for a simpler reason. (1 point)
- researchers added POS tags, it helped, but also did randomizing
## Summarize and compare the strategy of "classical statistical MT" vs. the strategy of neural approaches to MT. (1 point)
| Aspect                 | **Classical SMT**                                      | **Neural MT (NMT)**                       |
| ---------------------- | ------------------------------------------------------ | ----------------------------------------- |
| **How**           | Modular: Translation model + Language model            | End-to-end neural model                   |
| **Representations**     | Discrete symbols (words, phrases)                      | Continuous vectors (embeddings)           |
| **how does it learn** | Maximizes $P(e \mid f)$ using hand-crafted components  | Learns to generate $P(e \mid f)$ jointly  |
| **via what does it learn**           | Manually engineered (e.g., word alignment, distortion) | Automatically learned from data           |
| **decoding**           | Phrase table + language model; heuristics              | Beam search over softmax outputs          |
| **strengths**          | Transparent, interpretable, modular                    | Fluent, better generalization, contextual |
| **weaknesses**         | Brittle, error-prone, poor generalization              | Needs lots of data, less interpretable    |

