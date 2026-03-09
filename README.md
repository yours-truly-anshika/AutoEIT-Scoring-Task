# AutoEIT: Automated Scoring Engine for Spanish Elicited Imitation Tasks  

## Project Overview
This repository contains a fully automated, reproducible scoring pipeline for the Spanish Elicited Imitation Task (EIT). 

The prompt highlighted that existing generative LLMs often produce inconsistent scoring results, rendering them unsuitable for scientific research. To solve this scoring variability, this project implements a **deterministic, hybrid "Rule-Based + ML Embeddings" engine** that applies the EIT rubric in a consistent and scientifically valid manner.

## Methodology: The Hybrid Engine
The engine processes raw transcriptions through three distinct phases:

1. **Text Preprocessing (Regex Cleaner):** According to the EIT protocol, hesitations, false starts, and unintelligible garble must not penalize the learner. A robust regex pipeline was built to automatically strip transcriber artifacts (e.g., `[pause]`, `xxx`, `ma-`) and stimulus syllable counts (e.g., `(7)`) before evaluation.
2. **Semantic Similarity (Transformer Embeddings):** Using the `sentence-transformers` library (`paraphrase-multilingual-MiniLM-L12-v2` via PyTorch), the engine converts the target prompt and the cleaned transcription into high-dimensional vectors to calculate exact Cosine Similarity.
3. **Rubric Application (Deterministic Rules):** The continuous similarity score is deterministically mapped to the discrete 0-4 EIT scale using hard-coded thresholds derived directly from the meaning-based rubric criteria (e.g., awarding a 4 for exact matches, a 0 for abandoned items, and mapping >0.90 similarity to a 3 for preserved meaning with minor deviations).

## Key Highlights & Validation
* **Strict Reproducibility:** Random seeds (`torch.manual_seed`) are strictly enforced across all libraries to guarantee that the engine produces the exact same score for the same sentence, 100% of the time.
* **Qualitative Output Evaluation:** Because ground-truth human scores were not provided in the sample dataset, the notebook includes a qualitative evaluation script. It extracts sample sentence pairs for scores 0, 2, 3, and 4, demonstrating how the model successfully handles edge cases and disfluencies without artificial penalization.
* **Quantitative Validation Framework:** To demonstrate readiness for the full 175-hour GSoC project, a validation framework was built using `scikit-learn`. This framework is designed to measure the official project goals: **> 90% sentence-level agreement** and **< 10-point total score difference**. A demonstration using a mock human baseline is included in the notebook.

## Repository Structure
```text
AutoEIT-Scoring-Task/
├── data/
│   ├── AutoEIT Sample Transcriptions for Scoring.xlsx  # Original dataset
│   └── AutoEIT_Scored_Transcriptions.xlsx              # Output file with 0-4 scores
├── notebooks/
│   └── AutoEIT_Evaluation_Task.ipynb                   # Fully documented scoring pipeline
├── README.md                                           
└── requirements.txt                                    # Environment dependencies
```

## How to Run Locally

1. Clone the repository:
   ```bash
   git clone https://github.com/yours-truly-anshika/AutoEIT-Scoring-Task.git
   cd AutoEIT-Scoring-Task
   ```

2. Create and activate a virtual environment:
   ```bash
   python -m venv venv
   # Windows: venv\Scripts\activate
   # Mac/Linux: source venv/bin/activate
   ```

3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

4. Open the Jupyter Notebook to run the pipeline:
   ```bash
   jupyter notebook notebooks/AutoEIT_Evaluation_Task.ipynb
   ```
