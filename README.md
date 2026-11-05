# Project 1: Texas Data Broker Consumer-Rights Page Compliance Review

Authors: Robin Sambuis and Joe Rucker

This project reviews data brokers' consumer-rights pages against a seven-right rubric. The workflow extracts page text, routes relevant passages to parallel criterion checkers, aggregates and verifies findings, and lets a human reviewer approve gaps before generating a revision plan. Findings use four labels: Met, Not Met, Unsure, and Not Applicable.

## Files

| File | Purpose |
| --- | --- |
| `Project1_Texas_Data_Broker_Compliance.ipynb` | Main workflow, interactive demo, page-text baseline, and evaluation. |
| `Project1_Name_Only_Baseline.ipynb` | Separate baseline using company names and criteria without policy text. |
| `Data Broker Registration.xlsx` | Registry input containing company information and consumer-rights page links. |
| `Criteria.xlsx` | Criteria, status definitions, and a golden set of 20 companies with seven labels each. |
| `evidence_review_labeled.csv` | Included completed human reviews of supporting evidence; used only for optional human evaluation. |
| `recommendation_ratings_labeled.csv` | Included completed human ratings of revision recommendations; used only for optional human evaluation. |

## Environment and dependencies

The notebooks are written for Google Colab with a Python runtime and internet access. They use `google.colab` for secrets, uploads, and downloads, so they do not run unchanged in a standard local Jupyter environment.

The main notebook installs `openai`, `pydantic`, `pandas`, `openpyxl`, `beautifulsoup4`, and `requests`. It also imports `ipywidgets` and `IPython`, which are supplied by Colab. Its optional browser-fallback installation cell installs `playwright` and Chromium with system dependencies. Run that cell to enable browser retries for pages requiring JavaScript.

The name-only baseline installs `openai`, `pandas`, `openpyxl`, and `pydantic` and uses Colab and IPython.

Package versions are not pinned in the supplied notebooks. The main notebook prints selected library versions in Section 12.

## API configuration

Both notebooks read a Colab secret named `OPENAI_API_KEY`. Add the credential to Colab Secrets and enable notebook access before running the setup cell. Do not put credentials in the notebook or repository.

The supplied configuration uses:

- API endpoint: `https://api.portkey.ai/v1`
- Default model alias: `@30875-fall26-openai/gpt-5-nano`
- Optional model override: the `OPENAI_MODEL` environment variable, set before running setup.

The credential must have access to this Portkey endpoint and model alias. A regular OpenAI API key alone is not sufficient for the supplied configuration. Using another provider requires changing the client configuration and model. Model requests may incur charges.

## Run the main notebook and demo

1. Open `Project1_Texas_Data_Broker_Compliance.ipynb` in Google Colab.
2. Configure the API secret as described above.
3. Have `Data Broker Registration.xlsx` and `Criteria.xlsx` ready to upload. The registry filename matches the loader's `*Broker*Registration*.xlsx` pattern. Keep its contents unchanged: the first three rows contain metadata, and the fourth row contains column headers.
4. Run the cells sequentially through Section 9. Upload both workbooks when prompted, or place them in the Colab working directory beforehand.
5. In Section 9, select a company and click **Run compliance check**.
6. Inspect the findings and evidence, review the approval choices, and click **Generate revision plan from approved gaps** when ready.
7. Use **Save report (JSON)** to download the report.

For the interactive demo, you can stop at Section 9.

## Run classification evaluation

Continue into Sections 10 and 11 to define the page-text baseline and evaluate the main workflow and baseline against the golden set. This makes additional API requests. Later evaluation cells produce evidence-review and recommendation-rating templates. The operational check fetches a sample of registry pages.

Run the evaluation cells in order. The human-evaluation analysis below is optional, even though the completed review files are included. If you choose to skip it, run the other cells individually rather than selecting **Run all**.

### Optional human evaluation

The repository includes `evidence_review_labeled.csv` and `recommendation_ratings_labeled.csv`. These completed files support an optional analysis of evidence support and recommendation quality. They are not required for the main workflow, interactive demo, or classification evaluation.

The human-evaluation code cell appears immediately after **Usefulness of the recommendations** and before **Failure analysis**. It begins with:

```python
EVIDENCE_LABELS_PATH = "evidence_review_labeled.csv"
RATINGS_PATH = "recommendation_ratings_labeled.csv"
```

**To run this analysis:** upload both included CSVs to the Colab working directory, keeping their filenames unchanged, then execute the cell. The output reports evidence support and summary statistics for the human ratings.

**To skip this analysis:** skip that entire code cell and continue with **Failure analysis** and the remaining sections. The notebook does not automatically skip the cell; running it without both CSVs in the working directory raises `FileNotFoundError`. Do not substitute for other files.

The included labels describe the outputs originally reviewed. Rerunning the workflow can produce different evidence or recommendations. To evaluate a new run, review its newly generated templates and save completed copies under the expected filenames:

1. In `evidence_review.csv`, complete `human_supports_finding` with `yes` or `no`. Preserve the existing columns, including `final_status` and `right`, and save as `evidence_review_labeled.csv`. At least one valid yes/no label is needed for the support-rate calculation.
2. In `recommendation_ratings.csv`, complete `accuracy_1to5`, `specificity_1to5`, and `ease_of_use_1to5`, then save as `recommendation_ratings_labeled.csv`. This template is generated only when there are revision items to rate.
3. Upload the completed files to Colab before running the optional analysis.

## Run the company-name-only baseline

1. Open `Project1_Name_Only_Baseline.ipynb` in Google Colab.
2. Configure the same API secret and run the cells in order.
3. Upload `Criteria.xlsx` when prompted. This notebook does not need the registry workbook or labeled human-evaluation CSVs.
4. With `RUN_BASELINE = True`, the notebook attempts one model request per golden-set company: 20 requests in total.
5. Inspect the results in `standard_baseline_results/`, including `baseline_responses.json`, `standard_baseline_predictions.csv`, `standard_baseline_metrics.csv`, `standard_baseline_human_review.csv`, and `standard_baseline_errors.csv`.

## Outputs and reproducibility

The main workflow saves extracted page text in `saved_pages/`, supports JSON report downloads through the demo, and generates human-review CSV templates during evaluation. Download outputs you want to retain before ending the Colab session.

Live policy pages, model responses, and unpinned package versions may change between runs. The provided registry workbook is dated October 17, 2026, although the main notebook's introductory data-loading text refers to an October 25 export. Use the actual supplied workbook date when describing this input.

The labels assess the reviewed page against the project rubric; they do not establish a company's overall legal compliance.
