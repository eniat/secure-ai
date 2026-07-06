# Secure AI: Adversarial ML and LLM Guardrails

This project explores two secure AI problem areas: adversarial attacks against an image classifier and prompt injection attacks against a tool-using LLM system. The implementation is contained in a Jupyter notebook, with a short report explaining the methodology, results and limitations.

## Overview

**Task 1** trains a CNN face classifier on the Labeled Faces in the Wild dataset, then implements two targeted attacks that try to classify George W. Bush images as Colin Powell.

**Task 2** tests prompt injection against a local LLM system with a system prompt, file-reading tool, privacy check and guardrail classifier.

## What This Demonstrates

- PyTorch CNN image classification
- targeted data poisoning
- targeted FGSM-style evasion
- attack success rate evaluation
- local LLM inference using vLLM
- prompt injection testing
- file-access risk in tool-using LLM systems
- SAFE/UNSAFE guardrail classification
- concise reporting of secure AI results

## Repository Structure

```text
.
├── secure_ai_adversarial_ml_llm_guardrails.ipynb
├── Report.pdf
├── json_folder/
│   ├── user_info.json
│   ├── accounts.json
│   ├── locations.json
│   └── guardrail_cases.json
├── requirements.txt
├── .gitignore
└── README.md
```

`json_folder/` is required for the LLM file-access and guardrail sections. The notebook expects it at the repository root.

## Report

`Report.pdf` contains the written analysis. It explains the dataset, attack methodology, success rates, prompt extraction approach, file extraction approach, guardrail design and limitations.

The notebook provides the implementation and outputs; the report explains the reasoning behind them.

## Tech Stack

- Python
- Jupyter Notebook
- PyTorch
- NumPy
- scikit-learn
- scikit-image
- transformers
- requests
- vLLM
- IBM Granite 4.0 H 1B

## Task 1: Adversarial Face Classification

The first task uses the Labeled Faces in the Wild dataset. The dataset is filtered to people with at least 70 images, giving 1,288 samples, 1,850 features per image, 7 classes, 1,030 training samples and 258 testing samples.

A LeNet-style CNN is trained as the baseline classifier.

### Targeted Poisoning Attack

The poisoning attack is a backdoor-style training-time attack. A small trigger is inserted into selected source-class training samples, and their labels are changed to the target class.

Configuration:
- source class: George W. Bush
- target class: Colin Powell
- poison rate: 10%
- trigger size: 3x3 patch
- weighted sampling used to increase poisoned sample influence

Reported result:
- poisoning attack success rate: 92.45%

### Targeted Evasion Attack

The evasion attack is a targeted FGSM-style inference-time attack. It modifies source-class test samples using the input gradient to push predictions toward the target class.

Configuration:
- source class: George W. Bush
- target class: Colin Powell
- epsilon: 0.31

Reported result:
- targeted evasion success rate: 97.17%

## Task 2: Prompt Injection and Guardrails

The second task uses a local IBM Granite model through vLLM. The notebook builds a simple tool-using LLM system with a system prompt, local LLM wrapper, file-reading tool, privacy check and guardrail classifier.

The system is tested against prompt injection attempts that try to reveal hidden instructions and access files in `json_folder/`.

The required JSON files are:

- `json_folder/user_info.json`
- `json_folder/accounts.json`
- `json_folder/locations.json`
- `json_folder/guardrail_cases.json`

The JSON files are synthetic and are included only so the file-access and guardrail sections can be reviewed and reproduced.

The guardrail classifies inputs as `SAFE` or `UNSAFE`, then rechecks generated outputs.

Reported result:

- guardrail accuracy: 85%

## Setup

Create and activate a virtual environment:

```bash
python -m venv venv
```

Windows:

```bash
venv\Scripts\activate
```

macOS/Linux:

```bash
source venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Start the local LLM server before running Task 2:

```bash
vllm serve --model ibm-granite/granite-4.0-h-1b --host 0.0.0.0 --port 5001
```

The notebook expects the LLM endpoint at `http://127.0.0.1:5001/v1/completions`.

## How to Run

1. Ensure `json_folder/` is present at the repository root.
2. Start the vLLM server if running Task 2.
3. Open `secure_ai_adversarial_ml_llm_guardrails.ipynb`.
4. Run the notebook cells from top to bottom.

Task 1 can be run without the LLM server. Task 2 requires both the LLM server and `json_folder/`.

## Outputs

Notebook outputs are intentionally retained so the main experiment results can be reviewed without rerunning every cell.

The notebook shows dataset statistics, CNN accuracy, poisoning success rate, evasion success rate, prompt extraction attempts, synthetic file-access test results, guardrail classifications and guardrail accuracy.

## Limitations

This is a controlled secure AI experiment. Results depend on the selected model, dataset, hyperparameters, prompt wording and local LLM behaviour.

The guardrail improves detection of common unsafe prompt patterns, but it is not a production-grade defence. Adaptive prompt injection techniques may still bypass it.

## Dataset and safety notice

This repository uses public research data and synthetic local JSON files for controlled secure AI experiments. The JSON files do not contain real personal data, real credentials, live account data or confidential information. Prompt injection and file-access tests are included only to demonstrate defensive evaluation of tool-using LLM systems in a local lab environment.

## Usage Notice

This repository is provided for portfolio and review purposes only.

All rights are reserved. No permission is granted to copy, redistribute, submit, or reuse this work, in whole or in part, for academic coursework, assessment, or commercial purposes.

Where this repository relates to university coursework, it is shared only to demonstrate my own technical work and should not be used by other students as a submission or solution.
