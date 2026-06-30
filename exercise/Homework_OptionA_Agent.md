# Homework Option A: Extend Your Agent

**Time:** up to 60 minutes
**Requires:** Groq API key (the same one used in Part 3), no GPU needed

---

## Goal

In Part 3 you built an agent with two tools: `calculate` and `lookup`.
In this assignment you will add two NEW tools of your own choice
and test that your agent uses them correctly.

---

## Step 1: Choose two tools

Pick two from this list, or come up with your own:

- **Unit converter**: convert between km and miles, kg and pounds, or similar
- **Word/character counter**: count words or characters in a piece of text
- **Date and time tool**: return the current date or day of the week
- **Prime number checker**: check if a number is prime
- **Temperature converter**: Celsius to Fahrenheit and back
- **Simple text formatter**: convert text to uppercase, lowercase, or reverse it

Pick tools that are simple to implement in plain Python (a few lines each).
The goal is to practice the tool-calling pattern, not to write complex logic.

---

## Step 2: Implement your tools

For each tool you need three things, following the same pattern as `convert_units`
in Part 3, Exercise 2:

1. A JSON schema describing the tool (name, description, parameters)
2. A Python function that implements the logic
3. A line in the agent's tool-routing code that calls your function when needed

Use the starter notebook `Homework_OptionA_Agent.ipynb` as your base.
It already contains the working agent code from Part 3.
Add your two tools where marked with `# TODO`.

---

## Step 3: Test your agent

Write 4 to 6 questions that should trigger your new tools.
Include at least:
- One question that clearly needs Tool 1
- One question that clearly needs Tool 2
- One question that needs no tool at all (to confirm the agent does not call tools unnecessarily)

Example pattern (adapt to your chosen tools):
```python
test_questions = [
    "How many miles is 50 km?",              # needs your converter tool
    "How many words are in this sentence?",   # needs your counter tool
    "What is the capital of Italy?",          # needs no tool
]
```

Run your agent on each question and check the output.

---

## Step 4: Write a short report

At the end of your notebook, add a markdown cell answering these questions:

1. What two tools did you build?
2. For each test question: did the agent pick the correct tool? Was the result correct?
3. Did you find any question where the agent picked the wrong tool, or no tool
   when it should have? If so, what do you think went wrong?

---

## Self-Check

Before you finish, confirm:

- [ ] Both tools have a clear JSON schema with a description
- [ ] Both tools are implemented as working Python functions
- [ ] You tested at least 4 questions, including one with no tool needed
- [ ] You wrote a short report explaining what happened

If something does not work, that is fine. Write down what you tried
and what error or unexpected result you got. Debugging tool-calling agents
is part of the skill we are practicing today.
