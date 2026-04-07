# # job_search_automation

An end-to-end autonomous qualification pipeline designed to identify high-signal career opportunities. This system transforms high-volume raw data into a curated High/Rare match bucket (Top 7%) using hybrid semantic scoring, state-machine logic, and zero-maintenance parsing.

---
	
<br>

![](https://github.com/dpha05/job_search_automation_n8n/raw/main/01_lead_scraper/assets/demo.png)

<br>

## 📈 System Performance
*Based on a 1,967-sample calibration dataset (Last Update: April 2026)*
| Metric                  | Result            |
|-------------------------|-------------------|
| **Noise Reduction**     | 79% Auto-Rejected |
| **High/Rare Matches**   | TOP 7%            |
| **Daily Volume**        | 53 Leads/Day      |
| **Manual Effort Saved** | ~2.5 Hour Daily   |

<br>

## 🛠️ Tech Stack
- **Orchestration:** n8n (Self-hosted/Docker)
- **Database:** Supabase (PostgreSQL + `pgvector`)
- **AI Models:** Gemini 3.0, Llama 4 on Groq
- **Vector Embeddings:** Gemini embedding 1
- **Frontend/CRM:** NocoDB
- **Deployment:** Docker, GitHub (State-Sync)
- **External APIs**: Pushover (Notification), Jina (Web Scrape), Apify (Lead Scrape), DetectLanguage

<br>

---

<br>

## 🏗️ Decision Logic: The Why

### Phase 1: Lead Scrape & Deduplication

**Problem**
Tools that read websites like humans are slow, expensive, and break when a site changes its design. Additionally, searching across multiple regions creates a database filled with duplicate remote listings.

**Solution**
The system communicates directly with data sources to ensure structural stability and speed. It identifies the unique fingerprint of every job description to automatically remove duplicates across all platforms.

### Phase 2: Hybrid Lead Qualification

**Problem**
AI models often struggle to distinguish between average and best opportunities, giving middle-range scores to every job. Relying on a single check also misses critical details regarding technical requirements and seniority.

**Solution**
The system uses multiple tiers to check specific requirements separately for higher accuracy. It fast-tracks high-value roles that mention preferred tools and uses deterministic math instead of AI guess. This process stretches the scoring range to clearly isolate the most relevant matches.

### Phase 3: AIOps Infrastructure

**Problem**
Workflows can break or get stuck without anyone noticing. Additionally, job links often expire. Without a clear way to see and manage these leads, the entire system becomes difficult to track.

**Solution**
A watchdog audits the system every hour to fix stuck processes and automatically removes inactive job links. A visual dashboard provides acts as CRM to track lead progression and ensure all logic is backed up.

<br>

---

<br>

## 🛠️ Technical Features: The How

### Phase 1: Lead Scrape & Deduplication
*Focus: Low-latency data acquisition and architectural integrity.*

* **XHR Interception (Algolia Bypass):** Utilizes custom HTTP nodes to replicate Network XHR calls (API Key, App ID, and Request Payloads) for platforms like TrueUp and 80k. This bypasses the UI entirely, ensuring zero-maintenance ingestion that is immune to frontend CSS changes.
* **JS-Hydration Bypass:** A custom 11-node regex-based parser extracts data directly from raw XML sitemaps. This reduces the memory footprint from ~1GB (typical for Puppeteer) to <50MB, allowing for massive parallelization on lightweight self-hosted runners.
* **Multi-Region Scaling:** Implements a 4-branch architecture covering Prague (Default), DACH (Germany, Austria, Switzerland), Benelux, and the Nordics to manage localized job market data.
* **Dynamic Temporal Logic:** A dedicated Code Node calculates the `publishedAt` range. It automatically expands the lookback window from 24h to 7 days on Mondays to ensure weekend postings are captured during the Monday morning ingestion spike.
* **Semantic Dedupe:** Utilizes 95% Cosine Similarity via `pgvector`. It identifies "multi-posted" or "cross-platform" listings by analyzing the job description's vector DNA rather than simple IDs, maintaining a 100% unique lead database.
* **Unified Schema Normalization:** A "Schema Bridge" via standardized Set Nodes maps disparate data streams (LinkedIn API, XML Parsers, XHR Payloads) into a strictly typed PostgreSQL structure.
* **Jina Hybrid Fetching:** Implements a Switch node to route URLs by type. Standard sites use HTML fetchers, while proprietary sites are routed through a public Jina endpoint for Markdown conversion to bypass API token limits.
* **Workflow Health Monitoring:** Tracks running vs. completed statuses, timestamps, and `retry_counts` in a dedicated Supabase table (`workflow_health`) for persistent state tracking across the pipeline.
* **Execution Data Tagging:** Injects custom metadata (Job ID, Title, Company) into n8n execution headers, enabling granular filtering and debugging of the 14-workflow chain.
* **Vector Standardization:** Unified the entire pipeline to 1536 dimensions (OpenAI/Gemini standard) to align with Frontier RAG models and ensure high-resolution similarity matches.
* **Throttled Batch Processing:** Implements a sequential looping pattern with a 1-item batch size and a 1-second tactical delay to ensure compliance with API Rate Limits and prevent mid-workflow execution failures.

### Phase 2: Hybrid Lead Qualification
*Focus: Multi-model semantic reasoning and deterministic scoring precision.*

- **The 10-Node Rule:** Transitioned from monolithic 20+ node workflows to a "Micro-Workflow" architecture. Each workflow operates with a single responsibility (Language, Title, Technicality, or Responsibility).
- **Granular Audit Trail:** This modularity enables specific rejection statuses—`rejected_language`, `rejected_title`, `rejected_description`, and `rejected_responsibility`—creating a high-resolution audit trail to identify exactly where leads fail the funnel.
- **The Language & Confidence Gate:** Integrates a language API to automatically reject non-target languages to prevent resource waste.
- **The Fast-Track Logic:** Implements a "Tech-Stack Skip" logic. If a job description explicitly mentions core tools (n8n, Supabase, Clay, Zapier, Make), the system triggers a `skip_description` status, bypassing expensive AI checks and moving straight to final scoring.
- **The Technical Bridge Logic:** Utilizes a 70/30 weighted hybrid pass (AI Reasoning + Cosine Similarity) against a vectorized "Technical Anchor." 
  - **The Job Pivot:** Expanded the "Pass" definition to include Platform Engineering and GTM/Sales Ops. 
  - **Funnel Widening:** Stretched the technical ceiling from 0.65 to 0.70 to ensure high-potential roles aren't prematurely killed.
  - **Seniority Guardrails:** Hard-coded rejections for "Head of," "Director," and "Principal," while retaining "Senior" roles that prioritize skill over tenure.
- **Deterministic Responsibility Analysis:** Uses Llama 4 (Groq) at 0.1 temperature to analyze specific workload distribution. 
  - **The 50% Workload Rule:** A "Yes" is only triggered if core activities (AI Strategy, Orchestration, Consulting, or Mentoring) constitute >50% of the weekly duties. 
  - **The "Imposter" Firewall:** Automatically zeros out the score if the role requires 4+ years of experience in legacy/unrelated stacks. 
- **Final Strategic & Benefit Scoring:** A parallel dual-branch architecture: 
  - **Branch A (The Benefit Map):** Llama 4 (Groq) performs high-speed semantic extraction of 33 Boolean benefit indicators. These are fed into a deterministic Code Node to move weighted math out of the LLM. 
  - **Branch B (Core Pillar Reasoning):** Gemini 3.0 Flash evaluates the Mission Alignment (Open Source, NGO, or Impact-driven) and the Creative Strategy depth (checking for high-priority triggers like "self-reliant" or "strategic control").
- **Linear Range-Stretching Logic:** To counteract "Neutrality Bias" (AI clustering in the 15%–85% range), the system re-maps observed minimums and maximums to a full $0 \to 100$ spectrum, amplifying signal variance and making 'Rare' matches statistically distinct.
- **Weighted Branch Distribution:** The final 'Rare' score uses an 80/20 logic split: 80% semantic reasoning (Gemini/Llama) and 20% raw vector proximity to the "Ideal" anchor.
- **Sub-Branch Allocation:** Scoring reflects personal career priorities: Mission Alignment (40%), Responsibility Fit (40%), and Logistical Benefits (20%).
- **Priority Logic Escalation**: Includes a hard-coded Strategic Autonomy Override. Semantic triggers like "self-reliant" or "creative strategic control" trigger a "Priority-Yes" flag, escalating the lead regardless of other scores.
- **Weighted Benefit Summation:** Uses a deterministic point system for 33 benefits (e.g., 50 pts for Remote), including a "Jackpot" multiplier for 4-day work-week roles (4x8).
- **Universal Similarity Service:** A polymorphic PostgreSQL function `check_vibe()` that accepts a `target_key` parameter to toggle between "Technical" and "Ideal" anchors without duplicating SQL logic.
- **Refined Global Anchors:** Updated anchors to explicitly exclude "Wrong Stacks" (PHP, .NET, Swift) and include GTM/Pre-sales elements to reduce vector noise.

### Phase 3: AIOps & Infrastructure
*Focus: Pipeline observability, self-healing resilience, and lead lifecycle management.*

- **Success Monitoring Daemon:** A dedicated `sync_last_success` workflow audits the `workflow_health` table every hour to ensure system uptime.
- **Deterministic Recovery:** The watchdog identifies "silent failures" by flagging workflows that are stuck (running >90 mins) or unfired (missing daily execution), rather than relying on standard error triggers.
- **Exponential Backoff Lite:** Implements a `retry_count` (capped at 3) to prevent infinite loops and resource exhaustion on broken nodes.
- **Tag-Based Targeting:** Transitioned from brittle name-based execution to a robust **Tagging System** (`PROD`, `TIME_SENSITIVE`). This enables centralized management of 14+ workflows without issues arising from renaming or cloning.
- **3-Tiered URL Pruning:** A sophisticated verification logic that prevents applications to "ghost" roles by checking link activity on a rolling schedule:
  - **Tier 1 (New):** Checked every 2 days.
  - **Tier 2 (Mid):** Checked every 4 days.
  - **Tier 3 (Old):** Checked every 7 days.
- **HTML Signature Detection:** Bypasses basic 404 status checks by analyzing the page content for specific "Applied," "Expired," or "Inactive" HTML patterns on LinkedIn and StartupJobs.
- **GitHub State-Sync:** A custom n8n-to-GitHub bridge that automatically backs up the JSON configuration of all `PROD` tagged workflows to a private repository.
- **Smart Upsert Logic:** Handles "Create vs. Update" operations by catching GitHub API errors, ensuring the cloud backup remains the "Single Source of Truth."
- **Docker Virtualization:** The entire stack is containerized via Docker, enabling environment isolation and facilitating a future Dev/Prod deployment split.
- **Batch Rerun Engine:** A manual ingestion workflow designed to accept "Messy JSON" arrays, allowing for the re-processing of legacy data.
- **Logic Stress-Testing:** Enables "replay" of old leads through updated scoring filters to validate if architectural changes (e.g., the 0.65 -> 0.70 technical shift) capture the intended high-signal opportunities.
- **Visual CRM Integration:** Utilizes **NocoDB** as a high-level frontend for the Supabase backend, providing a spreadsheet-style interface for lead management.
- **Kanban Progression:** Implements a visual pipeline for lead status (e.g., *Sourced → Scored → Applied → Interview*), providing the project management layer missing from raw database views.
- **Hybrid Data Logic:** Leverages NocoDB for front-end formulas and quick edits while maintaining Supabase for heavy-duty vector operations and relational integrity.

<br>

---

<br>

## 📂 Resources
* [**Raw JSON Workflows**](https://github.com/dpha05/job_search_automation/tree/main/workflows)
* [**Video Explanation**](https://www.youtube.com/watch?v=P3Jgx5Q_dyU)
