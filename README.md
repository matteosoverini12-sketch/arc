<div align="center">

# ◈ Arc

### The data science language that gets out of your way

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://python.org)
[![License MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS%20%7C%20Windows-lightgrey.svg)]()
[![Built on Arch](https://img.shields.io/badge/built%20on-Arch%20Linux-1793D1?logo=arch-linux)](https://archlinux.org)

**Arc transpiles to Python. Runs anywhere Python runs. Does in 1 line what Python does in 20.**

[Install](#install) · [Quick Start](#quick-start) · [Syntax](#syntax) · [Examples](#examples) · [AI & Training](#ai--llm) · [Build from source](#build-from-source)

</div>

---

## What is Arc?

Arc is a high-level language that transpiles to Python. It's designed for data science, machine learning, and AI workflows — the things you do every day but that require too much boilerplate.

You write `.arc` files. Arc compiles them to `.py` and runs them with your system Python. All your existing libraries (numpy, pandas, sklearn, torch...) work exactly as before.

```arc
load("sales.csv")
[explore] sales
[clean]   sales
[split]   sales [train] 0.8 [target] "churn"
[train]   model [on] X_train [target] y_train [as] forest
[score]   model [on] X_test [target] y_test
[ai] sales [summarize]
```

That's a full ML pipeline. In 7 lines.

---

## Install

**Download the binary (recommended)**
```bash
# Linux / Arch
curl -Lo arc https://github.com/matteosoverini12-sketch/arc/releases/tag/arc_v0.3.0
chmod +x arc
sudo mv arc /usr/local/bin/arc
```

**Or run from source**
```bash
git clone https://github.com/matteosoverini12-sketch/arc
cd arc
python arc.py yourfile.arc
```

Arc has **zero Python dependencies** — the transpiler is pure stdlib. Dependencies like numpy and pandas are installed automatically on first run if missing.

---

## Quick Start

Create `hello.arc`:
```arc
[say]("Hello from Arc!")
name = [ask]("Your name: ")
[say](f"Welcome, {name}!")
```

Run it:
```bash
arc hello.arc
```

---

## Python and Arc side by side

| Task | Python | Arc |
|------|--------|-----|
| Load CSV | `df = pd.read_csv("data.csv")` | `load("data.csv")` |
| Explore dataset | 10+ lines of print statements | `[explore] df` |
| Clean nulls + duplicates | 6+ lines | `[clean] df` |
| Train a model | 15+ lines | `[train] m [on] X [target] y [as] forest` |
| Call an AI | 10+ lines (OpenAI SDK) | `[ai] "your prompt"` |
| Fine-tune a model | 50+ lines | `[aitrain] m [from] "llama3" [data] "data.jsonl"` |
| Benchmark code | 5+ lines | `[benchmark] "label":` / `[end_benchmark]` |

---

## Syntax

Arc uses `[keyword]` constructs. Anything Arc doesn't recognize is passed through as raw Python — so you can always drop down to Python when you need to.

### Data I/O
```arc
load("file.csv")                    ## → DataFrame named after the file
load("a.csv", "b.xlsx")             ## → multiple DataFrames
[save] df [to] "output.csv"         ## .csv .xlsx .json .parquet
```

### Exploration
```arc
[explore] df                        ## shape, types, nulls, memory, preview
[clean]   df                        ## drop duplicates, fill nulls, infer types
[insight] df                        ## stats, correlations, outliers (Z-score)
```

### Plotting
```arc
[plot] df                           ## auto histogram of numeric columns
[plot] df [x] "col" [y] "col2"      ## scatter or bar (auto-detected)
[plot] df [x] "col" [as] hist       ## force chart type
[plot] df [as] heatmap              ## correlation heatmap
[plot] df [x] "col" [by] "group" [title] "My Chart"
```
Supported types: `hist` `bar` `line` `scatter` `box` `violin` `heatmap` `pie` `area` `kde`

### Machine Learning
```arc
## Split
[split] df [train] 0.8 [target] "label"
## → X_train, X_test, y_train, y_test

## Train
[train] mymodel [on] X_train [target] y_train [as] forest
## kinds: linear logistic ridge lasso forest boost knn svm kmeans neural auto

## Evaluate
score = [score] mymodel [on] X_test [target] y_test

## Predict
predictions = [predict] mymodel [on] X_test

## Tune hyperparameters
[tune] mymodel [on] X_train [target] y_train [trials] 30

## Preprocessing
df = [encode] df [col] "category"
df = [scale]  df
pcs = [pca]   df [components] 3
[feature] mymodel [on] df [target] "label"
```

### Validation
```arc
[expect] df["age"]   [between] 0 [and] 120
[expect] df["email"] [not_null]
[expect] df["id"]    [unique]
[expect] df["email"] [matches] ".*@.*"
[expect] df["age"]   [type] "int"
[expect] df          [rows] > 1000
[validation_report]
```

### Pipelines
```arc
[pipeline] preprocess [on] df:
    [clean] df
    [filter] df [where] df["age"] > 0
    [encode] df [col] "region"
    [scale]  df
[end]

result = [run] preprocess [on] raw_data
```

### Debug
```arc
[debug] myvar               ## type, shape, memory, preview
[watch] myvar               ## track changes over time
[trace]                     ## line-by-line execution trace
[benchmark] "label":        ## start timer
[end_benchmark]             ## stop + print ms
[assert] x > 0 "x must be positive"
[profile] myfunc            ## wrap with cProfile
[log] "message" [level] warning
```

Run any file with auto-debug on every variable:
```bash
arc debug myfile.arc
```

### Control flow
```arc
[if] x > 0:
    [say]("positive")
[elif] x == 0:
    [say]("zero")
[else]:
    [say]("negative")

[for] i in range(10):
    [say](i)

[foreach] row in df.itertuples():
    [say](row)

[while] running [and] count < 100:
    count = count + 1

[safe]:
    result = risky_operation()
[except] ValueError:
    [say]("caught!")
```

### Functions & Classes
```arc
[function] greet(name):
    [return] f"Hello, {name}!"

[class] Point(x, y):
    [method] distance():
        [return] (self.x**2 + self.y**2) ** 0.5
```

### Logic & misc
```arc
[and]  [or]  [not]          ## inline logical operators
[say]("hello")              ## print
name = [ask]("Name: ")      ## input
[use] sklearn               ## import
[parallel](4) myfunc[args]  ## ThreadPoolExecutor
```

---

## AI / LLM

Arc auto-detects which AI backend to use. No config needed if Ollama is running.

**Priority:** OpenAI → Anthropic → Ollama local

```arc
## Auto-detect backend
[ai] "Explain this dataset in simple terms"
result = [ai] "Summarize the key findings"

## Force a specific model — Arc routes automatically
[ai] "prompt" [model] "llama3"          ## → Ollama (free, local)
[ai] "prompt" [model] "mistral"         ## → Ollama
[ai] "prompt" [model] "deepseek-r1"     ## → Ollama
[ai] "prompt" [model] "gpt-4o"          ## → OpenAI
[ai] "prompt" [model] "claude-sonnet-4" ## → Anthropic

## Structured output
data = [ai] "List top 5 features" [schema] "json"

## Summarize a DataFrame
[ai] df [summarize]

## Embed a text column
df = [ai] df [col] "description" [embed]
```

**Setup:**
```bash
# Free, local (recommended)
# Install from https://ollama.com, then:
ollama pull llama3

# OpenAI
export OPENAI_API_KEY=sk-...

# Anthropic
export ANTHROPIC_API_KEY=sk-ant-...
```

---

## AI Training

Train or fine-tune models directly from Arc. Arc picks the best backend automatically.

### Simple (Arc decides everything)
```arc
## Ollama model → tries LoRA, falls back to Modelfile
[aitrain] mymodel [from] "llama3" [data] "data.jsonl"

## OpenAI fine-tuning (recognized by gpt-* prefix)
[aitrain] mymodel [from] "gpt-4o-mini" [data] "data.jsonl"

## Train from scratch with PyTorch
[aitrain] mymodel [from] scratch [data] "data.jsonl"
```

### Advanced (full control)
```arc
[aitrain] mymodel [from] "llama3" [data] "data.jsonl" [lora]
          [epochs] 5 [lr] 0.0001 [batch] 4 [rank] 32
          [output] "models/mymodel/"

## With custom system prompt
[aitrain] chef [from] "llama3" [data] "recipes.jsonl"
          [system] "You are an expert Italian cuisine assistant."
```

### Data format (JSONL)
```jsonl
{"prompt": "What is machine learning?", "response": "Machine learning is..."}
{"messages": [{"role": "user", "content": "..."}, {"role": "assistant", "content": "..."}]}
```

### Backends
| Backend | Command | Requires | Notes |
|---------|---------|----------|-------|
| `ollama` | `[from] "llama3"` | Ollama running | Modelfile, zero GPU |
| `lora` | `[lora]` flag | `peft` + `transformers` | Real fine-tuning, GPU recommended |
| `openai` | `[from] "gpt-4o-mini"` | `OPENAI_API_KEY` | Async job |
| `pytorch` | `[from] scratch` | `torch` | Train from zero |

### After training — use it like any other model
```arc
answer = [ai] "What is the best pasta?" [model] "chef"
[say](answer)

## Check OpenAI async job status
[aitrain_status] mymodel
```

---

## Full example

```arc
## Load and validate
load("customers.csv")
[expect] customers["age"]   [between] 0 [and] 120
[expect] customers["email"] [not_null]
[validation_report]

## Explore and clean
[explore] customers
[insight] customers
customers = [clean] customers

## Pipeline
[pipeline] prep [on] df:
    [filter] df [where] df["age"] > 18
    [encode] df [col] "country"
    [scale]  df
[end]

ready = [run] prep [on] customers

## Train
[split] ready [train] 0.8 [target] "churned"
[train] clf [on] X_train [target] y_train [as] forest
[tune]  clf [on] X_train [target] y_train [trials] 20
[score] clf [on] X_test  [target] y_test
[feature] clf [on] ready [target] "churned"

## Visualize
[plot] ready [as] heatmap
[plot] ready [x] "age" [y] "spend" [by] "churned" [as] scatter

## AI insight
[ai] ready [summarize]
insight = [ai] "The churn model got 89% accuracy. Top 3 business actions?"
[say](insight)

## Save
[save] ready [to] "cleaned_customers.csv"
```

---

## Build from source

Arc is a single file with zero dependencies. Compile to a native binary with Nuitka:

```bash
# Install Nuitka
pip install nuitka

# Compile
python -m nuitka \
  --onefile \
  --standalone \
  --python-flag=no_site \
  --python-flag=no_warnings \
  --enable-plugin=anti-bloat \
  --include-module=warnings \
  --include-module=logging \
  --include-module=urllib.request \
  --include-module=concurrent.futures \
  --include-module=importlib.util \
  --nofollow-import-to=numpy \
  --nofollow-import-to=pandas \
  --nofollow-import-to=sklearn \
  --nofollow-import-to=matplotlib \
  --nofollow-import-to=seaborn \
  --nofollow-import-to=torch \
  --nofollow-import-to=transformers \
  --nofollow-import-to=openai \
  --nofollow-import-to=anthropic \
  --nofollow-import-to=scipy \
  --assume-yes-for-downloads \
  --output-filename=arc \
  --output-dir=dist \
  arc.py

sudo cp dist/arc /usr/local/bin/arc
```

The resulting binary is ~10-15 MB. No Python required on the target machine — Arc finds and uses the system Python automatically.

---

## CLI reference

```bash
arc <file.arc>                  # transpile + run
arc build <file.arc>            # transpile only → .py
arc build <file.arc> -o out.py  # transpile to specific file
arc debug <file.arc>            # run with auto-debug on every variable
arc repl                        # interactive REPL
arc help                        # full syntax reference
```

---

## Why Arc?

Arc was born on Arch Linux (hence the name). The philosophy is simple:

> Data science should be about insights, not boilerplate.

Arc doesn't replace Python. It sits on top of it, handles the repetitive parts, and gets out of the way. When you need raw Python, write raw Python — Arc passes it through unchanged.

---

## License

MIT — do whatever you want with it.

---

<div align="center">
Made with ◈ on Arch Linux
</div>
