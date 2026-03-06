# job_search_automation_n8n

An end-to-end autonomous qualification pipeline designed to identify high-signal career opportunities. This system transforms high-volume raw data into a curated High/Rare match bucket (Top 7%) using hybrid semantic scoring, state-machine logic, and zero-maintenance parsing.

---

## 🚀 Phase 1: Lead Scraper & Deduplication
*Focus: Low-latency data acquisition and architectural integrity.*

### **Technical Architecture & Data Handling**
- **JS-Hydration Bypass Engine:** A custom 11-node regex-based parser extracts data directly from raw XML sitemaps. By bypassing headless browser rendering (Puppeteer/Playwright), the system achieves near-zero infrastructure overhead and eliminates maintenance issues related to DOM changes.
- **Unified Schema Normalization:** Implements a "Schema Bridge" via standardized Set Nodes. This maps disparate data streams (LinkedIn API, XML Parsers, Manual G-Sheet Entry) into a strictly typed PostgreSQL structure, ensuring downstream nodes receive a consistent payload.
- **Semantic Conflict Resolution:** Utilizes **95% Cosine Similarity** via `pgvector`. Unlike simple ID-based deduplication, this identifies "multi-posted" or "cross-platform" listings by analyzing the job description's vector DNA, maintaining a 100% unique lead database.
- **Modular Source Scaling:** The ingestion layer is decoupled from the processing logic, allowing for $O(1)$ addition of new sources (e.g., Wellfound, StartupJobs) by simply mapping them to the central schema.
- **Computational Cost-Optimization:** By utilizing a structured Regex-based approach over headless browsers, the system reduces the memory footprint from ~1GB per instance (typical for Puppeteer) to <50MB. This allows for massive parallelization on lightweight self-hosted runners without infrastructure scaling costs.
- **Throttled Batch Processing:** Implements a sequential looping pattern with a 1-item batch size and a 1-second tactical delay. This ensures compliance with API Rate Limits (TPM/RPM) and prevents mid-workflow execution failures during peak ingestion windows.
- **Metadata Granularity:** The 11-node parser performs field-level extraction of 12+ data points, including ISO-8601 timestamps and unique Job IDs, enabling Pre-Vectorization ID Filtering to eliminate redundant API embedding costs.

---

## 🧠 Phase 2: The Hybrid Qualification Engine
*Focus: Multi-model semantic reasoning and mathematical precision.*

### **Advanced Signal Processing**
- **Dual-Stage Filtering Pipeline:**
    - **Stage 1 (Technical Guardrail):** A 70/30 weighted hybrid pass (AI Reasoning + Cosine Similarity) against a vectorized "Technical Anchor." This stage is designed to auto-reject low-level engineering roles that overlap with the desired niche but lack strategic depth.
    - **Stage 2 (The Deep-Dive Scorer):** A parallel 3-branch architecture evaluating **Ideal Similarity**, **Core Pillars**, and **Benefit Logistics**.
- **Linear Range-Stretching Logic:** To counteract the "Neutrality Bias" common in LLM scoring (clustering in the 15%–85% range), the system implements a linear scale correction. By re-mapping observed minimums and maximums to a full $0 \to 100$ spectrum, the system amplifies signal variance, making 'Rare' matches statistically distinct.
- **Hallucination-Proof Architecture:**
    - **Frontier Reasoning:** **Gemini 3.0 Flash** handles high-context variables (Mission/Responsibilities) using detailed scoring tiers and few-shot calibration.
    - **Deterministic Extraction:** **Llama 4 (Groq)** performs high-speed semantic extraction of 33 Boolean benefit indicators. These are fed into a **deterministic Code Node**, moving weighted math out of the LLM and into a "Boring" (consistent) execution environment.
- **State-Driven Self-Healing:** A daily 9:00 AM "State Sync" service identifies leads in transient states (e.g., `passed_title`, `passed_description`) caused by API timeouts. The system utilizes these status flags to resume executions from the point of failure, ensuring 100% fault tolerance.
- **Priority Logic Escalation:** Includes a hard-coded **Strategic Autonomy Override**. If the extraction layer detects high-priority semantic triggers (e.g., "self-reliant" or "creative strategic control"), the lead is assigned a "Priority-Yes" flag, ensuring high-autonomy roles are escalated regardless of logistical scores.
- **Weighted Benefit Summation:** The benefit branch uses a deterministic point system rather than AI guesswork. Each of the 33 benefits is assigned a value (e.g., 50 pts for Remote 5/5), including a "Jackpot" 100-point multiplier for 4-day work-week roles (4x8), ensuring consistent, objective evaluation.

### **Mathematical Constraints & Weighting**
- **Weighted Branch Distribution:** The final 'Rare' score is calculated using an 80/20 logic split: 80% is derived from semantic reasoning (Gemini/Llama), while 20% is tethered to raw vector proximity to the "Ideal" anchor.
- **Sub-Branch Allocation:** Scoring is granularized to reflect personal career priorities: **Mission Alignment (40%)**, **Responsibility Fit (40%)**, and **Logistical Benefits (20%)**.
- **Linear Transformation Ratios:** Based on a 100-sample calibration, the system re-maps tight semantic clusters (typically 0.53 to 0.65) into a high-variance scale. This "stretches" the difference between a 'Mid' and a 'Rare' lead into a 30-point gap, significantly reducing marginal error in classification.

### **Database Logic & Polymorphism**
- **Universal Similarity Service:** Implements a polymorphic PostgreSQL function `check_vibe()`. This single function accepts a `target_key` parameter, allowing the system to toggle between "Technical" and "Ideal" anchor comparisons without duplicating SQL logic or math formulas.
- **Lazy-Loading Data Pipeline:** To optimize Node.js memory limits within n8n, the system utilizes a "Lean Payload" strategy. Heavy 1536D vectors are stripped during workflow transit and only fetched from Supabase via "Lazy Loading" at the exact moment of similarity calculation.

---

## 🔎 Phase 3: Lead Researcher

---

## 🛡️ System Resilience & Monitoring
*Focus: Global exception handling and real-time pipeline observability.*

- **Centralized Error Orchestration:** The system utilizes a dedicated **Global Error Workflow** acting as a universal listener. Any node failure across any phase (Ingestion, Filtering, or Scoring) triggers an immediate context-capture routine and sends a priority notification via Pushover API.

---

## 🛠️ Tech Stack
- **Orchestration:** n8n (Self-hosted)
- **Database:** Supabase (PostgreSQL + `pgvector`)
- **AI Models:** Gemini 3.0 Flash, Llama 4 Maverick on Groq
- **Vector Embeddings:** Gemini embedding 1 (1536D)
- **Notifications:** Pushover API (Priority Alerts)

---

## 📈 System Performance
| Metric | Result |
| :--- | :--- |
| **Noise Reduction** | 71% Auto-Rejected |
| **High/Rare Matches** | TOP 7-8% |
| **Mid and Low Matches** | 10-12% each |
| **Manual Effort Saved** | ~1 Hour Daily |

### ✅ Next Steps (Project Roadmap)
- **Dynamic Persona Onboarding (Voice-to-Config):** Developing a onboarding layer where users record a voice memo to define their values. The system will transcribe the audio to dynamically generate anchors, boolean checklists, and rejection keywords.
- **Vectorized Portfolio Matching:** Cross-referencing "Rare" leads against a database of past projects to calculate a "Relevant Experience" score based on historical output.
- **Dynamic Weight Calibration:** Moving away from hard-coded 80/20 splits to a config-driven model for real-time adjustments to AI influence vs. semantic similarity.

## 📄 License
This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.
