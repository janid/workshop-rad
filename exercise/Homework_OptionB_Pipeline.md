# Homework Option B: Build Your Own Content Pipeline

**Time:** up to 60 minutes
**Requires:** Groq API key (the same one used in Part 3), no GPU needed

---

## Goal

In Part 3 you built a content pipeline that took an image description
and generated a tweet and a caption from it.
In this assignment you will build a similar pipeline for a topic of your own choice.

---

## Step 1: Choose your topic

Pick one of these, or propose your own:

- **Recipes**: input a list of ingredients, output a recipe name and short description
- **Books**: input a book summary, output a one-line pitch and a target audience
- **Movies**: input a plot description, output a tagline and a genre classification
- **Products**: input a product description, output marketing copy and a price range guess
- **Events**: input an event description, output a short announcement and a hashtag

Your pipeline should take ONE input and produce AT LEAST TWO different outputs
using structured JSON, similar to the `tweet` and `caption` fields in Part 3.

---

## Step 2: Design your prompt

Write a clear prompt that:
- Explains the input
- Lists the exact output fields you want (with short descriptions)
- Asks for JSON output only

Use the starter notebook `Homework_OptionB_Pipeline.ipynb`.
It already contains the working `generate_content` pattern from Part 3.
Adjust the prompt and output fields for your chosen topic.

---

## Step 3: Run your pipeline on 3 examples

Create 3 different inputs for your topic and run them through your pipeline.

Example pattern (adapt to your topic):
```python
inputs = [
    "A creamy pasta dish with garlic, parmesan, and black pepper",
    "A spicy noodle soup with chicken and lime",
    "A simple breakfast with eggs, toast, and avocado",
]
```

For each input, print all the output fields your pipeline generated.

---

## Step 4: Write a short report

At the end of your notebook, add a markdown cell answering these questions:

1. What topic did you choose, and what output fields did you design?
2. Look at your 3 results: are the outputs consistent in quality?
   Is there one example that worked noticeably better or worse than the others?
3. If you changed one thing about your prompt to improve the results,
   what would it be?

---

## Self-Check

Before you finish, confirm:

- [ ] Your prompt clearly defines the input and at least 2 output fields
- [ ] You used `response_format={"type": "json_object"}` for structured output
- [ ] You tested 3 different inputs and printed the results
- [ ] You wrote a short report reflecting on the results

If your JSON output sometimes fails to parse, that is a common real issue.
Write down what happened and how you might handle it in a real application
(for example: retrying the request, or validating the fields before using them).
