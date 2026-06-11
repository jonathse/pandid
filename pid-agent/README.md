# pid-agent (Agent Application)

This directory contains the core agent container application built with Google Cloud **`agents-cli`** and the **Agent Development Kit (ADK)**. 

The `pid-agent` coordinates a multi-agent refinement loop (consisting of an Extractor, a Reviewer, and a Zoomer agent) that consumes a custom, reusable parsing skill, extracts schematic graph topologies (nodes & edges) from complex Piping and Instrumentation Diagrams (P&IDs), and loads them into BigQuery.

---

## 📂 App Directory Structure

```
pid-agent/
├── app/                  # Main application source code
│   ├── __init__.py       # App container definition
│   ├── agent.py          # Multi-agent graph coordination logic
│   └── app_utils/        # Application utilities and BQ loaders
├── tests/                # Test suites
│   ├── unit/             # Setup and structure mock tests
│   ├── integration/      # Stream and endpoint mock tests
│   └── eval/             # Agent behavior metrics and datasets
├── pyproject.toml        # Application dependency requirements
└── agents-cli-manifest.yaml  # CLI metadata config (Region, Target)
```

---

## 🚀 Step-by-Step Quick Start Guide

This guide is designed for developers who are **new to ADK, agents-cli, and Google Cloud**. 

> [!NOTE]
> If you have not created a Google Cloud Project or configured your local developer terminal yet, please first refer to the comprehensive [Root Repository Setup Guide](../README.md) for full instructions.

### 1. Prerequisites (Fast Setup)

Ensure you have **`uv`** (Python tool manager) and **Google Cloud SDK** installed:
*   **Install `uv` (macOS/Linux):** `curl -LsSf https://astral.sh/uv/install.sh | sh`
*   **Install Google Cloud SDK:** Follow [Google Cloud SDK Guides](https://cloud.google.com/sdk/docs/install).

Authenticate your terminal with Google Cloud (needed for Gemini AI API calls):
```bash
gcloud auth login
gcloud auth application-default login
```

---

### 2. Configure Environment Variables (`.env`)

The agent needs to know which project to charge Gemini API calls to, and where to create the BigQuery tables.

1.  In this directory (`pid-agent`), copy the environment template:
    ```bash
    cp .env.example .env
    ```
2.  Open `.env` in any editor and specify your GCP configuration:
    ```ini
    # Google Cloud Project Configuration
    GOOGLE_CLOUD_PROJECT=your-gcp-project-id
    GOOGLE_CLOUD_LOCATION=us-central1
    GOOGLE_GENAI_USE_VERTEXAI=True
    AGENT_MODEL="gemini-3.1-pro-preview"

    # BigQuery Configuration
    # The agent will automatically provision this dataset and its tables on startup!
    BIGQUERY_DATASET_ID=your_desired_dataset_id
    ```

---

### 3. Install and Launch Locally

`agents-cli` provides unified tooling to manage dependencies, launch local playgrounds, and execute lints/checks.

1.  **Install the Agents CLI globally:**
    ```bash
    uv tool install google-agents-cli
    ```
2.  **Verify CLI Installation and Setup Skills:**
    ```bash
    uvx google-agents-cli setup
    ```
3.  **Install dependencies inside a virtual environment:**
    ```bash
    agents-cli install
    ```
4.  **Run the Local Web Playground:**
    ```bash
    agents-cli playground
    ```
    *   Open the URL printed in the terminal (usually `http://localhost:8501`).
    *   Select the **`app`** folder in the web UI.
    *   Upload a P&ID diagram (image/PDF) and converse directly with your agent!

---

### 4. Code Quality & Testing

Before deploying, run the verification suite to ensure all components and imports are correct:

*   **Format and Lint Code:**
    ```bash
    agents-cli lint
    ```
*   **Run Unit and Integration Tests:**
    ```bash
    uv run pytest tests/unit
    ```

---

### 5. Deploy the Agent to Google Cloud

By default, the boilerplate is in **Prototype Mode** (runs and tests locally). When you are ready to host the agent on Google Cloud as a fully managed endpoint:

1.  **Add your Deployment Target:**
    Configure the project scaffolding to add Google's managed **Agent Runtime** support:
    ```bash
    agents-cli scaffold enhance . --deployment-target agent_runtime
    ```
2.  **Deploy:**
    Trigger the automatic cloud compilation and deployment pipeline:
    ```bash
    agents-cli deploy
    ```
    The CLI will configure all GCP infrastructure, compile the agent, and output a live cloud service URL!
