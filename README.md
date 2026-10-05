# Multi-Agent Data Analyst

An interactive Streamlit application that turns a CSV dataset into an end-to-end
analysis workflow. Specialized agents profile the data, perform exploratory
analysis, train and verify machine-learning models, explain results with Gemini,
and generate a reusable Jupyter notebook.

**Live demo:** [multiagent-data-analyst.onrender.com](https://multiagent-data-analyst.onrender.com/)

## What it does

The application combines a Streamlit interface with MCP-style tools and an
agent-to-agent (A2A) message bus:

1. Upload a CSV dataset.
2. Inspect its shape, columns, types, missing values, and summary statistics.
3. Generate exploratory analysis, visualizations, correlations, and outlier
   information.
4. Select a target column and train an automatically configured classification
   or regression pipeline.
5. Review model metrics and run verification checks.
6. Ask Gemini to explain model results in plain language.
7. Assemble the analysis into a downloadable Jupyter notebook.
8. Inspect agent messages, persisted memory, and generated artifacts.

The project is designed to make a repeatable analysis workflow accessible to
beginners while keeping each stage modular and replaceable.

## Agent workflow

```text
CSV upload
    |
    v
Profiler Agent --> EDA Agent --> Model Agent --> Verifier Agent
       |              |              |               |
       +-------------- shared memory and A2A bus -----+
                                      |
                                      v
                         Notebook Synthesizer Agent
                                      |
                                      v
                             Gemini explanations
```

### Agents

- **Profiler Agent** — identifies column types, missing values, and basic data
  quality information.
- **EDA Agent** — creates summaries, correlations, distributions, and
  visualizations.
- **Model Agent** — determines the task type and builds a scikit-learn
  preprocessing and modeling pipeline.
- **Verifier Agent** — evaluates model output and assigns a quality assessment.
- **Notebook Synthesizer Agent** — combines the workflow outputs into a
  self-contained Jupyter notebook.
- **Gemini integration** — provides human-readable explanations and
  recommendations for model results.

Agents communicate through the A2A bus and persist selected results through the
memory tools, allowing later stages to consume earlier outputs.

## Application pages

The Streamlit application is available from the home page and its multi-page
navigation:

- **Home** — upload a CSV and access the basic file, dataset, and memory tools.
- **Dataset Explorer** — inspect the uploaded data.
- **EDA Dashboard** — run profiling and exploratory analysis.
- **AutoML** — select a target column, train a model directly or through the
  Model Agent, and view metrics.
- **Profiler** — run and inspect the Profiler Agent output.
- **Verifier** — validate model results and review the quality assessment.
- **Notebook Report** — generate and inspect the consolidated notebook.
- **A2A Dashboard** — inspect agent-to-agent communication and workflow state.

## Technology stack

| Area | Technologies |
| --- | --- |
| Interface | Streamlit |
| Language | Python |
| Data processing | pandas, NumPy |
| Machine learning | scikit-learn, joblib |
| Visualization | Matplotlib, Seaborn, Plotly |
| Generative AI | Google Gemini API |
| Notebooks | nbformat |
| Validation and statistics | jsonschema, statsmodels |
| Coordination | Custom MCP-style tools and A2A bus |
| Persistence | JSON-backed project and application memory |

## Requirements

- Python 3.10 or newer
- `pip`
- A Gemini API key for Gemini-powered explanations

All Python dependencies are listed in
[requirements.txt](requirements.txt).

## Installation

### Windows PowerShell

```powershell
git clone https://github.com/Uday0515/datapilot-agents.git
cd datapilot-agents

python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### macOS or Linux

```bash
git clone https://github.com/Uday0515/datapilot-agents.git
cd datapilot-agents

python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

## Configuration

Create a `.env` file in the repository root and add your Gemini API key:

```env
GEMINI_API_KEY=your_gemini_api_key
```

Keep `.env` out of version control. The AutoML page reads this value when
initializing Gemini. If you do not configure a key, the data exploration and
machine-learning features can still be used, but Gemini explanations will not
be available.

## Run the application

From the repository root, with the virtual environment activated:

```bash
streamlit run streamlit_app/app.py
```

Open the local URL shown by Streamlit, upload a CSV file from the home page, and
then use the pages in the sidebar to run each stage of the workflow.

### Optional command-line orchestrator

The repository also includes a Python orchestrator for exercising the agents
outside Streamlit:

```bash
python src/orchestrator.py
```

The orchestrator expects a `sample.csv` file in the `src` directory. For normal
interactive use, start with the Streamlit command above.

## Project structure

```text
datapilot-agents/
├── mcp/
│   └── manifest.json              # Available MCP-style tools
├── src/
│   ├── agents/                    # Profiler, EDA, model, verifier, notebook agents
│   ├── core/
│   │   └── a2a_bus.py             # Agent-to-agent message bus
│   ├── tools/                     # File, dataset, model, memory, and notebook tools
│   └── orchestrator.py            # Optional command-line workflow
├── streamlit_app/
│   ├── app.py                     # Home page and CSV upload
│   └── pages/                     # Streamlit workflow pages
├── streamlit_app_storage/
│   ├── memory/                    # Application memory
│   └── uploads/                   # Uploaded datasets
├── project_storage/               # Generated reports and project artifacts
├── memory/                        # Persistent agent memory
├── requirements.txt
└── README.md
```

Generated files such as uploaded datasets, plots, models, memory records, and
notebooks are stored in the project or application storage directories. Review
those directories before committing generated artifacts.

## Typical workflow

1. Start the application and upload a CSV file on the **Home** page.
2. Open **Dataset Explorer** to confirm that the data loaded correctly.
3. Run the **Profiler** and **EDA Dashboard** stages.
4. Open **AutoML**, choose a target column, and train a model.
5. Review the metrics and request a Gemini explanation if an API key is
   configured.
6. Open **Verifier** to assess the model output.
7. Use **Notebook Report** to generate a consolidated report.
8. Use **A2A Dashboard** to inspect the messages exchanged by the agents.

## Important considerations

- The application accepts CSV uploads; clean column names and an appropriate
  target column generally produce better modeling results.
- Automatic model selection is a convenience, not a substitute for domain
  review or production validation.
- Model quality depends on dataset size, target balance, missing values, and
  feature quality.
- Do not upload confidential or regulated data to external AI services unless
  your organization has approved that use.
- Generated reports and memory files may contain dataset-derived information.
  Treat them according to the sensitivity of the source data.

## Future improvements

- Add a data-question-answering agent backed by retrieval.
- Add fairness and bias evaluation to the verification stage.
- Expand the AutoML model catalog with optional gradient-boosting backends.
- Add Docker and Google Cloud Run deployment configurations.
- Add richer experiment tracking and comparison across model runs.
- Add optional voice-based interaction.

