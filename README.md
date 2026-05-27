# Supply Chain Management (SCM) Assistant

A production-ready Retrieval-Augmented Generation (RAG) pipeline built using **Flowise AI** to analyze supply chain performance datasets and cross-reference automated operations against internal regulatory policy directives. 

This application utilizes a **Google Gemini 2.5 Flash** large language model integrated with a serverless vector database registry to execute instant, contextual data retrieval over structured commercial records entirely within zero-cost service limits.

---

## 🌐 Public Architecture & Live Chatbot
* **Live Public Chatbot Deployment:** [https://cloud.flowiseai.com/chatbot/15b17f7d-1421-40f0-8c94-cc106cfb3ead]
* **Workflow Configuration Graph:** [`scm_assistant.json`](./scm_assistant.json)

---

## 🛠️ Production Tech Stack
* **Orchestration Engine:** Flowise AI (Low-Code Node Framework)
* **Large Language Model (LLM):** Google Gemini 2.5 Flash (`gemini-2.5-flash`)
* **Vector Embedding Layer:** Hugging Face Serverless Inference API (`sentence-transformers/all-MiniLM-L6-v2`)
* **Vector Database Index:** Pinecone Serverless Registry (384 Dimensions, Cosine Similarity Metric)
* **Data Ingestion Pool:** Combined CSV Data (2,000 Purchase Orders) + Operational PDF Policy Directives

---

## 📈 System Engineering & Performance Tuning

### Data Ingestion Pipeline & Architecture
The system utilizes a split execution model. High-density data matrices from `supplier_performance_data.csv` (containing 2,000 discrete rows detailing PO identifiers, supplier compliance scores, delivery timelines, and financial volumes) were ingested alongside corporate standard operational policies. 

### Chunking Strategy & Vector Synchronization Optimization
During exploratory testing, variations in text-chunk configurations were evaluated to preserve the mathematical integrity of tabular rows during transformation:
* **Configuration Variant A (Standard Sentence Splitting):** Standard recursive character chunking resulted in corrupted row cross-references, fracturing structural lines across boundaries and blinding the model to complete records.
* **Configuration Variant B (Row-Isolated Chunking & High-Capacity Context Retrieval - *DEPLOYED*):** Configured text boundaries to isolate distinct purchase records. To prevent context window starvation during multi-row lookups, the retrieval configuration was scaled to **Top K = 50**. This expansion explicitly enables the vector search thread to surface comprehensive tabular distributions alongside policy sections simultaneously, resolving the fallback errors ("*Hmm, I'm not sure*") caused by context truncation.

---

## 🧪 Verbatim Evaluation Question & Answer Log

### Question 1: List Tier-3 suppliers with active disruptions and their policy response level.
**Chatbot Response:**
> The following Tier-3 suppliers have an active disruption flag, and a Level 3 response applies to each:
> * **SUP-010 (Bohai Electronics):** Active Disruptions: "Typhoon season disruption" (PO-11750, PO-10010), "Tariff adjustment pending" (PO-10590), "Regulatory enforcement pending" (PO-10126). Response Level: Level 3 — This supplier is flagged as High Risk (e.g., in PO-11750, PO-10010, PO-10126) with active disruption flags. Additionally, "Regulatory enforcement pending" is a disruption category that mandates a Level 3 response regardless of risk level.
> * **SUP-080 (Maghreb Castworks):** Active Disruption: "Regulatory enforcement pending" (PO-10312). Response Level: Level 3 — This supplier is flagged as High Risk (PO-10312) with an active disruption flag. Furthermore, "Regulatory enforcement pending" is a disruption category that mandates a Level 3 response regardless of risk level.

### Question 2: Which suppliers qualify for the Volume Rebate Program? Give total count.
**Chatbot Response:**
> Based on the provided context and corporate procurement policy parameters (requiring Tier-1 placement, an On-Time Delivery rate $\ge$ 93%, a Defect Rate < 0.5%, and a Sustainability Score > 85), there are a total of **19 suppliers** who qualify for the annual Volume Rebate Program. The qualifying supplier entities identified include:
> * SUP-001 (Apex Logistics), SUP-005 (Global Freight Corp), SUP-014 (Zhongshan Tech), SUP-022 (Nordic Parts), SUP-033 (Hanover Precision), SUP-045 (Vanguard Systems), SUP-051 (PacRim Component), SUP-064 (Caliber Assembly), SUP-072 (Eurasia Foundry), SUP-089 (Alpine Micro), SUP-095 (Sterling Machining), SUP-104 (Vertex Forge), SUP-112 (Siam Polymer), SUP-128 (Atlas Castings), SUP-141 (Krypton Circuits), SUP-155 (Beacon Logistics), SUP-168 (Matrix Integration), SUP-180 (Zenith Instruments), and SUP-199 (Summit Dynamics).

### Question 3: Which region has highest total PO value? Does it breach the concentration cap?
**Chatbot Response:**
> Based on the processed transaction registers, the **EMEA (Europe, Middle East, and Africa)** geographic region represents the highest financial concentration, accumulating a total Purchase Order value of **$193,987,179.91**. 
> According to **Policy §5.3**, regional financial distribution is restricted to a strict risk concentration cap of **45.5%** of the total global procurement portfolio. The EMEA volume accounts for approximately **48.5%** of total global spend; therefore, the EMEA region **explicitly breaches the corporate concentration limit** and requires a formal executive mitigation waiver.

### Question 4: List all Supplier Watch List (SWL) companies and their policy restrictions.
**Chatbot Response:**
> Suppliers are designated on the Supplier Watch List (SWL) when their aggregate Operational Compliance Score falls strictly below the mandatory baseline floor of 60. There are **11 suppliers** identified under active SWL restrictions:
> * SUP-011 (Dravex Components India)
> * SUP-029 (Plataforma Global)
> * SUP-042 (Maghreb Castworks Branch B)
> * SUP-057 (Oasis Manufacturing)
> * SUP-073 (Baltic Freight)
> * SUP-091 (Gaza Assembly)
> * SUP-115 (Andes Material Corp)
> * SUP-134 (Mekong Fabrications)
> * SUP-150 (Spartan Heavy Tech)
> * SUP-177 (Siberian Forging)
> * SUP-191 (Patagonia Castings)
> 
> **Policy Restrictions (§3.4):** Any entity placed on the active SWL is subject to a mandatory procurement freeze restricting new Purchase Order issuances to a maximum ceiling of **20% of their prior quarter's verified historical volume**. No contract tier upgrades or extensions can be processed while on SWL status.

### Question 5: Which product category has the highest average defect rate? Does it exceed Tier-2 limit?
**Chatbot Response:**
> The product category containing the highest aggregate average defect rate is **Mechanical Components**, maintaining a verified mean defect rate of **2.12%** calculated across 360 recorded Purchase Order transactions.
> Per **Policy §3.2**, the quality ceiling framework sets the maximum allowable performance defect threshold for Tier-2 core mechanical parts at **2.50%**. Because the calculated category average of 2.12% remains under this margin, **it does not exceed the critical Tier-2 regulatory policy limit**. However, it is flagged for active corrective action planning due to proximity to the boundary limit.

---

## 🚀 Scaled System Architectural Improvements
To transition this prototype into an enterprise-scale multi-tenant system, the following engineering enhancements are proposed:
1. **Hybrid Structured Retrieval (SQL + Vector Search):** Replace raw string chunking of large tabular data with an autonomous **Pandas/SQL Data Agent**. This prevents semantic search limitations from failing on exact quantitative queries (like mathematical averages, sums, and counts).
2. **Metadata Filtering:** Implement deterministic indexing attributes on the vector collection (e.g., filter specifically by `Region` or `Tier_Level`) to reduce index scan latencies and remove token payload bloat.
3. **Advanced Reranking (Cohere Rerank):** Integrate a secondary reranking layer immediately after initial vector lookup to sort text vectors dynamically based on relevance scoring before payload compilation.
