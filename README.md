# Drone News Aggregation Agent

This repository documents the complete build and architecture for an autonomous AI agent designed to streamline daily content creation for a niche industry platform. The system fully automates news gathering, intelligent summarization, content formatting, and cross-platform social publishing.

---

## Technology Stack & Architecture

The solution uses a low-code, self-hosted stack to ensure maximum control over data processing and minimal operational costs.

| Component | Tool / Platform | Design Justification |
| :--- | :--- | :--- |
| **Workflow Engine** | **n8n** (Self-Hosted Docker) | Provides a powerful visual workflow editor and the flexibility to inject custom code (JavaScript) for complex data transformations and API error handling. |
| **Data Source** | **NewsData.io API** | Selected for its reliability and its dedicated support for the **`image_url`** field, which resolved persistent issues found with standard Google News RSS feeds. |
| **AI / LLM** | **Google Gemini** (Free Tier) | Chosen for its high-speed API performance and cost efficiency, making it ideal for high-volume, repetitive tasks like summarization and data extraction. |
| **Social Output** | **LinkedIn** | Primary platform target for professional content distribution, demonstrating API-level posting capabilities. |
| **User Flow** | **Daily Schedule Trigger** | Configured to execute the entire process once daily at a fixed time, creating a set-it-and-forget-it content solution. |

---

## Engineering Decisions & Debugging

The following decisions were crucial for achieving a production-ready, reliable agent:

* **Data Integrity and Image Fix:**
    * Abandoned unstable RSS feeds (which provided broken image links) for the **NewsData.io API**.
    * Implemented a separate **HTTP Request node** to download the image URL and convert it to a **binary file (`data`)**—the format required by LinkedIn's API for media upload.
* **Anti-Hallucination Prompting:**
    * The LLM prompt is engineered with strict rules (e.g., "Use ONLY the information in the Description") and is forced to return a single **JSON object**, eliminating the risk of the AI inventing facts or adding commentary.
* **Resilience & Stability:**
    * Configured the workflow to use the high-throughput **Gemini 1.5 Flash** model to avoid frequent API crashes (429/503 errors) common with the free tier's `Pro` model.
    * The final LinkedIn connection required manual **token deletion and re-authorization** to overcome persistent `403 Forbidden` errors, proving the ability to solve external API permission conflicts.
* **Clean Output:** The `Edit Fields` node is used to successfully parse the Gemini JSON output into clean, usable fields (like `final_caption`, `image_url`, etc.), ensuring the final LinkedIn node receives perfectly structured data.

---

## Submission Contents

This repository is self-contained and provides all necessary files:

* `post_automation.json`: The exported n8n workflow file.
* `README.md`: This document.
* `screenshots/`: A folder containing all visual proof of successful execution:
    * Full green n8n automation pipeline.
    * Successful creation of multiple posts on the target social platform (LinkedIn).
    * Final node output confirmation.
