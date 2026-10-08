## Strict Design Rules for Notebook Creation

All Jupyter Notebooks created or modified in this repository must strictly adhere to the following design rules:

### 1 Succinct and Elegant Titles
* Notebook titles must be succinct, elegant, and highly professional.
* You may add a subtitle for clarity and precision.
* Treat the notebook title and subtitle as if the notebook was the starting point for a published academic paper.

### 2 Succinct Abstract and Methodology-Focused Introductions
* The notebook abstract (executive summary) must succinctly represent what the notebook does, its inputs, analytical methodology, and exported dataset payloads/artifacts for easily understanding dependencies when reusing results or methodology.
* No general project context is required in the abstract—focus directly on the core hypothesis, input dependencies, analytical pipeline, and generated outputs.
* Provide clear, friendly context for readers while pushing complex LaTeX and heavy mathematical rigor to the deeper sections of the notebook.

### 3 Detailed Cell-Level Documentation
* Every cell (both markdown and code) must have a clear title and a paragraph describing its methodology, implementation details, and what the code is doing.
* Include LaTeX where it helps provide clarity.

### 4 High Precision in Parameter Documentation
* Be exceptionally precise on all parameters.
* This includes documenting the model architecture, input/output dimensions, dataset formation/splits, and the training process.
* Precise parameter documentation ensures experiments can be easily modified and verified by external researchers.

### 5 Visual Examples and Computation Validation
* Provide concrete examples to show how computations are performed and how datasets are constructed.
* Use logs, inline printed outputs, images, and videos where appropriate. This helps readers instantly verify the methodology and results.

### 6 Consistent and Transparent Metrics
* Be clear about the selected testing metrics and how they relate to the results.
* The metrics must be highly consistent with the notebook's motivation and accompanied by an explanation of why they were selected and how they facilitate interpretation.
* Training, validation, and testing metrics must be consistent with each other.
* Include training details, wall-clock execution times, and step-by-step logs to track these metrics.
* Include a justification for the selected model architecture in the relevant section.
* Use plenty of charts to show the results visually.

### 6.1 Primary Environment & Storage Setup (Google Colab & Google Drive)
* All Jupyter Notebooks created or modified in this repository run in **Google Colab** as their primary execution environment.
* Notebooks must configure **Google Drive (`/content/drive/MyDrive/`) as the primary storage setup** for model checkpoints (`/content/drive/MyDrive/graph_checkpoints`) and dataset payloads (`/content/drive/MyDrive/graph_data`).
* Include an explicit `try ... except ImportError:` block attempting to mount Google Drive (`from google.colab import drive; drive.mount('/content/drive')`) with a robust local path fallback search hierarchy (`src/static/data/`, `data/`, `graphs/data/`) to guarantee seamless execution across both Google Colab and local environments.

### 7 Preservation of Historical Notebooks and Results
* **Never overwrite, modify, or destroy previous notebook files or results** when introducing a new experiment, solver, or interpretability technique.
* Always create a new, dedicated tutorial notebook (e.g., `5.jspace_causal_steering_and_attention_tutorial.ipynb`) with an incremental numerical index to preserve past findings and maintain a clean research progression.

### 8 Self-Contained Inline Figure Rendering & Flexible Chart Distribution
* All visualization plotting cells must execute `plt.show()` (or display the figure) directly within the cell output in addition to calling `plt.savefig()`.
* This guarantees that charts are rendered inline inside the notebook itself when viewed on GitHub, Jupyter, or Google Colab, making the notebook completely self-contained.
* Visualization charts may be presented across multiple code cells directly following their respective analytical sections rather than being restricted to a single grouped multi-panel figure at the end of the notebook.

### 9 Summary Contribution to Development Log
* Each notebook contributes to a larger research project.
* After completing a notebook, include a complete summary in the main project `DEVELOPMENT LOG.md` so that the results provide context for future experiments and can be easily consolidated into a formal research paper.

### 10 Notebook Execution Constraint
* Do not run the notebook or you will encounter issues.

### 11 Guidelines for LLM Engagement, API Configuration, Retry Mechanics, and Caching
* **Target Model:** Unless explicitly instructed otherwise, use **Gemini 3.1 Flash-Lite** (`gemini-3.1-flash-lite`) via `google-genai` SDK for qualitative synthesis and feature extraction.
* **Secret & API Key Configuration:** Attempt to read API credentials first from Google Colab User Data secrets (`from google.colab import userdata; userdata.get("GOOGLE_API_KEY")`) with fallback to standard environment variables (`os.environ.get("GOOGLE_API_KEY")`).
* **Retry Mechanics & Error Handling:**
  - Implement a retry loop attempting LLM requests up to **a maximum of 3 times** upon API or network failure (with exponential backoff, e.g. `time.sleep(2 * attempt)`).
  - Do **NOT** populate outputs with synthetic or placeholder text if an LLM request fails. Instead, throw an explicit exception (e.g., `RuntimeError`) detailing the failure after exhausting all retries.
* **Persistent Generation Caching:**
  - When labeling a dataset, maintain a persistent disk cache (e.g., `llm_generation_cache.json`) exported across primary Google Drive (`/content/drive/MyDrive/persuade_data`) and local fallback directories (`data/`, `analysis/`). Important: each notebook has its own cache!
  - Provide an optional configuration to check the cache prior to invoking the LLM API so that if notebook execution is interrupted and resumed, previous LLM generations are restored without re-querying the API.
