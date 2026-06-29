# Part 2: Speaker Notes and Exercise Examples

These notes are for the presenter.
Each section matches a code cell in the notebook.
Read the "Say this" part before running the cell.
Read the "Expected output" part to explain the result after running.

---

## Setup

### Cell: Install packages

**Say this:**
Run this cell first. It installs the libraries we need for Part 2.
Even if Part 1 already installed some of them, we run this again
because each Colab session starts fresh.

---

### Cell: GPU check

**Say this:**
We confirm that Colab gave us the T4 GPU again.

**Expected output:**
```
GPU available: True
GPU model  : Tesla T4
GPU memory : 15.8 GB
```

---

## Part 2.1: Text Generation with GPT-2

### Cell: Load GPT-2

**Say this:**
We load GPT-2 medium, which has 345 million parameters.
This is bigger than gpt2-small and produces better quality text.
It still loads quickly because it is only about 1.5 GB.
Notice the long comment block. This lists other decoder models you can try later,
including multilingual ones like Qwen and BLOOM.

**Expected output:**
```
Model loaded.
```

---

### Cell: Basic text generation

**Say this:**
We give the model a starting sentence and it continues it.
We ask for three different completions so we can compare them.
We fix the random seed to 42 so everyone in the room sees the same result.

**Expected output:**
```
Prompt: 'The future of artificial intelligence is'

Completion 1:  not just about machines being able to think, but about
               machines being able to feel. The future of AI is about...

Completion 2:  bright, and the potential for it to transform our lives
               is enormous. But there are also risks...

Completion 3:  uncertain. We don't know what the future holds for AI,
               but we do know that it will be transformative...
```
(exact words vary, but all completions are grammatically correct English)

**Explain the output:**
- The model never memorized these sentences. It generates new text each time based on patterns.
- All three are different because do_sample=True adds randomness to token selection.
- GPT-2 medium produces more coherent text than the smaller gpt2 version.
- The model has no knowledge of facts. It only knows which words tend to follow other words.
  It may say things that are grammatically correct but factually wrong.

---

### Cell: Compare sampling settings

**Say this:**
Now we compare six different settings on the same prompt.
Watch how the output changes from top to bottom.

**Expected output:**
```
[greedy (no sampling)]
  could support life, according to a new study published in the journal Nature.

[temperature = 0.3]
  could support life, according to a new study. The planet orbits a star...

[temperature = 1.0]
  orbits a star much like our own sun, and researchers believe it could...

[temperature = 1.5]
  drifts through the outer edges of a binary star system, bathed in...

[top_k = 5]
  could support life, according to a new study published in the journal...

[top_p = 0.9]
  may be capable of supporting liquid water, say astronomers at NASA...
```

**Explain the output:**
- Greedy always gives exactly the same result. Run this cell again and row 1 never changes.
- Temperature 0.3 is very close to greedy: safe and predictable.
- Temperature 1.0 is the default. Creative but still readable.
- Temperature 1.5 is more surprising. May occasionally produce unusual word choices.
- top_k=5 only picks from the 5 most probable tokens. Controlled and consistent.
- top_p=0.9 is nucleus sampling. This is the default in most modern chatbots like ChatGPT.
  It adapts: when the model is confident it uses fewer tokens, when uncertain it uses more.

---

### Exercise 1: Text Generation

**Say this before running:**
Now try your own prompt. Change the my_prompt variable and run the cell.
Then try different temperature values one at a time.
Note: we removed set_seed so each run gives a different result.

**Examples to suggest to students:**

Good prompts:
```
"Once upon a time in a small village near the mountains"
"The most important thing in machine learning is"
"Scientists at the University of Maribor have recently discovered"
"In the year 2050, every student will"
```

What to observe:
- temperature=0.1: run three times, results are almost identical each time
- temperature=1.5: run three times, results are noticeably different each time
- top_k=3: output sounds flat and repetitive
- top_k=100: more variety, sometimes unexpected words

Discussion question:
"Which setting would you use for a customer support chatbot?
Which for a creative story generator?"
Answer: low temperature for support (predictable and safe),
higher temperature for creative writing (varied and interesting).

---

## Part 2.2: Translation and Summarization with T5

### Cell: Load T5

**Say this:**
T5 uses an encoder-decoder architecture.
Unlike GPT-2 which only has a decoder, T5 first reads the whole input,
then generates the output.
We load it without the pipeline API because newer versions of transformers
changed how pipelines work for T5.
We define a simple helper function t5_run that handles everything for us.

**Expected output:**
```
Model loaded.
```

---

### Cell: Translation

**Say this:**
T5 was trained on many different tasks using text prefixes.
The prefix "translate English to French:" tells the model what task to do.
This was an early form of instruction following, two years before ChatGPT.

**Expected output:**
```
Input : translate English to French: Hello, how are you today?
Output: Bonjour, comment allez-vous aujourd'hui?

Input : translate English to German: The workshop starts at nine in the morning.
Output: Der Workshop beginnt um neun Uhr morgens.

Input : translate English to Romanian: Machine learning is a fascinating field.
Output: Invatarea automata este un domeniu fascinant.
```

**Explain the output:**
- French and German translations are good quality for simple sentences.
- Romanian may have small errors. T5-base is a general model, not a specialist translator.
- All three use the same model weights. Only the prefix text changes.
- For production-quality translation, dedicated models like Helsinki-NLP/opus-mt work better.
  But this shows the concept clearly.

---

### Cell: Summarization

**Say this:**
The same model, the same weights, but a different prefix: "summarize:".
We give it a paragraph about transformers and ask for a shorter version.

**Expected output:**
```
Original text:
The transformer architecture was introduced in 2017 in the paper
Attention Is All You Need by Vaswani et al. [...]

Summary:
the transformer architecture was introduced in 2017 in the paper attention is all you need.
it replaced recurrent neural networks with a self-attention mechanism.
```

**Explain the output:**
- The summary keeps the main facts: year, paper title, key idea.
- T5-base produces lowercase output. This is a known quirk of the model.
- The summary is shorter but not dramatically so. Larger models give tighter summaries.
- Notice the model chose "self-attention mechanism" from the original text.
  It learned what information is most important to keep.

---

### Exercise 2: Translation and Summarization

**Say this before running:**
Replace the example text with your own sentence to translate,
and your own paragraph to summarize.
The more specific and concrete your text, the better the result.

**Examples to suggest to students:**

For translation:
```
"translate English to French: Artificial intelligence is changing how we work and learn."
"translate English to German: Please send me the report by Friday."
"translate English to French: The conference will take place in Ljubljana next month."
```

For summarization (suggest students paste from Wikipedia or a news article):
```
"The James Webb Space Telescope was launched in December 2021.
It is the largest and most powerful space telescope ever built.
It observes the universe in infrared light, which lets it see through dust
and observe the earliest galaxies formed after the Big Bang.
The telescope is located about 1.5 million kilometers from Earth."
```
Expected summary: "the james webb space telescope was launched in december 2021.
it is the largest and most powerful space telescope ever built."

Good discussion point:
Ask students what the model kept and what it dropped.
The summary usually keeps the most concrete facts (date, superlative description)
and drops supporting details (infrared, 1.5 million km).

---

## Part 2.3: Text and Image Similarity with CLIP

### Cell: Download images

**Say this:**
We download three images from Wikipedia Commons.
We need to add a User-Agent header because Wikipedia blocks requests without one.
The three images are a dog, a cat, and the Eiffel Tower.
These are very different from each other, which makes the CLIP demo easy to read.

**Expected output:**
```
Downloading images...
  OK: a yellow dog on grass
  OK: a cat sitting on a surface
  OK: the Eiffel Tower in Paris
```
Then the three images appear side by side.

---

### Cell: Load CLIP

**Say this:**
CLIP was trained by OpenAI on 400 million image-text pairs.
We load a version that uses 32x32 pixel patches.
Note: we call the model's internal layers directly instead of the high-level API.
This is because newer versions of transformers changed the output format.

**Expected output:**
```
CLIP loaded.
```

---

### Cell: CLIP similarity heatmap

**Say this:**
We compute a vector for each image and for each text description.
We then measure cosine similarity between every image-text pair.
The result is a 3x5 grid. Look at which column has the highest value in each row.

**Expected output:**
A blue heatmap. Typical values:

```
                         a yellow  a cat si  the Eiff  a red sp  a bowl o
Img 1: a yellow dog    [  0.31      0.19      0.14      0.11      0.10  ]
Img 2: a cat sitting   [  0.19      0.28      0.12      0.10      0.11  ]
Img 3: the Eiffel Tow  [  0.11      0.10      0.33      0.09      0.08  ]
```
(exact values vary slightly)

**Explain the output:**
- Each row has its highest value in the column that matches its image. This is correct zero-shot classification.
- Scores are around 0.28 to 0.33 for correct pairs. This looks low but cosine similarity
  in a 512-dimensional space naturally gives smaller values than in 2D.
  What matters is that correct pairs score higher than wrong pairs.
- "a red sports car" and "a bowl of fruit" score around 0.08 to 0.11 for all images.
  The model correctly says none of these images show a car or fruit.

---

### Exercise 3: CLIP Text-Image Matching

**Say this before running:**
Uncomment some of the example descriptions or write your own.
Add them to the list and run the cell again.
Watch how the scores shift.

**Specific examples to try and expected results:**

```
"an animal with four legs"
```
Expected: scores high for both dog (~0.22) and cat (~0.21), low for Eiffel Tower (~0.09).
The description is true for both animals so CLIP cannot distinguish between them.

```
"a tall metal structure"
```
Expected: scores highest for Eiffel Tower (~0.20), lower for dog and cat (~0.10).
The model connects "metal structure" with the tower even without the word "Eiffel".

```
"a fluffy domestic animal"
```
Expected: scores highest for cat (~0.23), a bit lower for dog (~0.18).
The word "fluffy" helps separate cat from dog.

```
"a rocket launching into space"
```
Expected: scores very low for all three images (~0.07-0.09).
None of the images match this description at all.

Good discussion point:
Ask students why "an animal with four legs" matches both dog and cat equally.
Then ask what description would help CLIP distinguish between them.
Answer: more specific details like color, fur texture, or breed.

---

## Part 2.4: Image Captioning with ViT + GPT-2

### Cell: Load model

**Say this:**
This model combines a Vision Transformer encoder with a GPT-2 decoder.
The ViT splits each image into patches of 16x16 pixels and encodes them.
GPT-2 then reads those patch encodings and writes a caption word by word.
This is called cross-attention: the decoder looks at the image patches
at every step while generating text.

**Expected output:**
```
Model loaded.
```

---

### Cell: Generate captions

**Say this:**
We run all three images through the model and show the results.
The generated caption appears above each image.
The true description we gave is shown below.

**Expected output:**
Something like:
```
Caption: a dog sitting in the grass looking at the camera
True:    a yellow dog on grass

Caption: a cat sitting on a white surface looking at the camera
True:    a cat sitting on a surface

Caption: the eiffel tower in paris france
True:    the Eiffel Tower in Paris
```

**Explain the output:**
- All three captions are correct. The model identifies the main subject of each image.
- The captions are short and simple because this model was trained on short caption datasets.
- The Eiffel Tower caption is usually very accurate because it is a well-known landmark
  with many training examples.
- Compare to CLIP: CLIP chose between options we gave it.
  This model generates new text with no options to select from.
- Larger models like BLIP-2 produce much richer captions:
  "A golden retriever dog sitting on green grass in a park, looking directly at the camera."

---

## Part 2.5: Speech Recognition with Whisper

### Cell: Load audio sample

**Say this:**
We load a short audio clip from LibriSpeech, a public English audiobook dataset.
The clip is about 6 seconds long.
After this cell runs, you will see an audio player. Click play to hear the recording.
It is a man reading from a Victorian-era book.

**Expected output:**
```
Audio length : 5.9 seconds
Sampling rate: 16000 Hz
Ground truth : MISTER QUILTER IS THE APOSTLE OF THE MIDDLE CLASSES AND WE ARE GLAD TO WELCOME HIS GOSPEL
```
An audio player appears. The recording is clear with no background noise.

---

### Cell: Transcribe with Whisper

**Say this:**
Whisper converts the audio into a mel spectrogram first.
This is a 2D image that shows which frequencies are present at each moment in time.
The encoder reads this image, and the decoder generates the transcript word by word.
Let us see how close Whisper gets to the ground truth.

**Expected output:**
```
Ground truth  : MISTER QUILTER IS THE APOSTLE OF THE MIDDLE CLASSES AND WE ARE GLAD TO WELCOME HIS GOSPEL
Transcription :  Mister Quilter is the apostle of the middle classes and we are glad to welcome his gospel.
```

**Explain the output:**
- The transcription is almost perfect. Word for word the same as the ground truth.
- The only differences are punctuation (Whisper adds a period), capitalization,
  and a small space at the start. These are purely formatting differences.
- The ground truth is all uppercase because it comes from an old dataset format, not because someone shouted.
- Whisper-small is very strong on clean English speech like this.
  For noisy audio, accented speech, or less common languages, whisper-medium or whisper-large-v3 work better.

---

## Optional: Stable Diffusion

### Cell: Generate image

**Say this:**
Diffusion is a completely different approach from everything else we have seen.
The model starts with a random noise image and runs 20 denoising steps.
At each step it asks: "given my text prompt, which parts of this noisy image
are in the right direction?" After 20 steps the noise becomes a coherent image.
With fp16 precision on the T4, this takes about 20 to 30 seconds.

**Expected output:**
A 512x512 pixel image that matches the prompt.
For "a photo of a mountain lake at sunset, highly detailed":
a realistic landscape with warm orange and pink light, water reflections, mountains in the background.

**Explain the output:**
- The image did not exist before. It is not copied from any training image.
  The model learned the visual patterns of mountain lakes and generates a new one each time.
- Run it again with the same prompt and you get a completely different image.
- The quality is good for a base model. Larger models and more inference steps give better results.

**Good prompts to suggest:**
```
"a futuristic city at night with neon lights, cyberpunk style"
"a watercolor painting of a cat sitting on a windowsill"
"a photo of fresh pasta on a wooden table, food photography"
"an oil painting of a forest in autumn, warm colors, impressionist style"
```

**What makes a good prompt:**
- Concrete nouns ("mountain lake", "neon lights") work better than abstract ideas ("happiness", "justice").
- Style words help: "oil painting", "watercolor", "photorealistic", "digital art".
- More detail usually helps: "golden hour lighting", "shallow depth of field", "highly detailed".

---

## General Notes for Part 2

The main message of Part 2:
all these models follow the same basic idea: encode the input into vectors,
then decode those vectors into output.
The input and output can be text, image, or audio in any combination.

When students ask why quality is not perfect:
- GPT-2 is from 2019. Modern models are 100 to 1000 times larger.
- T5-base has 220M parameters. GPT-4 has hundreds of billions.
- We use small models because they run free on Colab.
  They are enough to understand the concepts.

When students ask if they can use these in a real project:
- For demos and prototypes: yes, these models work well.
- For production: use larger models via API (OpenAI, Groq, Google Gemini)
  or fine-tune a smaller model on your specific data.
