# Runnables in LangChain

## What are Runnables?
A **Runnable** is the fundamental building block in LangChain that represents a unit of work. Every component (LLM, Prompt, Output Parser, Chain, Retriever, etc.) is a Runnable.

Runnables can be:
- Executed individually
- Chained together
- Run in parallel
- Stream outputs
- Process batches of inputs

---

# Why use Runnables?

- Standard interface for all LangChain components
- Easy to combine multiple operations
- Supports synchronous and asynchronous execution
- Supports streaming
- Supports batch processing
- Makes building pipelines simple

---

# Runnable Methods

## 1. invoke()

Executes the runnable on a single input.

```python
result = chain.invoke({"topic": "AI"})
```

Returns the complete output after execution.

---

## 2. batch()

Runs the same runnable on multiple inputs.

```python
results = chain.batch([
    {"topic": "AI"},
    {"topic": "ML"},
    {"topic": "NLP"}
])
```

Useful for processing many requests efficiently.

---

## 3. stream()

Returns output as it is generated.

```python
for chunk in chain.stream({"topic": "AI"}):
    print(chunk)
```

Useful for:
- Chatbots
- Live responses
- Token-by-token generation

---

## 4. ainvoke()

Asynchronous version of `invoke()`.

```python
result = await chain.ainvoke({"topic":"AI"})
```

---

## 5. abatch()

Asynchronous version of `batch()`.

```python
results = await chain.abatch(inputs)
```

---

## 6. astream()

Asynchronous streaming.

```python
async for chunk in chain.astream(input):
    print(chunk)
```

---

# Runnable Sequence

Runs components **one after another**.

Example:

```text
User Input
     ↓
Prompt
     ↓
LLM
     ↓
Output Parser
```

Example:

```python
chain = prompt | model | parser
```

Output of one becomes input to the next.

---

# Runnable Parallel

Runs multiple runnables simultaneously.

```python
from langchain_core.runnables import RunnableParallel
```

Example:

```python
parallel = RunnableParallel(
    summary=summary_chain,
    sentiment=sentiment_chain
)

result = parallel.invoke(text)
```

Output:

```python
{
   "summary": "...",
   "sentiment": "Positive"
}
```

Useful when tasks are independent.

---

# RunnablePassthrough

Passes the original input unchanged.

```python
from langchain_core.runnables import RunnablePassthrough
```

Example:

```python
chain = RunnablePassthrough()
```

Useful when the original input is needed later in the pipeline.

Example:

```python
RunnableParallel(
    original=RunnablePassthrough(),
    summary=summary_chain
)
```

Output:

```python
{
   "original": "...",
   "summary": "..."
}
```

---

# RunnableLambda

Wraps a normal Python function into a Runnable.

```python
from langchain_core.runnables import RunnableLambda
```

Example:

```python
def uppercase(text):
    return text.upper()

chain = RunnableLambda(uppercase)
```

Now it behaves like any other Runnable.

---

# RunnableBranch

Executes different chains based on a condition.

Example:

```text
If sentiment == Positive
        ↓
 Positive Chain

Else
        ↓
Negative Chain
```

Useful for conditional workflows.

---

# RunnableAssign

Adds new fields to existing data without replacing it.

Example:

Input

```python
{
    "text":"LangChain is awesome"
}
```

After assign

```python
{
    "text":"LangChain is awesome",
    "summary":"..."
}
```

Useful for enriching data.

---

# Pipe Operator (|)

The `|` operator connects runnables together.

Example:

```python
chain = prompt | model | parser
```

Equivalent to

```text
Prompt
   ↓
LLM
   ↓
Parser
```

---

# Advantages of Runnables

- Simple pipeline creation
- Reusable components
- Supports streaming
- Supports async execution
- Parallel execution
- Batch processing
- Easy debugging
- Better performance

---

# Common Runnable Workflow

```text
User Input
      │
      ▼
Prompt Template
      │
      ▼
LLM
      │
      ▼
Output Parser
      │
      ▼
Final Output
```

---

# Quick Revision

| Runnable | Purpose |
|-----------|---------|
| `invoke()` | Run one input |
| `batch()` | Run multiple inputs |
| `stream()` | Stream output |
| `ainvoke()` | Async invoke |
| `abatch()` | Async batch |
| `astream()` | Async stream |
| `RunnableSequence` | Execute sequentially |
| `RunnableParallel` | Execute simultaneously |
| `RunnablePassthrough` | Keep original input |
| `RunnableLambda` | Wrap Python function |
| `RunnableBranch` | Conditional execution |
| `RunnableAssign` | Add new fields to output |
| `|` | Connect runnables |