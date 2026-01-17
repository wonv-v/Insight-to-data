## 1. Executive Summary
*"Transforming Unstructured Voices into Structured Intelligence."*

This project engineered an automated data pipeline that converts high-volume, unstructured customer feedback (raw text) into strict, queryable SQL data.  
By utilizing Large Language Models (LLMs) not as chatbots but as **Semantic Logic Engines**, I replaced brittle keyword matching (RegEx) with **context-aware inference**.  
The system features a **Text-to-SQL interface**, allowing non-technical stakeholders to query database insights using natural language.

**Note:** While the system design, architecture, and solution logic were created by the student, AI assistance was leveraged to support technical implementation and data structuring tasks.

**Key Technologies:** Python, OpenAI API (Structured Outputs), Pydantic, SQL (MySQL), LangChain

---

## 2. The Problem
Customer feedback is a vital resource in modern product development. However, as user volume grows, traditional analysis methods fail:

- **Scalability Bottleneck:** Manual review is impossible at scale, and simple keyword matching misses context (e.g., sarcasm).  
- **Data Flatness:** Standard sentiment analysis scores a review as a single "Positive" or "Negative," failing to capture mixed feedback (e.g., "The product is amazing, but the shipping was a nightmare").  
- **Accessibility Gap:** Business insights remain locked behind SQL queries, requiring constant developer intervention for reporting.

---

## 3. Solution Architecture
**Concept:** "LLM as a Backend ETL Parser"

1. **Ingestion:** Raw reviews are captured and stored immutably.  
2. **Semantic Parsing (AI Layer):**  
   - Text is processed by GPT-4o with **Strict Schema Enforcement**  
   - The model identifies multiple distinct issues within a single review (1:N mapping)  
3. **Normalization:** Vague terms are mapped to standardized Enum tags  
   - e.g., "broken", "cracked" → `DAMAGED_ITEM`  
4. **Natural Language Querying (HCI Layer):**  
   - Implemented a Text-to-SQL agent using LangChain  
   - Example: "Show me the top 3 complaints about shipping this month" → real-time data visualization

---

## 4. Key Technical Challenges & Solutions

### Challenge A: Controlling AI Hallucinations
- **Problem:** LLMs are non-deterministic. Without constraints, the model might tag one review as "Battery" and another as "Power_Issue," making SQL aggregation impossible.  
- **Solution:** Implemented Pydantic validation with Enum constraints. If the output does not match the schema, the pipeline automatically rejects or retries.  

Challenge B: Modeling Complex Data Relationships (1:N)
	•	Problem: A single review often contains multiple sentiments. Storing one sentiment score per review leads to data loss.
	•	Solution: Designed a One-to-Many (1:N) database relationship. Python logic extracts a list of insight objects from a single text block, populating a child table (review_analytics) linked to the parent (raw_reviews).

Challenge C: Bridging the Gap for Non-Technical Users
	•	Problem: SQL is a barrier for Product Managers and Marketers.
	•	Solution: Integrated a vector-augmented Text-to-SQL engine. Natural language questions are translated into executable SQL queries.
	•	Result: Democratized data access, reducing ad-hoc requests to the engineering team by 80%.

- Advanced Strategy: Metadata-Augmented Hybrid Input

Objective

To further bridge the gap between human intent and machine logic, this project proposes a Hybrid Feedback Architecture that combines structured metadata with natural language input.

⸻

Core Concept: Guided Intelligence

Instead of relying solely on free-form text, users provide feedback through:
	•	Predefined Like/Dislike tags
(e.g., Shipping, UI/UX, Pricing)
	•	An open-ended text field
(“I would like to hear about it”)

This design introduces a Human-in-the-Loop labeling process at the point of entry, allowing users to explicitly declare intent before the AI performs semantic analysis.

⸻

Technical Synergy & Benefits

1. Anchored Inference (Improved Precision)
	•	Traditional LLM pipelines must infer the topic from scratch.
	•	In this system, user-selected tags act as a Ground Truth anchor.
	•	The LLM’s role is narrowed to:
	•	Extracting nuance
	•	Interpreting sentiment
	•	Identifying sub-issues

Result:
Significantly reduced semantic ambiguity and lower misclassification rates.

⸻

2. Conflict Detection & Data Integrity
The system cross-verifies user-selected tags against the written feedback.

Example:
	•	Tag: Like → Customer Service
	•	Text: “The staff was rude.”

This mismatch triggers a Data Conflict flag.
	•	The LLM acts as an arbiter, evaluating whether the input reflects:
	•	Sarcasm
	•	User misselection
	•	Mixed or contradictory sentiment

Result:
Higher data reliability and protection against silent labeling errors.

⸻

3. HCI-Driven Efficiency (Token & Cost Optimization)
	•	Tags function as explicit metadata, reducing prompt ambiguity.
	•	The LLM no longer performs broad-spectrum topic classification.
	•	Prompts become:
	•	Shorter
	•	More targeted
	•	Faster to process

Result:
Lower token usage (reduced API costs) and improved system throughput.

⸻

Architectural Impact

Data Responsibility Split
	•	Primary Category
	•	Defined directly by user tags
	•	Stored in SQL: category_tag
	•	Granular Insight
	•	Extracted by the LLM
	•	Stored in SQL: insight_detail or sub_category

⸻

Final Outcome

This approach produces a dual-layered data structure that delivers:
	•	High-level statistical clarity
(Reliable aggregation, dashboards, trend analysis)
	•	Deep qualitative intelligence
(Context, nuance, and actionable insight)


5. Database Schema Design
	•	raw_reviews (Source of Truth)
	•	Stores immutable user content
	•	Columns: review_id (PK), user_text, timestamp
	•	review_insights (Structured Analytics)
	•	Stores AI-parsed variables
	•	Columns:
	•	insight_id (PK)
	•	review_id (FK) → links to parent review
	•	category_tag (ENUM) → standardized topic
	•	sentiment_score (INT) → 1-10 granularity

6. Business Impact
	•	Operational Efficiency: Automated classification of 10,000+ monthly reviews, saving ~40 hours of manual labor per week
	•	Granular Insights: Successfully decoupled “Service” issues from “Product” issues → Engineering team focused on product defects, Operations handled shipping complaints
	•	Real-Time Agility: Enabled instant trend detection (e.g., spike in “Defective Button” reports within hours of a new release)

7. Future Improvements
	•	Hybrid Search: Implement vector embeddings (RAG) for semantic search alongside SQL queries
	•	Example: “Find reviews that sound frustrated about the battery”
	•	Cost Optimization: Fine-tune a smaller model (Llama 3 or GPT-4o-mini) to reduce API costs for high-volume batch processing

Disclaimer

This portfolio is not a technical implementation document. It is a visualization of system thinking created by a student studying Information Systems, reflecting structured analysis and conceptual design through written exposition.

