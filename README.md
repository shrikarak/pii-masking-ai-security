# AI for Data Security: Automated PII Detection and Masking

**Copyright (c) 2026 Shrikara Kaudambady. All rights reserved.**

## 1. Introduction

In the age of data, protecting sensitive user information is not just good practice—it's a legal necessity. A critical aspect of data security and compliance with regulations like GDPR and CCPA is the prevention of data leakage. This involves redacting Personally Identifiable Information (PII) from unstructured text sources like customer support logs, internal chat messages, and user-generated content before it is stored or analyzed.

This project provides a Jupyter Notebook that implements a robust, AI-powered solution for automatically detecting and masking PII in text. It demonstrates a powerful hybrid approach that combines a pre-trained NLP model with custom-defined rules for maximum accuracy and flexibility.

## 2. The Solution Explained: A Hybrid NLP Approach

Our solution uses the `spaCy` library to build a pipeline that leverages both statistical models and explicit rules.

1.  **Pre-trained NER Model:** We use one of spaCy's pre-trained Named Entity Recognition (NER) models. This model has been trained on a vast corpus of text and can automatically identify common entities like:
    *   `PERSON` (People's names)
    *   `GPE` (Geopolitical Entities, like cities and countries)
    *   `DATE`, `ORG`, etc.

2.  **Custom Rule-Based Matching:** While the pre-trained model is powerful, it might not be optimized for specific PII formats like email addresses, phone numbers, or internal ID codes. To solve this, we add a custom `EntityRuler` to the spaCy pipeline. This component uses a list of exact patterns—in our case, regular expressions (regex)—to find and label specific strings. In this notebook, we add rules for:
    *   `EMAIL`
    *   `PHONE`
    *   `CREDIT_CARD`

By running the custom `EntityRuler` *before* the statistical NER model, we ensure that our precise, high-confidence rules always take precedence, creating a highly accurate and adaptable pipeline.

### The Workflow
The process is simple and effective:
1.  A block of raw text is fed into our custom spaCy pipeline.
2.  The `EntityRuler` first finds and labels any regex matches (e.g., `john.doe@email.com` becomes an `EMAIL` entity).
3.  The text is then passed to the NER model, which finds statistical entities (e.g., `John Doe` becomes a `PERSON` entity).
4.  The code iterates through all detected entities and replaces their text with a placeholder, such as `[REDACTED_PERSON]` or `[REDACTED_EMAIL]`, resulting in a clean, anonymized block of text.

## 3. How to Use the Notebook

### 3.1. Prerequisites

This project requires Python 3, Jupyter, and the `spaCy` library.

1.  **Install spaCy:**
    ```bash
    pip install spacy
    ```
2.  **Download the English Language Model:**
    After installing spaCy, you need to download the pre-trained model package.
    ```bash
    python -m spacy download en_core_web_sm
    ```

### 3.2. Running the Notebook

1.  Clone this repository:
    ```bash
    git clone https://github.com/shrikarak/pii-masking-ai-security.git
    cd pii-masking-ai-security
    ```
2.  Start the Jupyter server:
    ```bash
    jupyter notebook
    ```
3.  Open `pii_detection_and_masking.ipynb` and run the cells sequentially to see the PII detection and masking process in action.

## 4. Deployment and Customization

This notebook provides a robust template for a real-world PII masking service.

1.  **Deployment as an API:**
    *   The `mask_pii` function and the `nlp` pipeline object can be wrapped in a lightweight web framework like **FastAPI** or **Flask**.
    *   This creates an API endpoint that can accept a JSON payload containing raw text and return the anonymized text.

2.  **Integration into Data Pipelines:**
    This API service can be called from various points in your infrastructure:
    *   **Customer Support Systems:** Anonymize support tickets and chat logs before they are saved to a database for analytics.
    *   **Data Lakes:** As part of an ETL/ELT process, use the service to create an anonymized version of raw text data before it lands in a data lake or warehouse for broader access.
    *   **Internal Communications:** A filter could be applied to internal messaging systems to prevent the accidental sharing of customer PII in a public channel.

3.  **Customizing the Rules:**
    *   The `patterns` list in the notebook can be easily extended to include regular expressions for any other sensitive information specific to your business, such as `USER_ID`, `ORDER_NUMBER`, or `SSN`.
