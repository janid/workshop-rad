# Part 1: Speaker Notes and Exercise Examples

These notes are for the presenter.
Each section matches a code cell in the notebook.
Read the "Say this" part before running the cell.
Read the "Expected output" part to explain the result after running.

---

## Setup

### Cell: Install packages

**Say this:**
Run this cell first. It installs the libraries we need for today.
It takes about 30 seconds. You will see a lot of output but no errors.

---

### Cell: GPU check

**Say this:**
This confirms that Colab connected us to a T4 GPU with 16 GB of memory.
All models we use today fit in this memory.
If you see "CPU" instead of "GPU", go to Runtime > Change runtime type and select T4 GPU.

**Expected output:**
```
GPU available: True
GPU model  : Tesla T4
GPU memory : 15.8 GB
```

---

## Part 1.1: The Tokenizer

### Cell: Load model

**Say this:**
We now download BERT from HuggingFace.
The first time takes about one minute because it downloads about 440 MB.
After that it is saved in cache and loads in seconds.
We add `attn_implementation="eager"` because we want to look at attention weights later.
Without this, newer versions of PyTorch use an optimized version that does not give us those weights.

**Expected output:**
```
Model loaded.
```
You may also see a warning about UNEXPECTED keys like `cls.predictions.bias`.
This is normal. BERT was pre-trained with two extra heads (masked language modeling and next sentence prediction).
We load only the encoder part, so those heads are not used. The warning tells us they were skipped.
You can ignore this.

---

### Cell: Tokenize text

**Say this:**
Let us see what the tokenizer does.
We give it one sentence and look at what comes out.

**Expected output:**
```
Tokens: ['[CLS]', 'the', 'workshop', 'on', 'large', 'language', 'models', 'starts', 'today', '.', '[SEP]']
Input IDs: [101, 1996, 9573, 2006, 2312, 2653, 4275, 4627, 2651, 1012, 102]
Attention mask: [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
```

**Explain the output:**
- The sentence has 9 words but we get 11 tokens. Two are added by the tokenizer: `[CLS]` at the start and `[SEP]` at the end.
- `[CLS]` has ID 101 and `[SEP]` has ID 102. These are always the same.
- All attention mask values are 1 because this is a single sentence with no padding.
- `bert-base-uncased` converts everything to lowercase, so "The" becomes "the".

---

### Cell: Subword tokenization

**Say this:**
Now let us see what happens with unusual words.
BERT knows about 30,000 tokens. Words it has not seen before are split into smaller pieces.

**Expected output:**
```
Word                         Tokens
-------------------------------------------------------
  tokenization               ['token', '##ization']
  transformers               ['transformers']
  supercalifragilistic       ['super', '##cal', '##if', '##rag', '##ili', '##stic']
  NVIDIA                     ['nv', '##id', '##ia']
  NLP                        ['nl', '##p']
```

**Explain the output:**
- "transformers" is a common word in the training data, so it has its own token.
- "supercalifragilistic" is very rare, so it is split into 6 pieces.
- "NVIDIA" and "NLP" are also split because BERT was trained on general text, not technical documents.
- The `##` prefix marks a continuation piece. The model learns to put these pieces back together.

---

## Part 1.2: Word Embeddings and Positional Encoding

### Cell: Helper functions

**Say this:**
These are two utility functions we will use to visualize vectors.
`cosine_similarity` measures how similar two vectors are.
`plot_heatmap` draws the result as a colored grid.
We do not need to understand the math in detail. Just run this cell.

---

### Cell: Word embedding heatmap

**Say this:**
Each token is now represented as a vector of 768 numbers.
Let us measure how similar these vectors are to each other.

**Expected output:**
The diagonal is always 1.0 (dark green) because every token is identical to itself.
Off-diagonal values are mostly low (yellow to red).

**Explain the output:**
- The diagonal is 1.0 everywhere because we compare each token to itself.
- Tokens with similar meanings will have a higher score. For example, "world" and "workshop" are both nouns about a broad concept and may score a bit higher than "!" and "world".
- `[CLS]` and `[SEP]` will look different from all content words. They are special tokens with no real word meaning.
- The key point: each token already has a different vector even before any transformer block processes it. The transformer will then mix these vectors using attention.

---

### Cell: Positional embedding heatmap

**Say this:**
Now let us look at the positional embeddings.
These vectors only depend on the position, not on the word.

**Expected output:**
A clear gradient pattern. Position 0 is most similar to position 1, a bit less to position 2, and so on.
The top-left and bottom-right corners are darkest. The top-right and bottom-left are lightest.

**Explain the output:**
- Compare this to the word embedding heatmap. That one had no clear pattern.
- This one has a clear gradient: nearby positions are more similar.
- This is exactly what we want. The model uses this to know where each token is in the sentence.
- Position 0 and position 1 are very similar. Position 0 and position 10 are very different.

---

## Part 1.3: Attention Mechanism

### Cell: Forward pass

**Say this:**
We now run the full BERT model and ask it to save the attention weights.
This is called a forward pass.

**Expected output:**
```
Number of layers : 12
Attention shape  : (1, 12, 12, 12)  [batch, heads, seq, seq]
```

**Explain the output:**
- 12 layers, each with 12 heads.
- The shape (1, 12, 12, 12) means: 1 sentence, 12 heads, 12 tokens, 12 tokens.
- Each head produces a 12x12 matrix. Each cell says: "how much does token i pay attention to token j?"

---

### Cell: Attention heatmap

**Say this:**
We now plot the attention matrix for one specific head.
Layer 11 (the last layer), head 5 is a good starting point.

**Expected output:**
A heatmap where some rows have a clear bright spot in one column.

**Explain the output:**
- Each row is a token. The brightness in that row shows which tokens it pays attention to.
- Look at the row for "it". In many heads at this layer, "it" pays strong attention to "cat". This is the model learning that "it" refers to "cat".
- The `[CLS]` token often attends to many tokens at once because it collects information from the whole sentence.
- Try `layer_idx = 0` to see early layers. Early layers often show local patterns (each token attending to its neighbors). Later layers show more global patterns (long-distance relationships).

**Good combinations to show:**
- Layer 11, Head 5: shows "it" attending to "cat" (coreference)
- Layer 0, Head 0: shows mostly local attention (each token looks at nearby tokens)
- Layer 5, Head 3: shows `[CLS]` collecting from many tokens

---

## Exercises

### Exercise 1: Multilingual Sentiment Analysis

**Say this before running:**
This model was trained on product reviews in six languages:
English, Dutch, German, French, Spanish, and Italian.
It gives a star rating from 1 to 5.
Note: because it was trained on product reviews, it may be less accurate
for other types of text like news, social media, or academic writing.
Let us run the four example sentences and then you will add your own.

**Expected output for the four examples:**
```
Sentence                                             Stars      Score
---------------------------------------------------------------------------
This workshop is very well organized!                5 stars    0.72
The coffee machine is broken again.                  2 stars    0.43
Das Essen war ausgezeichnet.                         5 stars    0.81
Le service etait correct, mais pas exceptionnel.     3 stars    0.47
```

**Explain the output:**
- The first sentence is clearly positive, so 5 stars with high confidence (0.72).
- The second sentence is negative but not strongly, so 2 stars with lower confidence.
- The German sentence is positive and the model handles it well even though it says nothing about stars.
- The French sentence is mixed ("correct but not great"), so 3 stars.

**Example sentences to suggest to students:**

Good for showing strong positive (5 stars):
```
"The best course I have ever attended!"
"Excellent quality, I am very happy with this product."
"Perfetto, non potrei essere più soddisfatto."  (Italian: Perfect, I could not be more satisfied.)
```

Good for showing strong negative (1 star):
```
"Terrible experience, everything went wrong."
"Esta es la peor clase que he tenido."  (Spanish: This is the worst class I have had.)
"Völlig enttäuschend, nie wieder."  (German: Completely disappointing, never again.)
```

Good for showing the sarcasm problem:
```
"Oh great, the printer is broken again. What a wonderful day."
```
Expected: the model gives 3 or 4 stars because it reads "great" and "wonderful" literally.
This is a known limitation. Detecting sarcasm requires understanding context that is outside the sentence.

Good for showing a neutral sentence:
```
"The product arrived on time."
```
Expected: 3 stars. The sentence has no emotional words so the model is not sure.

---

### Exercise 2: Named Entity Recognition

**Say this before running:**
This model reads a sentence and finds named entities.
It labels each one as a person, organization, location, or miscellaneous.

**Expected output for the example text:**
```
Text: Jani Dugonik works at the University of Maribor in Slovenia. He will present his research in Sweden next August.

Entity                    Type       Score
------------------------------------------------
Jani Dugonik              PER        0.9987
University of Maribor     ORG        0.9901
Slovenia                  LOC        0.9995
Sweden                    LOC        0.9998
```

**Explain the output:**
- "Jani Dugonik" is correctly found as a person with very high confidence.
- "University of Maribor" is found as an organization (ORG) even though it contains a location name.
- "Slovenia" and "Sweden" are both found as locations.
- "August" is not tagged. It is a month name, not a named entity.
- The scores are very high (above 0.99) for well-known entity types. Lower scores appear for ambiguous cases.

**Example sentences to suggest to students:**

Good for showing all four types (PER, ORG, LOC, MISC):
```
"Cristiano Ronaldo plays for Al Nassr in Riyadh and will compete at the FIFA World Cup."
```
Expected:
- Cristiano Ronaldo -> PER
- Al Nassr -> ORG
- Riyadh -> LOC
- FIFA World Cup -> MISC (it is an event)

Good for showing the Amazon ambiguity:
```
"Amazon announced new products at their Seattle headquarters."
```
Expected: Amazon -> ORG (company context), Seattle -> LOC

```
"The Amazon river flows through Brazil and Colombia."
```
Expected: Amazon -> LOC (river context), Brazil -> LOC, Colombia -> LOC

This is a good example to show students that the model uses the words around the entity to decide the type.

Good for showing a harder case:
```
"Apple and Microsoft both presented at the Paris conference."
```
Expected: Apple -> ORG, Microsoft -> ORG, Paris -> LOC
But if you write "Apple is a fruit grown in Washington state." the model should give Apple a low confidence or miss it, because there is no clear organization context.

---

### Exercise 3: Zero-Shot Classification

**Say this before running:**
This is different from the previous two exercises.
In those exercises, the model was trained on specific categories (stars, entity types).
Here, we give the model any category names we want and it figures out which one fits best.
The model has never seen these category names during training.

**Expected output for the example text:**
```
Text: Researchers have developed a new neural network architecture...

Label                        Score  Bar
-------------------------------------------------------
scientific research          0.5234  #####################
artificial intelligence      0.3891  ###############
economy                      0.0512  ##
politics                     0.0241  #
sports                       0.0122
```
(exact scores may differ slightly between runs)

**Explain the output:**
- "scientific research" and "artificial intelligence" are both correct and get the highest scores.
- "economy", "politics", and "sports" are clearly wrong and get very low scores.
- The scores always add up to 1.0 because the model treats this as a probability distribution.

**What happens when you add more labels:**

Add "machine learning" to the list:
- It will take some score away from "artificial intelligence" because the two topics are very similar.
- Together they may add up to roughly what "artificial intelligence" alone had before.
- This shows that adding similar labels splits the probability.

Add "technology":
- "technology" is a broad label that covers both AI and research, so it may score high.
- This can push both "scientific research" and "artificial intelligence" down a bit.

**A short ambiguous sentence to try:**
```
"Apple released a new product yesterday."
```
Try these labels: "technology", "food", "business", "agriculture"

Expected behavior:
- "technology" and "business" score high (Apple the company is the most common interpretation)
- "food" and "agriculture" score low but not zero (the word "apple" can also mean a fruit)
- This is a good example to discuss: the model uses the full sentence context, not just one word

**Another short sentence:**
```
"It was raining heavily in the city center."
```
Try: "weather", "sports", "news", "science"
Expected: "weather" very high, others very low. This shows the model can be very confident when the topic is clear.

---

## General Notes for Exercises

When students add their own sentences, ask them to explain what result they expected and whether the model agreed.
If the model is wrong, discuss why:
- Sarcasm (Exercise 1): the model reads words literally
- Ambiguous names (Exercise 2): the model uses context but can still be wrong
- Similar labels (Exercise 3): adding overlapping categories splits the score

These are real limitations of current models and good discussion points.
