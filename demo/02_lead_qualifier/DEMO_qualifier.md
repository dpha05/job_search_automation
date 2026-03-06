# 🧠 Phase 2: Lead Qualification 
**Hybrid weighted AI lead scoring with linear range-stretching and semantic anchors.**

<br>

### 📊 Highlights
* **71% Rejection Filter:** Two-stage rejection of irrelevant leads: Regex exclusion for titles and hybrid 70/30 weighted filtering with AI + Cosine Similarity for descriptions.
* **Scoring Range Adjustment:** Implemented a linear scale correction to counteract AI "conservatism." By re-mapping the model's natural 15-85% output to a full 0-100% spectrum, the system achieves higher variance and clearer tier separation.
* **Hallucination Prevention:** For important pillars: frontier model **Gemini 3.0** with detailed system prompt, including scoring tiers, few-shot examples and standard value references and for benefits and logistics: lighter **OLlama 4 (Groq)** doing boolean extraction, leaving math logic to consistent code.
* **Self-Healing Status Sync:** Automated daily sync identifies leads in transient statuses (faulty executions) to ensure fault tolerance and minimize data loss.

<br>

---

<br>

![](https://github.com/dpha05/job_search_automation_n8n/raw/main/02_lead_qualifier/assets/demo.png)

<br>

---

<br>

### 🎥 Video Walkthrough (97 Seconds)

[![Video Walkthrough Preview](https://img.youtube.com/vi/iOiFAwF-i9M/0.jpg)](https://www.youtube.com/watch?v=iOiFAwF-i9M)

<br>

---

<br>

### 🖼️ n8n Workflow Screenshots

**Stage 1: Reject Technical Leads:**
![Reject Technical Preview](assets/screenshot_reject.png)

<br>

**Stage 2: Score Filtered Leads:**
![Score Filtered Preview](assets/screenshot_score.png)

<br>

**Setting Global Anchors:**
![Set Global Anchors Preview](assets/screenshot_anchor.png)

<br>

**Sync Daily Status:**
![Sync Status Preview](assets/screenshot_sync.png)

<br>

---

<br>

### 📂 Technical Documentation
* [**Raw JSON Workflows**](workflows/)
* [**Detailed Technical README**](../README.md)

<br>

---

### ✅ Next steps
* **Voice-Driven Onboarding:** Developing a "System Personalization" flow where users can record a voice memo to define their career values (7 petals style). The system will transcribe and distill these into dynamic parameters, boolean benefit checks, priority pillars, positive and negative search keywords, ideal and negative job anchors.
* **Dynamic Weight Calibration:** Based on onboarding dynamically adjust the 70/30 weights and scoring multipliers.
* **Vectorized Portfolio Matching:** Cross-referencing "High" and "Rare" leads against own CV and experiences to calculate a "Relevant Experience" score.

<br>

---
[← Return to Portfolio](https://linktr.ee/dpha05)
