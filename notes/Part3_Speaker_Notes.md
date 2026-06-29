# Part 3: Speaker Notes and Exercise Examples

These notes are for the presenter.
Each section matches a code cell in the notebook.
Read the "Say this" part before running the cell.
Read the "Expected output" part to explain the result after running.

---

## Setup

### Cell: pip install

**Say this:**
Run this first. We install four packages:
openai for the API client, langchain-groq to connect LangChain to Groq,
langgraph for the agent framework, and langchain-core for shared types.
This takes about 30 seconds.

---

### Cell: Groq API key

**Say this:**
Before we run any code, everyone needs a free Groq API key.
Follow the steps in the markdown cell above. It takes about two minutes.
The recommended way is to add it to Colab Secrets (the key icon in the left sidebar).
This way the key is never visible in the notebook and you can share the notebook safely.

**Expected output:**
```
API key loaded from Colab Secrets.
```

---

## Part 3.1: Connecting to an LLM via API

### Cell: First API call

**Say this:**
In Parts 1 and 2, we loaded models directly into the GPU.
For large models that is not possible, and for production you want one model
serving many users at the same time.
The solution is an inference server: the model runs in one place and we call it over the network.

The OpenAI API format is the standard today.
Groq, Ollama, vLLM, and NVIDIA NIM all use the same format.
We point the client at Groq by setting base_url.
The model name is the only other thing that changes between providers.

**Expected output:**
Something like:
```
A transformer model is a type of deep learning architecture that uses
self-attention mechanisms to process sequential data in parallel.
It was introduced in the 2017 paper "Attention Is All You Need" and
has become the foundation for most modern language models.
```

**Explain the output:**
- Notice the response came back in about 1 second. Groq uses custom hardware (LPUs) for fast inference.
- The response is a plain string. No tensors, no GPU, no model loaded locally.

---

### Cell: Streaming

**Say this:**
By default, the API waits until the full response is ready, then sends it all at once.
With streaming=True, we get tokens as they are generated.
This is exactly how ChatGPT shows text word by word.

**Expected output:**
```
Streamed response:
An encoder reads the full input sequence and compresses it into a context vector...
```
The text appears word by word, not all at once.

**Explain the output:**
- Each chunk contains one or a few tokens.
- `end=""` and `flush=True` print without newline and immediately, giving the streaming effect.
- In a web application you would send each chunk to the browser as a server-sent event.

---

### Cell: Multi-turn conversation

**Say this:**
The model has no memory between calls. We have to send the full conversation history every time.
This is why chatbots store the message list and grow it with each turn.
Watch how the third answer references things from the first two turns.

**Expected output:**
```
User     : What is a large language model?
Assistant: A large language model (LLM) is a neural network trained on massive amounts of text...

User     : How is it different from a traditional text classifier?
Assistant: A traditional classifier maps text to a fixed set of labels...
           Unlike classifiers, LLMs can generate new text...

User     : Give one real-world example where you would use each.
Assistant: For a traditional classifier: spam detection in email...
           For an LLM: a customer support chatbot that handles open-ended questions...
```

**Explain the output:**
- The third answer refers to "each" meaning LLM vs. classifier. The model knows this from the history.
- The history list grows with every turn. For very long conversations this can hit the context limit.
  That is why real applications summarize older parts of the conversation.

---

### Cell: Ollama comparison

**Say this:**
This cell only shows comments. No code runs.
The key message is that switching from Groq to a local Ollama instance
requires changing exactly two lines: base_url and api_key.
Everything else, streaming, multi-turn, tool use, stays identical.
This is why the OpenAI API standard matters so much in practice.

---

## Part 3.2: Structured JSON Output

### Cell: JSON extraction

**Say this:**
The model normally returns free text. But in code we often need structured data.
We solve this with two things: clear field names in the prompt,
and response_format json_object which forces the model to always return valid JSON.

**Expected output:**
Something like:
```json
{
  "topic": "AI research",
  "entities": ["MIT", "Dr. Sarah Chen", "Nature"],
  "sentiment": "positive",
  "key_fact": "A new training method reduces fine-tuning costs by 60%."
}
```

**Explain the output:**
- The JSON is always valid because response_format enforces it.
  Without this flag the model sometimes adds text before or after the JSON.
- "entities" is a list, not a string. The model followed the field type from the description.
- Try asking for a field that is not in the text, for example "author_age".
  The model will return null or an empty string, not crash.

---

### Exercise 1: Structured Extraction

**Say this before running:**
Now try your own text and your own fields.
The key skill here is writing clear field descriptions in the prompt.
The better you describe what you want, the more reliable the output.

**Example texts to suggest to students:**

Sports news:
```
"Novak Djokovic won his 25th Grand Slam title at Wimbledon yesterday,
defeating Carlos Alcaraz in five sets. The match lasted four hours."
```
Good fields to extract: winner, tournament, opponent, result, duration

Product review:
```
"I bought the Sony WH-1000XM5 headphones last week. The noise cancellation
is incredible but the price at 380 euros feels high. Battery lasts 30 hours."
```
Good fields to extract: product, brand, price, pros, cons, rating (model infers this)

What happens when a field is missing:
Ask for "stock_price" from a sports article.
Expected: the model returns null or "not mentioned".
This is the correct behavior. Discuss: what would you do in code when a field is null?

---

## Part 3.3: Tool Use and Function Calling

### Cell: Define tools

**Say this:**
A tool definition is a JSON schema that tells the model:
- what the function does (description)
- what arguments it takes (parameters)
- which arguments are required

The model reads these schemas and learns when to call each tool.
We implement the actual functions in Python. The model never sees the Python code,
only the schema description.

**Expected output:**
```
Tools defined: ['calculate', 'lookup']
```

---

### Cell: run_agent

**Say this:**
This is the full tool use loop. Let us walk through what happens for the first question.

1. We send "What is 2 to the power of 15?" plus the tool schemas
2. The model decides to call calculate with expression "2**15"
3. We run the Python eval and get "32768"
4. We send the result back to the model
5. The model writes the final answer in natural language

**Expected output:**
```
Question : What is 2 to the power of 15?
  [Tool] calculate({'expression': '2**15'}) -> 32768
Answer   : 2 to the power of 15 is 32768.

Question : What does 'tokenizer' mean in machine learning?
  [Tool] lookup({'term': 'tokenizer'}) -> A component that splits text into tokens...
Answer   : A tokenizer is a component that splits text into tokens...

Question : If a model has 7 billion parameters and each takes 2 bytes in fp16, how many GB is that?
  [Tool] calculate({'expression': '7_000_000_000 * 2 / 1_000_000_000'}) -> 14.0
Answer   : A 7 billion parameter model in fp16 requires 14 GB of memory.

Question : What is RAG and when would you use it?
  [Tool] lookup({'term': 'rag'}) -> Retrieval-Augmented Generation...
Answer   : RAG stands for Retrieval-Augmented Generation...
```

**Explain the output:**
- For the math question, the model passes a Python expression. We eval it safely.
- For the memory question, the model constructs the expression itself from the numbers in the question.
  We never told it how to calculate GPU memory. It reasoned about which tool to use and how.
- For the RAG question, the model chose lookup not calculate. It understood the question type.

Good discussion question:
"What would happen if we asked a question that does not need any tool?"
Answer: the model returns msg.content directly without calling any tool.
Try: "What country is Paris in?" and the model answers without a tool call.

---

### Exercise 2: Add a New Tool

**Say this before running:**
The convert_units tool is already written. Run the cell and test it.
Then try adding a new word to the GLOSSARY dictionary.

**Expected output for the two test questions:**
```
Question : How many GB is 4096 MB?
  [Tool] convert_units({'value': 4096, 'conversion': 'mb_to_gb'}) -> 4096 MB = 4.000 GB
Answer   : 4096 MB is exactly 4 GB.

Question : What is 37 degrees Celsius in Fahrenheit?
  [Tool] convert_units({'value': 37, 'conversion': 'celsius_to_fahrenheit'}) -> 37 C = 98.6 F
Answer   : 37 degrees Celsius is 98.6 degrees Fahrenheit, which is normal human body temperature.
```

**Good words to add to GLOSSARY:**
```python
"agent"          : "A system where an LLM can take actions, observe results, and repeat until a goal is reached.",
"prompt"         : "The input text given to a language model to guide its response.",
"vector database": "A database that stores and searches data by semantic similarity using embedding vectors.",
"context window" : "The maximum number of tokens a model can process in a single call.",
```

Then test with:
```
"What is an agent in AI?"
"What is a context window?"
```

---

## Part 3.4: The ReAct Loop

### Cell: react_agent

**Say this:**
In Part 3.3 the model used grammar enforcement to call tools in JSON format.
ReAct is a different approach: we ask the model to write its reasoning as plain text.
It writes Thought, Action, Input, and we parse that text.
The advantage is that the reasoning is visible and easy to debug.
The disadvantage is that it depends on the model following the text format exactly.

max_steps=5 means the loop runs at most 5 times.
The loop stops earlier if the model writes "Answer:".

**Expected output for the default question:**
```
Question: What is the square root of 2025, and what does fine-tuning mean?

--- Step 1 ---
Thought: I need to find the square root of 2025 and explain fine-tuning.
         The square root of 2025 is 45 (since 45*45 = 2025).
         For fine-tuning, let me look it up.
Action: lookup
Input: fine-tuning

Observation: Training a pre-trained model further on a smaller task-specific dataset.

--- Step 2 ---
Thought: I now have both answers.
Answer: The square root of 2025 is 45. Fine-tuning means training a pre-trained
        model further on a smaller task-specific dataset to adapt it to a new task.
```

**Explain the output:**
- The model answered in 2 steps here because it knew sqrt(2025)=45 from training
  but still looked up fine-tuning in the glossary.
- max_steps=5 is the maximum, not the minimum.
  The loop always stops as soon as the model writes "Answer:".
- The reasoning in Thought is visible, which helps you understand why the model made each decision.

**Examples that show multiple steps (show these to demonstrate the loop):**

Two separate tool calls:
```python
react_agent("First calculate 144 squared, then tell me what RAG means.")
```
Expected: Step 1 calls calculate(144**2) -> 20736, Step 2 calls lookup(rag), Step 3 gives the answer.

Two sequential calculations:
```python
react_agent("What is 2 to the power of 10 plus the square root of 256?")
```
Expected: Step 1 calculates 2**10=1024, Step 2 calculates math.sqrt(256)=16,
Step 3 calculates 1024+16=1040, Step 4 gives the answer.
(The model may also combine this into fewer steps depending on its reasoning.)

A question that needs no tools (1 step only):
```python
react_agent("What is the capital of France?")
```
Expected: Step 1 directly writes Answer: Paris. No tool call needed.
This shows that the loop exits immediately when the model has the answer.

---

## Part 3.5: LangGraph

### Cell: Build graph

**Say this:**
Compare this code to the ReAct loop in Part 3.4.
In Part 3.4 we wrote: the while loop, the text parser, the tool router, the stop condition.
Here we define two nodes and two edges, and LangGraph handles the rest.

The graph has exactly two nodes:
- agent: calls the LLM
- tools: executes whichever tool the model requested

tools_condition is a built-in function that reads the model output and decides:
if there is a tool call, go to tools; if not, go to END.

**Expected output:**
```
LangGraph agent ready.
```

---

### Cell: Run graph

**Say this:**
We run the same question as in Part 3.4.
The answer should be equivalent even though the internal mechanism is different.
Look at the total message count at the end. It tells you how many steps the graph took.

**Expected output:**
```
Question: What is the square root of 2025, and what does fine-tuning mean?

Answer  : The square root of 2025 is 45. Fine-tuning refers to training
          a pre-trained model on a smaller, task-specific dataset...

Total messages in graph: 5
(human -> agent thought -> tool call -> tool result -> final answer)
```

**Explain the output:**
- 5 messages means: human question, agent response with tool call, tool result,
  possibly another agent response, final answer. The exact count depends on how many tools were called.
- The answer is equivalent to Part 3.4 but we wrote much less code.
- In a real application you would add more tools: web search, database lookup, file reading.
  Each new tool is just one more @tool function and one more node if needed.

**Side by side comparison to show students:**

Part 3.4 ReAct: wrote the loop, text parser, observation handler, stop condition = 30+ lines
Part 3.5 LangGraph: wrote tool definitions, agent node, two add_node calls, two add_edge calls = 15 lines
Same behavior, half the code, and LangGraph handles edge cases we did not think of.

---

## Mini Project: Content Generation Pipeline

### Cell: generate_content + loop

**Say this:**
This combines everything from Part 3.2 and brings it back to Part 2.
In Part 2 we generated image captions with ViT+GPT2.
Here we take those captions and use the LLM to create social media content.
This is a realistic two-step pipeline: vision model produces description,
language model produces content.

**Expected output:**
Something like:
```
Image 1: A happy golden Labrador sitting on green grass...
Tweet  : Meet the happiest pup on the block! This golden boy is living his best life
         in the great outdoors. Who else is smiling just looking at this? #GoldenRetriever #Dogs
Caption: Pure joy on four legs! This golden Labrador knows how to enjoy a sunny day in the park.

Image 2: A fluffy tabby cat sitting calmly on a white surface...
Tweet  : Purrfect Monday vibes from this regal tabby. Calm, composed, and absolutely adorable.
         #CatsOfInstagram #TabbyLife
Caption: Serenity now. This gorgeous tabby is the definition of cool, calm, and collected.

Image 3: The Eiffel Tower photographed from the ground...
Tweet  : La vie est belle! One of the world's most iconic landmarks on a perfect day.
         Tag someone you would visit Paris with! #Paris #EiffelTower #Travel
Caption: A timeless classic. The Eiffel Tower never fails to take your breath away.
```

**Explain the output:**
- Each image gets a tweet and a caption. The content is different for each image.
- The model followed our JSON schema: keys "tweet" and "caption".
- The hashtags are generated automatically based on the image content.
- This is how real content creation tools work: image in, social post out, no human writing needed.

Good discussion question:
"How would you extend this pipeline for a real application?"
Possible answers:
- Add a third LLM call to translate the posts to other languages
- Add a tool that posts directly to Twitter/Instagram via API
- Use a vision model to analyze real uploaded photos instead of hardcoded descriptions

---

## General Notes for Part 3

The main message of Part 3:
modern AI applications are not single model calls.
They are pipelines, loops, and systems where models reason, act, observe, and repeat.

When students ask about rate limits on the free tier:
- Groq free tier: 30 requests per minute, 30,000 tokens per minute
- If you hit the limit, you get a 429 error and the cell fails
- Solution: wait 10 seconds and run again, or reduce the number of test questions

When students ask about the Groq model names changing:
- Groq deprecates models frequently as better ones become available
- Always check console.groq.com/docs/models before a workshop for current names
- The code pattern never changes, only the model string

When students ask how to use this with their own project:
- Replace the GLOSSARY lookup with a database query or a web search
- Replace the calculate tool with whatever external service they need (weather API, stock prices, etc.)
- LangGraph scales from 2 nodes to 20+ nodes with the same pattern
