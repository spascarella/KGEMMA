# KGEMMA: Knowledge Graph for Enhanced Model Management in AI

KGEMMA is a knowledge graph–based system for managing, analyzing, and querying machine learning models and their properties. It integrates data from the [Hugging Face Hub](https://huggingface.co/), [arXiv](https://arxiv.org/), and LLM-extracted information to build a semantic knowledge graph of AI/ML models, their evaluation metrics, hyperparameters, and associated research papers.

## Table of Contents

- [Project Overview](#project-overview)
- [Repository Structure](#repository-structure)
- [Ontology](#ontology)
- [Installation](#installation)
- [Usage](#usage)
- [SPARQL Queries](#sparql-queries)

## Project Overview

The project follows a five-stage pipeline, each implemented as a Jupyter notebook:

1. **Data Scraping** (`data_scraping.ipynb`) — Fetches model metadata from the Hugging Face Hub and scrapes related papers from arXiv.
2. **Preprocessing** (`preprocessing.ipynb`) — Cleans and transforms the scraped data, preparing it for RDF conversion.
3. **Hyperparameter Extraction** (`llm_hyperparameters_scraping.ipynb`) — Uses the OpenAI API to extract hyperparameter names and values from paper text.
4. **Graph Construction** (`graph.ipynb`) — Converts the structured data into RDF triples and serializes the knowledge graph in Turtle format.
5. **Querying & Analysis** (`query.ipynb`) — Runs SPARQL queries against the graph for quality assurance and analytics.

## Repository Structure

```
KGEMMA/
├── README.md
├── Project_Proposal.pdf
├── .gitignore
├── data_scraping.ipynb
├── preprocessing.ipynb
├── llm_hyperparameters_scraping.ipynb
├── graph.ipynb
├── query.ipynb
└── queries/
    ├── quality/          # Data quality queries (q1–q8)
    │   ├── q1.rq        # Count entities by class
    │   ├── q2.rq        # Models without names
    │   ├── q3.rq        # Models without hyperparameters
    │   ├── q4.rq        # Domain constraint violations
    │   ├── q5.rq        # Range/datatype violations
    │   ├── q6.rq        # Duplicate model names
    │   ├── q7.rq        # Instances without types
    │   └── q8.rq        # Duplicate triples
    └── results/          # Analytics queries (q1–q11)
        ├── q1.rq        # Top 4 models per task by downloads
        ├── q2.rq        # Datasets with text-classification metrics
        ├── q3.rq        # Models by base model
        ├── q4.rq        # Text-classification metrics per dataset
        ├── q5.rq        # Italian text-generation models
        ├── q6.rq        # Image-classification models by dataset
        ├── q7.rq        # Best performing models by metric
        ├── q8.rq        # Model hyperparameters
        ├── q9.rq        # Model–paper relationships
        ├── q10.rq       # Additional analytics
        └── q11.rq       # Additional analytics
```

## Ontology

The knowledge graph uses the custom namespace `http://kg-course/model-management/` alongside standard vocabularies (RDF, RDFS, XSD, Schema.org, BIBO).

**Core classes:**

| Class | Description |
|---|---|
| `kg:Model` | A machine learning model |
| `kg:HyperParameter` | A model configuration parameter |
| `kg:EvaluationMetric` | A performance metric on a dataset |
| `kg:Paper` | A research paper describing a model |

**Key properties:**

| Property | Domain → Range | Description |
|---|---|---|
| `kg:name` | Model → xsd:string | Model name |
| `kg:task` | Model → xsd:string | ML task (e.g. text-classification) |
| `kg:downloads` | Model → xsd:integer | Download count |
| `kg:hasHyperParameter` | Model → HyperParameter | Links a model to its hyperparameters |
| `kg:hasEvaluationMetric` | Model → EvaluationMetric | Links a model to its evaluation metrics |
| `kg:hasPaper` | Model → Paper | Links a model to its research paper |
| `kg:parameterName` | HyperParameter → xsd:string | Name of the hyperparameter |
| `kg:parameterValue` | HyperParameter → xsd:string | Value of the hyperparameter |
| `kg:metricType` | EvaluationMetric → xsd:string | Metric name (accuracy, F1, etc.) |
| `kg:metricValue` | EvaluationMetric → xsd:float | Metric score |
| `kg:datasetName` | EvaluationMetric → xsd:string | Dataset used for evaluation |

## Installation

### Prerequisites

- Python 3.12 or later
- A Hugging Face API token (for data scraping)
- An OpenAI API key (for hyperparameter extraction)

### Setup

```bash
# Clone the repository
git clone https://github.com/spascarella/KGEMMA.git
cd KGEMMA

# Install dependencies
pip install pandas rdflib oxrdflib huggingface_hub arxiv openai pygments
```

## Usage

Run the notebooks in order:

```text
1. data_scraping.ipynb              → Scrape models and papers
2. preprocessing.ipynb              → Clean and prepare data
3. llm_hyperparameters_scraping.ipynb → Extract hyperparameters via LLM
4. graph.ipynb                      → Build the RDF knowledge graph
5. query.ipynb                      → Run SPARQL queries
```

Each notebook writes its outputs (CSV, JSON, RDF/Turtle files) to directories listed in `.gitignore` (`output/`, `output_json/`, `data/`, `papers/`).

## SPARQL Queries

Pre-written SPARQL queries are stored in the `queries/` directory:

- **`queries/quality/`** — Eight queries that validate the graph (missing names, constraint violations, duplicates, etc.).
- **`queries/results/`** — Eleven queries for model discovery and comparison (top models by downloads, best metrics, model–paper links, etc.).

These queries are loaded and executed in `query.ipynb`.