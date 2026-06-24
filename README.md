# Agentic AI for 5G–6G Networks
### AI-RAN · O-RAN SMO · Agentic AI — Personal Research Repository

> I work in 5G/6G technology. This repo is my structured knowledge base as I learn the AI/ML side of the RAN stack — from foundational agentic AI concepts to O-RAN SMO architecture, NVIDIA AI-RAN hardware, and agentic closed-loop control systems.

---

## Table of Contents

- [What This Repo Covers](#what-this-repo-covers)
- [Repo Structure](#repo-structure)
- [Section 01 — Agentic AI Foundations](#section-01--agentic-ai-foundations)
- [Section 02 — O-RAN Architecture & SMO](#section-02--o-ran-architecture--smo)
- [Section 03 — AI-RAN (NVIDIA & Industry)](#section-03--ai-ran-nvidia--industry)
- [Section 04 — Agentic AI Applied to RAN](#section-04--agentic-ai-applied-to-ran)
- [Section 05 — 6G & AI-Native Networks](#section-05--6g--ai-native-networks)
- [Section 06 — Agentic Design Patterns for Telecom](#section-06--agentic-design-patterns-for-telecom)
- [Section 07 — Governance, Safety & Ethics](#section-07--governance-safety--ethics)
- [Resources — Papers, Specs & Tools](#resources--papers-specs--tools)
- [Reading Roadmap](#reading-roadmap)
- [Glossary of Key Terms](#glossary-of-key-terms)

---

## What This Repo Covers

This is not a course or a tutorial. It is a **personal research repository** — notes, summaries, and curated links built while reading primary sources. The three main pillars are:

**AI-RAN** — Running AI inference workloads (LLM serving, computer vision, prediction models) on the same compute hardware as the 5G RAN baseband. This is the hardware convergence story, primarily driven by NVIDIA Aerial and the AI-RAN Alliance.

**O-RAN SMO & RIC** — The software intelligence layer of Open RAN. The Service Management and Orchestration (SMO) framework hosts the Non-RT RIC, which runs rApps for long-term analytics and policy. The Near-RT RIC runs xApps for 10ms–1s closed-loop RAN control. The interfaces that connect them — A1, E2, O1, O2, R1 — are what make this programmable.

**Agentic AI** — Moving beyond static ML models (train once, deploy, run inference) toward autonomous AI agents that plan, use tools, reflect on outcomes, and take actions in closed loops. Applied to RAN, this means agents that can read KPIs, diagnose problems, generate A1 policies, trigger xApp actions, and verify outcomes — without a human in the loop for every decision.

These three things are converging. By 6G, they will be inseparable.

---

## Repo Structure

```
agentic-ai-5g-6g-research/
│
├── 00-resources/               ← All source PDFs and downloaded papers
│   ├── papers/                 ← arXiv and IEEE papers (named AuthorYear_Title.pdf)
│   ├── specs/                  ← O-RAN Alliance specs, 3GPP documents
│   └── whitepapers/            ← Industry whitepapers (NVIDIA, Ericsson, Nokia etc.)
│
├── 01-agentic-ai-foundations/  ← What is agentic AI, how agents reason
├── 02-oran-smo-architecture/   ← O-RAN stack, SMO, Non-RT RIC, Near-RT RIC
├── 03-ai-ran/                  ← AI-RAN hardware, NVIDIA Aerial, C-RAN vs D-RAN
├── 04-agentic-ai-for-ran/      ← LLM agents applied to RAN control loops
├── 05-6g-ai-native/            ← 6G architecture, AI-native paradigms
├── 06-agentic-design-patterns/ ← ReAct, multi-agent patterns, planning loops
├── 07-governance-safety/       ← Risk, ethics, regulatory frameworks
│
└── README.md
```

Each section folder follows this internal structure:

```
01-agentic-ai-foundations/
├── notes/
│   ├── 01-agentic-vs-generative-ai.md
│   ├── 02-react-architecture.md
│   └── ...
├── summaries.md    ← one-paragraph summary per paper with key takeaway
└── open-questions.md  ← gaps and things I want to explore further
```

---

## Section 01 — Agentic AI Foundations

**Goal of this section:** Understand what separates an agentic AI system from a standard LLM or ML model. By the end, you should be able to explain: what makes a system "agentic", what ReAct is, how multi-agent systems coordinate, and what trustworthy agentic AI requires.

### Core Concepts to Build

**Agentic AI vs Generative AI**
Generative AI responds. Agentic AI acts. The key differences: an agent has a goal, a plan to achieve it, tools it can invoke, memory of past actions, and a feedback loop to evaluate outcomes. An LLM answering a question is not agentic. An LLM that reads network KPIs, decides a beam configuration is suboptimal, sends an API call to update it, and verifies the KPI improved — that is agentic.

**The ReAct Architecture**
ReAct (Reason + Act) is the foundational loop in most agentic systems. The agent alternates between: (1) reasoning about the current state, (2) selecting a tool/action to invoke, (3) observing the result, then reasoning again. This loop continues until the goal is reached or a stopping condition triggers. In a RAN context: Reason = analyze E2 KPI telemetry; Act = send A1 policy or E2 control message; Observe = check if SINR/throughput improved.

**Core Components of an Agent**
- **Planning module** — breaks a high-level goal (e.g., "reduce handover failure rate below 0.5%") into sub-tasks
- **Memory** — short-term (current episode context), long-term (past actions and their outcomes, stored in vector DB or structured log)
- **Tool use** — API calls, database queries, code execution, E2 interface messages
- **Reflection** — self-evaluation of whether the action taken actually improved the situation

**Multi-Agent Systems**
Single agents hit limits: context length, specialization, parallelism. Multi-agent systems assign specialized agents to subtasks (e.g., one agent monitors DL throughput, another handles mobility, a coordinator synthesizes their outputs). Coordination patterns: hierarchical (orchestrator + workers), peer-to-peer (agents negotiate), sequential pipelines (output of one feeds input of next).

**Cognitive Architectures for Language Agents**
Research classifies agent architectures by memory type (in-context, external, parametric), action space (text output, API calls, code), and decision strategy (chain-of-thought, tree-of-thought, Monte Carlo tree search). Understanding this taxonomy helps you evaluate papers: when a paper claims "agentic", what exactly is the agent doing?

**Trustworthy Agentic AI**
As agents take real actions with real consequences (changing network configurations, allocating spectrum), trust becomes critical. Key dimensions: safety (won't take catastrophic actions), reliability (consistent behavior under distribution shift), interpretability (can explain why it took an action), controllability (human can override or pause). These are not nice-to-haves in telecom — they are regulatory requirements.

### Papers to Read for This Section

| Paper | Where to get it | Why it matters |
|-------|----------------|----------------|
| Agentic AI: A Survey of Architectures, Taxonomies and Applications | [arXiv:2309.02427](https://arxiv.org/abs/2309.02427) | Broadest survey; read first to get the full landscape |
| ReAct: Synergizing Reasoning and Acting in Language Models | [arXiv:2210.03629](https://arxiv.org/abs/2210.03629) | The foundational ReAct paper — essential |
| From System 1 to System 2: A Survey of Reasoning Large Language Models | [arXiv:2502.17419](https://arxiv.org/abs/2502.17419) | How LLMs reason step by step; connects to agentic planning |
| Towards Trustworthy Agentic AI: A Comprehensive Survey | [arXiv:2502.09920](https://arxiv.org/abs/2502.09920) | Safety and governance of agentic systems |
| Cognitive Architectures for Language Agents | [arXiv:2309.02427](https://arxiv.org/abs/2309.02427) | Taxonomy of how agents are built |
| Enhancing AI Systems with Agentic Workflow Patterns | [arXiv:2601.12560](https://arxiv.org/abs/2601.12560) | Practical patterns: reflection, planning, tool use |
| Model AI Governance Framework for Agentic AI | [IMDA Singapore, 2024](https://www.imda.gov.sg/resources/press-releases-factsheets-and-speeches/press-releases/2024/imda-and-pdpc-advance-ai-governance-with-updated-framework) | Policy framework — cross-reference with Section 07 |

### Key External References

- **What is Agentic AI — KoreAI explainer:** https://kore.ai/blog/what-is-agentic-ai/
- **Agentic AI vs GenAI — Databricks:** https://www.databricks.com/glossary/agentic-ai
- **LangGraph multi-agent workflows:** https://langchain-ai.github.io/langgraph/concepts/multi_agent/
- **LangChain agent framework docs:** https://python.langchain.com/docs/concepts/agents/
- **AutoGen (Microsoft) multi-agent framework:** https://microsoft.github.io/autogen/

---

## Section 02 — O-RAN Architecture & SMO

**Goal of this section:** Understand the full O-RAN stack deeply enough to know exactly where AI/ML agents can be inserted, at which timescale, through which interface, and with what authority over the RAN.

### The O-RAN Stack — Layer by Layer

**O-RAN disaggregation**
Traditional RAN had monolithic base stations (BBU from one vendor, RRU from same vendor, software locked). O-RAN splits this into: O-RU (Radio Unit — fronthaul), O-DU (Distributed Unit — L1/L2 processing), O-CU-CP (Centralized Unit — Control Plane, RRC/PDCP), O-CU-UP (User Plane). These are now separate software-defined functions from potentially different vendors, connected by open interfaces (eCPRI for fronthaul, F1 between DU and CU, E1 between CU-CP and CU-UP).

**SMO — Service Management and Orchestration**
The SMO is the top-level management layer. It handles: network configuration (via O1 interface to O-DU, O-CU, O-RU), infrastructure management (via O2 interface to O-Cloud), and hosting the Non-RT RIC. The SMO is where long-loop AI decisions happen — minutes, hours, or longer timescales.

**Non-RT RIC and rApps**
The Non-RT RIC is the AI/ML brain embedded within the SMO. It operates on control loops greater than 1 second (typically minutes to hours). It trains ML models, runs batch analytics on historical KPIs, and generates A1 policies pushed down to the Near-RT RIC. rApps are third-party applications running inside the Non-RT RIC via the R1 interface. Examples: energy saving rApp (identifies which cells to sleep based on traffic patterns), predictive maintenance rApp (flags cells likely to fail based on KPI degradation trends), interference management rApp (generates A1 policies for inter-cell interference coordination).

**Near-RT RIC and xApps**
The Near-RT RIC operates on 10ms–1s control loops. It receives A1 policies from the Non-RT RIC (via A1 interface) and directly controls O-CU and O-DU via the E2 interface. xApps are the applications running here. The E2 interface uses service models: E2SM-KPM (Key Performance Metrics subscription — xApp subscribes to receive UE-level and cell-level KPIs in near real time), E2SM-RC (RAN Control — xApp sends control messages to change RAN parameters like handover thresholds, scheduling weights, beam selection).

**Interface Summary**

| Interface | Between | Direction | Timescale |
|-----------|---------|-----------|-----------|
| O1 | SMO ↔ O-CU/O-DU/O-RU | Management/config | Minutes to hours |
| O2 | SMO ↔ O-Cloud | Infrastructure lifecycle | Minutes to hours |
| A1 | Non-RT RIC → Near-RT RIC | Policy push | Seconds to minutes |
| R1 | rApps ↔ Non-RT RIC | rApp service exposure | — |
| E2 | Near-RT RIC ↔ O-CU/O-DU | Near-real-time control | 10ms–1s |
| Y1 | Near-RT RIC → Y1 consumers | RAN analytics exposure | — |
| Fronthaul | O-DU ↔ O-RU | Radio data plane | Sub-ms |

### Papers to Read for This Section

| Paper | Where to get it | Why it matters |
|-------|----------------|----------------|
| Understanding O-RAN: Architecture, Interfaces, Algorithms, Security (Polese et al.) | [arXiv:2202.01032](https://arxiv.org/abs/2202.01032) | **The single best O-RAN survey. Read this first.** |
| O-RAN xApps: Survey and Research Challenges | [PDF via mentis.info](https://mentis.info/wp-content/uploads/2025/02/xApps_COMNET_2025.pdf) | Deep dive into xApp architecture, E2SM, RL-based control |
| RAN Intelligent Controller: From Open-Source to Real-World Validation | [ScienceDirect 2024](https://www.sciencedirect.com/science/article/pii/S2405959524000067) | Open-source Non-RT and Near-RT RIC implementation walkthrough |
| OrchestraN: Network Automation through Orchestrated Intelligence in O-RAN | Search IEEE or arXiv: D'Oro, Bonati, Polese, Melodia | Multi-controller coordination in O-RAN |
| cop(o_ran_ai_ml).pdf | In `00-resources/` | O-RAN Alliance co-publication on AI/ML workflow |

### Key External References

- **O-RAN Alliance official specifications (WG1–WG11):** https://www.o-ran.org/specifications
- **O-RAN Alliance resources & whitepapers:** https://www.o-ran.org/o-ran-resources
- **O-RAN Software Community (OSC) — open-source RIC implementation:** https://o-ran-sc.org/
- **OpenAICellular (OAIC) — full-stack open O-RAN research platform with tutorials:** https://openaicellular.github.io/oaic/
- **Orange ns-O-RAN-flexric — RIC Testing as a Platform (E2AP v1.01, KPM v3, RC v1.03):** https://github.com/Orange-OpenSource/ns-O-RAN-flexric
- **GitHub topic: oran** — browse active community repos: https://github.com/topics/oran
- **GitHub topic: ric** — xApp, DRL, multi-slice repos: https://github.com/topics/ric
- **Ericsson whitepaper — SMO enabling intelligent RAN operations:** https://www.ericsson.com/en/reports-and-papers/white-papers/smo-enabling-intelligent-ran-operations
- **NXGConnect — deep technical explainer on xApps and rApps:** https://www.nxgconnect.com/post/ran-intelligence-with-xapps-and-rapps

---

## Section 03 — AI-RAN (NVIDIA & Industry)

**Goal of this section:** Understand AI-RAN as a hardware and deployment concept — separate from O-RAN's software intelligence. AI-RAN means running AI inference workloads alongside the 5G baseband on the same GPU/CPU compute pool at the cell site.

### What AI-RAN Actually Means

Traditional 5G gNBs use dedicated ASICs or FPGAs for baseband processing (L1 — FFT, channel estimation, LDPC decode). AI-RAN replaces or augments this with GPU-based general-purpose compute that can run both the 5G signal processing stack AND AI inference workloads (LLM serving, computer vision, recommendation systems) simultaneously on shared hardware. The value proposition: monetize idle cell-site compute by serving AI applications during off-peak traffic periods, while the same hardware runs full 5G throughput during peak hours.

**NVIDIA's Role**
NVIDIA Aerial is the software platform: cuBB (CUDA Baseband Library) runs the full 5G L1 on GPU, cuVNF handles the 5G core functions, and ARC (AI-RAN Controller) integrates the intelligence layer. The hardware: H100/H200/B200 GPUs co-located at cell sites or in centralized data centers (C-RAN), connected via Spectrum-X networking fabric and BlueField DPUs for data plane acceleration.

**C-RAN vs D-RAN in AI-RAN Context**
D-RAN (Distributed RAN): compute sits at each cell site. Lower fronthaul requirements, but AI workloads are limited by per-site hardware. C-RAN (Centralized RAN): baseband processing pooled in a central location. Enables larger GPU clusters, better resource sharing between cells, but requires ultra-low latency fronthaul (eCPRI over fiber, sub-100µs). AI-RAN at scale almost certainly requires C-RAN to pool enough GPU compute.

**Why This Matters for Agentic AI**
An agentic system running in the SMO or Non-RT RIC needs compute to run LLM inference. If that compute is co-located with the RAN (AI-RAN architecture), the agent can operate with lower latency, tighter integration with the RAN control loop, and avoid sending sensitive network data to external cloud infrastructure.

### Papers and Sources to Read for This Section

| Source | Where to get it | Why it matters |
|--------|----------------|----------------|
| AI-RAN Alliance Vision and Mission Whitepaper (2024) | [ai-ran.org/publications](https://ai-ran.org/publications/) | The definitive industry position on AI-RAN |
| Integrating AI/ML in Open-RAN: Overcoming Challenges | [ai-ran.org/publications](https://ai-ran.org/publications/) | Practical challenges of combining AI and RAN |
| The Interplay of AI-and-RAN: Dynamic Resource Allocation for Converged 6G Platform | [arXiv:2503.07420](https://arxiv.org/abs/2503.07420) | Resource sharing between AI and RAN workloads |
| NVIDIA AI Aerial launches to optimize wireless networks | [NVIDIA Blog, Sep 2024](https://developer.nvidia.com/blog/nvidia-ai-aerial-launches-to-optimize-wireless-networks-and-deliver-new-ai-experiences/) | NVIDIA's official AI-RAN platform announcement |
| Deploy AI-RAN at cell sites with NVIDIA ARC-Compact | [NVIDIA Developer, May 2025](https://developer.nvidia.com/blog/deploy-ai-ran-at-cell-sites-with-nvidia-arc-compact/) | Practical deployment guide for AI-RAN hardware |
| SoftBank + Red Hat: AI-RAN data center power optimization | [SoftBank Press, Mar 2025](https://www.softbank.jp/en/corp/news/press/sbkk/2025/20250310_01/) | Real-world AI-RAN deployment case study |
| 2025_AI-RAN_FAQ.pdf | In `00-resources/` | Quick reference FAQ — industry consensus answers |
| open-airan24-final14.pdf | In `00-resources/` | Open AI-RAN 2024 workshop proceedings |

### Key External References

- **AI-RAN Alliance — official site, publications, membership:** https://ai-ran.org/
- **NVIDIA Aerial SDK documentation:** https://developer.nvidia.com/aerial-sdk
- **NVIDIA Spectrum-X networking platform:** https://www.nvidia.com/en-us/networking/spectrumx/

---

## Section 04 — Agentic AI Applied to RAN

**Goal of this section:** This is where the two streams (Agentic AI + O-RAN) converge. Understand the specific papers proposing agentic architectures for RAN control, what the agent's action space is, which O-RAN layer they operate at, and how they differ from traditional RL-based xApps.

### Why Traditional ML-RL is Not Enough

Most existing O-RAN AI/ML work uses supervised learning or reinforcement learning deployed as xApps: train a model offline, deploy it, it runs inference on KPI subscriptions and sends E2 control messages. Problems with this approach:

1. **Brittle to distribution shift** — RL agents trained in simulation fail when real network conditions differ
2. **No natural language interface** — operator cannot express intent in human terms; must retrain or reconfigure
3. **Single-objective** — most RL xApps optimize one KPI; multi-objective tradeoffs require retraining
4. **No reasoning trace** — cannot explain why a decision was made; impossible to audit
5. **No self-correction** — if an action degrades the network, the agent keeps following the same policy until retrained

Agentic AI addresses all five: LLM-based planners can handle novel situations, express their reasoning in natural language, balance multiple objectives via natural language goal specification, and detect when their actions are not working and adapt.

### Key Papers in This Space

| Paper | arXiv | O-RAN Layer | Agent Type | Key Contribution |
|-------|-------|-------------|------------|-----------------|
| Agentic AI-RAN: Intent-Driven, Explainable, Self-Evolving Open RAN Intelligence | [2602.24115](https://arxiv.org/abs/2602.24115) | SMO + Non-RT + Near-RT RIC | Goal-driven multi-agent | Defines agentic primitives for each O-RAN control layer; multi-cell simulation vs DRL baselines |
| Edge Agentic AI Framework for Autonomous Network Optimisation in O-RAN | [2507.21696](https://arxiv.org/abs/2507.21696) | Edge / Near-RT RIC | LLM-driven edge agent | AutoGen-based multi-agent coordination for O-RAN at the edge |
| RAN Cortex: Memory-Augmented Intelligence for Context-Aware O-RAN | [2505.07842](https://arxiv.org/abs/2505.07842) | SMO / Non-RT RIC | Memory-augmented agent | Persistent memory for RAN agents (analogous to RAG for telecom); multi-timescale memory |
| MobiLLM: Agentic AI for Closed-Loop Threat Mitigation in 6G Open RANs | [2509.21634](https://arxiv.org/abs/2509.21634) | Non-RT + Near-RT RIC | LLM-based security agent | Security-focused agentic framework; threat detection + automated mitigation |
| LLM-HRIC: LLM-Empowered Hierarchical RAN Intelligent Control | Search: Bao, Yun, Lee, Quek (2025) | Multi-layer RIC | Hierarchical LLM agents | Hierarchical LLM control across Non-RT and Near-RT timescales |
| Agentic AI meets Neural Architecture Search: Proactive Traffic Prediction for AI-RAN | [2510.00851](https://arxiv.org/abs/2510.00851) | Near-RT RIC / AI-RAN | NAS + Agentic AI | Proactive traffic prediction using neural architecture search within an agentic loop |
| LLM-Driven Agentic AI for Enhanced O-RAN Resilience | Search: Wu, Farooq, Chen (Authorea 2025) | Near-RT RIC | LLM agent | Resilience and fault recovery using LLM agents in O-RAN |

### What to Look for When Reading These Papers

For each paper, extract and document:
- **What is the agent?** (single LLM, multi-agent system, hierarchical?)
- **What is the action space?** (A1 policy, E2 control, O1 config, or just text output?)
- **Which O-RAN layer?** (SMO / Non-RT RIC / Near-RT RIC / CU-DU)
- **What timescale?** (>1s, 10ms–1s, <10ms)
- **What is the baseline it beats?** (RL xApp, static policy, heuristic?)
- **What dataset or simulator?** (ns-O-RAN, OpenAirInterface, real network?)
- **What gap does it leave open?** (this feeds your own research ideas)

---

## Section 05 — 6G & AI-Native Networks

**Goal of this section:** Understand what "AI-native" means at the 6G architecture level — not just AI running on top of the network (as in O-RAN xApps), but AI baked into the protocol stack, the air interface, and the network functions themselves.

### AI-Native vs AI-Assisted

**AI-assisted (5G O-RAN approach):** The network protocol stack is unchanged. AI/ML models are deployed as xApps/rApps that observe network state and send configuration changes via defined interfaces (A1, E2). AI is an add-on layer.

**AI-native (6G vision):** AI is part of the protocol definition. Examples: AI-designed waveforms (learned constellations, AI-compressed channel feedback CSI), AI channel estimation replacing DMRS-based algorithms, end-to-end learned encoding/decoding replacing LDPC, AI-native beam management, AI-native resource scheduler embedded in L2. The network does not just use AI — it cannot function without it.

### Key Standards and Vision Documents

| Source | Where to get it | What it covers |
|--------|----------------|----------------|
| Bharat 6G Vision Document | [DoT India, 2023](https://dot.gov.in/sites/default/files/Bharat%206G%20Vision%20Document_0.pdf) | India's official 6G vision: AI-native, green, inclusive |
| 3GPP Release 18 — AI/ML for NR Air Interface | [3GPP TR 38.843](https://www.3gpp.org/ftp/Specs/archive/38_series/38.843/) | CSI feedback compression, beam management, positioning — AI in Rel-18 |
| 3GPP Release 19 — Study on AI/ML for 5GS | [3gpp.org](https://www.3gpp.org/release-19) | Expanding AI into core network functions |
| Nokia Bell Labs — Technology Innovations for 6G System Architecture | [Nokia Whitepaper, 2022](https://www.nokia.com/networks/insights/6g-white-paper/) | Architecture vision with AI-native layers |
| ITU-R IMT-2030 Framework | [ITU-R M.2160](https://www.itu.int/rec/R-REC-M.2160/en) | International 6G standardization framework |
| Latest Agentic+6G convergence paper | [arXiv:2605.23989](https://arxiv.org/abs/2605.23989) | AI-native 6G with agentic control systems |

### Key External References

- **DoT India 6G portal:** https://dot.gov.in/6g
- **6G-IA (Europe 6G flagship):** https://6g-ia.eu/
- **Next G Alliance (USA 6G):** https://www.nextgalliance.org/
- **Samsung 6G whitepaper collection:** https://research.samsung.com/6G

---

## Section 06 — Agentic Design Patterns for Telecom

**Goal of this section:** Move from "what is an agentic system" to "how do I design one for a specific RAN use case". These are the reusable patterns — the building blocks.

### The Core Patterns

**Pattern 1 — Reflection**
Agent takes an action, observes the outcome, compares to expected outcome, and revises its plan. In RAN: agent sends an A1 policy to reduce handover failures → checks E2 KPM report after 30 seconds → if HO failure rate did not improve, agent reflects on why and tries a different policy parameter.

**Pattern 2 — Tool Use**
Agent has access to a set of tools it can invoke: read KPI from Non-RT RIC data lake, query historical performance, call E2 interface to send control message, look up spec documentation, run a simulation. Tool use is what gives an LLM-based agent real-world grounding beyond its training data.

**Pattern 3 — Planning**
Given a high-level goal ("improve coverage in sector 3 during peak hours"), the agent breaks it into: (1) identify which KPIs indicate poor coverage, (2) retrieve current KPI values, (3) identify root cause candidates, (4) select candidate action, (5) predict outcome, (6) execute, (7) verify. Long-horizon planning is what separates agentic systems from single-shot ML inference.

**Pattern 4 — Multi-Agent Coordination**
Different agents with different specializations coordinate to handle complex tasks. In O-RAN: a monitoring agent continuously watches KPI subscriptions, a diagnosis agent interprets anomalies, a planning agent generates candidate actions, a safety agent checks actions against guardrails before execution, a verification agent monitors post-action KPIs. This mirrors the Non-RT RIC / Near-RT RIC layered control architecture.

**Pattern 5 — Memory-Augmented Reasoning**
Agent maintains external memory beyond the LLM context window: a vector database of past network states and the actions that resolved them, structured logs of policy decisions and their outcomes. When a new situation arises, the agent retrieves similar past episodes and uses them to reason by analogy. This is what RAN Cortex (arXiv:2505.07842) proposes for O-RAN.

**Pattern 6 — Intent-Driven Control**
Human operator specifies intent in natural language: "prioritize voice quality in the stadium sector during the match". Agent translates this into: specific KPI targets, appropriate A1 policy parameters, xApp action constraints. This removes the need for operators to manually specify threshold values — the agent interprets intent and handles the parameter translation.

### The 12 Agentic Design Patterns (from arXiv:2601.19752)

The paper by Belcak et al. (2025) formally enumerates 12 patterns. Document your notes on each in this section. The core 6 above map to several of them. The additional ones cover: subagent delegation, self-consistency checking, constitutional constraints, chain-of-thought prompting, retrieval augmentation, and prompt chaining.

### Papers and References

| Paper | Where to get it | Why it matters |
|-------|----------------|----------------|
| Agentic Design Patterns (Belcak et al., 2025) | [arXiv:2601.19752](https://arxiv.org/abs/2601.19752) | Formal taxonomy of 12 agentic design patterns |
| Small Language Models are the Future of Agentic AI | [arXiv:2601.19752 ref](https://arxiv.org/abs/2601.19752) | Why not every agent needs GPT-4; SLMs for edge deployment |
| LangGraph concepts — multi-agent systems | [LangGraph docs](https://langchain-ai.github.io/langgraph/concepts/multi_agent/) | Practical implementation of multi-agent patterns |
| AutoGen: Enabling Next-Gen LLM Applications | [arXiv:2308.08155](https://arxiv.org/abs/2308.08155) | Microsoft's multi-agent conversation framework |

---

## Section 07 — Governance, Safety & Ethics

**Goal of this section:** When an agent autonomously changes live network configurations affecting millions of users, governance is not optional. This section covers the risk landscape, technical safety mechanisms, ethical frameworks, and regulatory context.

### The Risk Landscape for Autonomous RAN AI

**Autonomous AI in telecom is categorically different from autonomous AI in other domains.** A misconfigured beam or a rogue handover parameter can cause service outages affecting hospitals, emergency services, and critical infrastructure. The risks:

- **Cascading failures** — an agent optimizes locally, creating interference that degrades a neighboring cell, which triggers another agent's response, creating an oscillation loop
- **Distribution shift at deployment** — agent trained on historical data makes wrong decisions when network conditions change (new buildings, seasonal traffic patterns, new UE types)
- **Opacity** — if the agent cannot explain why it changed a parameter, operators cannot diagnose whether it made a mistake
- **Misaligned objectives** — agent optimizes the KPI it was given (e.g., DL throughput) while degrading other KPIs not in its objective (e.g., latency, energy consumption)
- **Adversarial manipulation** — in O-RAN, E2 KPI data comes from the RAN; a compromised or spoofed gNB could manipulate the agent's input

### Technical Safety Mechanisms

- **Guardrails** — hard constraints on the action space: agent cannot disable more than N cells simultaneously, cannot change handover threshold beyond ±X dB from baseline, cannot reduce transmit power below coverage threshold
- **Human-in-the-loop gates** — actions above a certain impact score require operator confirmation before execution
- **Rollback mechanisms** — every action the agent takes is logged with the full network state; if KPIs degrade after an action, automatic rollback to the pre-action configuration
- **Shadow mode** — agent runs in parallel with the existing control loop, generating recommendations without executing them; operator validates before enabling autonomous mode
- **A/B testing** — new agent policies applied to a subset of cells; compared against control cells before full rollout

### Papers and References

| Paper | Where to get it | Why it matters |
|-------|----------------|----------------|
| Model AI Governance Framework for Agentic AI | [IMDA Singapore, 2024](https://www.imda.gov.sg/resources/press-releases-factsheets-and-speeches/press-releases/2024/imda-and-pdpc-advance-ai-governance-with-updated-framework) | Comprehensive policy framework for agentic AI |
| Towards Trustworthy Agentic AI | [arXiv:2502.09920](https://arxiv.org/abs/2502.09920) | Technical safety strategies: guardrails, sandboxing, verification |
| MobiLLM (security section) | [arXiv:2509.21634](https://arxiv.org/abs/2509.21634) | Threat mitigation in 6G O-RAN agentic systems |
| O-RAN Security Focus — WG11 | [o-ran.org/specifications](https://www.o-ran.org/specifications) | Official O-RAN security specifications |
| mgf-for-agentic-ai.pdf | In `00-resources/` | Multi-goal framework — handling conflicting objectives safely |

### Key External References

- **NIST AI Risk Management Framework:** https://www.nist.gov/system/files/documents/2023/01/26/AI%20RMF%201.0.pdf
- **EU AI Act — high-risk AI systems (telecom qualifies):** https://www.europarl.europa.eu/topics/en/article/20230601STO93804/eu-ai-act-first-regulation-on-artificial-intelligence
- **ITU-T Focus Group on AI for 5G (FG-AN):** https://www.itu.int/en/ITU-T/focusgroups/ai4nn/Pages/default.aspx

---

## Resources — Papers, Specs & Tools

Complete reference list across all sections.

### Foundational Survey Papers

| Paper | arXiv / DOI | Year |
|-------|-------------|------|
| Understanding O-RAN: Architecture, Interfaces, Algorithms, Security (Polese et al.) | [arXiv:2202.01032](https://arxiv.org/abs/2202.01032) | 2023 |
| Agentic AI: A Survey of Architectures, Taxonomies and Applications | [arXiv:2309.02427](https://arxiv.org/abs/2309.02427) | 2023 |
| O-RAN xApps: Survey and Research Challenges | [COMNET 2025 PDF](https://mentis.info/wp-content/uploads/2025/02/xApps_COMNET_2025.pdf) | 2025 |
| Towards Trustworthy Agentic AI: A Comprehensive Survey | [arXiv:2502.09920](https://arxiv.org/abs/2502.09920) | 2025 |
| ReAct: Synergizing Reasoning and Acting in Language Models | [arXiv:2210.03629](https://arxiv.org/abs/2210.03629) | 2022 |

### Key Agentic AI + RAN Papers

| Paper | arXiv | Year |
|-------|-------|------|
| Agentic AI-RAN: Intent-Driven, Explainable, Self-Evolving Open RAN | [arXiv:2602.24115](https://arxiv.org/abs/2602.24115) | 2026 |
| Edge Agentic AI Framework for Autonomous Network Optimisation in O-RAN | [arXiv:2507.21696](https://arxiv.org/abs/2507.21696) | 2025 |
| RAN Cortex: Memory-Augmented Intelligence for O-RAN | [arXiv:2505.07842](https://arxiv.org/abs/2505.07842) | 2025 |
| MobiLLM: Agentic AI for Closed-Loop Threat Mitigation in 6G Open RANs | [arXiv:2509.21634](https://arxiv.org/abs/2509.21634) | 2025 |
| Agentic AI meets NAS: Proactive Traffic Prediction for AI-RAN | [arXiv:2510.00851](https://arxiv.org/abs/2510.00851) | 2025 |
| The Interplay of AI-and-RAN: Dynamic Resource Allocation for Converged 6G | [arXiv:2503.07420](https://arxiv.org/abs/2503.07420) | 2025 |
| From System 1 to System 2: A Survey of Reasoning LLMs | [arXiv:2502.17419](https://arxiv.org/abs/2502.17419) | 2025 |
| Enhancing AI Systems with Agentic Workflow Patterns | [arXiv:2601.12560](https://arxiv.org/abs/2601.12560) | 2026 |
| Agentic Design Patterns (Belcak et al.) | [arXiv:2601.19752](https://arxiv.org/abs/2601.19752) | 2026 |
| AutoGen: Enabling Next-Gen LLM Apps via Multi-Agent Conversation | [arXiv:2308.08155](https://arxiv.org/abs/2308.08155) | 2023 |

### Official Specifications

| Spec | Organization | Link |
|------|-------------|------|
| O-RAN Architecture Description (WG1) | O-RAN Alliance | [o-ran.org/specifications](https://www.o-ran.org/specifications) |
| O-RAN Use Cases and Deployment Scenarios | O-RAN Alliance | [o-ran.org/specifications](https://www.o-ran.org/specifications) |
| O-RAN Security Focus (WG11) | O-RAN Alliance | [o-ran.org/specifications](https://www.o-ran.org/specifications) |
| 3GPP TR 38.843 — AI/ML for NR Air Interface (Rel-18) | 3GPP | [3gpp.org](https://www.3gpp.org/ftp/Specs/archive/38_series/38.843/) |
| ITU-R IMT-2030 Framework (6G) | ITU-R | [itu.int](https://www.itu.int/rec/R-REC-M.2160/en) |
| Bharat 6G Vision Document | DoT India | [dot.gov.in](https://dot.gov.in/sites/default/files/Bharat%206G%20Vision%20Document_0.pdf) |

### Industry Whitepapers

| Document | Organization | Link |
|----------|-------------|------|
| AI-RAN Alliance Vision and Mission Whitepaper | AI-RAN Alliance | [ai-ran.org/publications](https://ai-ran.org/publications/) |
| Integrating AI/ML in Open-RAN: Overcoming Challenges | AI-RAN Alliance | [ai-ran.org/publications](https://ai-ran.org/publications/) |
| SMO Enabling Intelligent RAN Operations | Ericsson | [ericsson.com](https://www.ericsson.com/en/reports-and-papers/white-papers/smo-enabling-intelligent-ran-operations) |
| Technology Innovations for 6G System Architecture | Nokia Bell Labs | [nokia.com](https://www.nokia.com/networks/insights/6g-white-paper/) |
| Evaluating Generative AI for Telecom | Ericsson | [ericsson.com](https://www.ericsson.com/en/blog/2025/5/evaluating-generative-ai-for-telecom) |

### Open-Source Tools and Platforms

| Tool | Purpose | Link |
|------|---------|------|
| O-RAN Software Community (OSC) | Open-source Non-RT RIC, Near-RT RIC reference implementation | [o-ran-sc.org](https://o-ran-sc.org/) |
| OpenAICellular (OAIC) | Full-stack O-RAN research platform with rApp/xApp tutorials | [openaicellular.github.io/oaic](https://openaicellular.github.io/oaic/) |
| Orange ns-O-RAN-flexric | ns-3 + FlexRIC simulator, E2AP v1.01, KPM v3, RC v1.03 | [github.com/Orange-OpenSource/ns-O-RAN-flexric](https://github.com/Orange-OpenSource/ns-O-RAN-flexric) |
| FlexRIC (EURECOM) | Flexible RIC for research — supports multi-vendor E2 agents | [github.com/EURECOM-SIM5GCNL/flexric](https://github.com/EURECOM-SIM5GCNL/flexric) |
| LangGraph | Multi-agent workflow framework | [langchain-ai.github.io/langgraph](https://langchain-ai.github.io/langgraph/) |
| AutoGen (Microsoft) | Multi-agent conversation and coordination framework | [microsoft.github.io/autogen](https://microsoft.github.io/autogen/) |

### Where to Track New Papers

| Source | What to search | Link |
|--------|---------------|------|
| arXiv cs.NI | O-RAN, AI-RAN, SMO agentic, xApp, rApp 6G | [arxiv.org/list/cs.NI/recent](https://arxiv.org/list/cs.NI/recent) |
| arXiv eess.SP | Signal processing + AI, 6G air interface | [arxiv.org/list/eess.SP/recent](https://arxiv.org/list/eess.SP/recent) |
| IEEE Xplore | Filter: "O-RAN" or "Open RAN", 2022–present | [ieeexplore.ieee.org](https://ieeexplore.ieee.org) |
| IEEE INFOCOM | Top networking conference — O-RAN papers every year | [infocom.info](https://infocom.info/) |
| IEEE Globecom | Major telecom conference — AI-RAN and O-RAN tracks | [globecom2025.ieee-globecom.org](https://globecom2025.ieee-globecom.org/) |
| Semantic Scholar | Set alerts for authors: Polese, Bonati, D'Oro, Melodia | [semanticscholar.org](https://www.semanticscholar.org/) |

---

## Reading Roadmap

This is the order I recommend reading through the material if you are starting with a 5G background but are new to the AI side.

**Week 1–2 — Get the foundation**
1. Read arXiv:2202.01032 (Polese et al. O-RAN survey) — focus on architecture, interfaces, RIC
2. Read the O-RAN Alliance WG1 Architecture Description (download from o-ran.org)
3. Read arXiv:2309.02427 (Agentic AI survey) — read Section 2 and 3 to understand agent taxonomy
4. Read arXiv:2210.03629 (ReAct paper) — short, read fully

**Week 3–4 — Go deep on the RIC**
5. Read the xApps survey (COMNET 2025 PDF) — all of it; this is your Near-RT RIC bible
6. Set up OAIC or ns-O-RAN locally; run the rApp-healthcheck tutorial
7. Read the ScienceDirect RIC open-source paper (open-source implementation walkthrough)

**Week 5–6 — AI-RAN hardware side**
8. Read AI-RAN Alliance Vision Whitepaper
9. Read arXiv:2503.07420 (Interplay of AI and RAN)
10. Read NVIDIA Aerial blog posts (ARC-Compact, Aerial SDK overview)

**Week 7–9 — Agentic AI applied to RAN**
11. Read arXiv:2602.24115 (Agentic AI-RAN) — most important paper for this repo's theme
12. Read arXiv:2505.07842 (RAN Cortex)
13. Read arXiv:2507.21696 (Edge Agentic AI)
14. Read arXiv:2509.21634 (MobiLLM)
15. For each: fill in the "what to look for" table documented in Section 04

**Week 10–11 — Design patterns and governance**
16. Read arXiv:2601.19752 (Agentic Design Patterns)
17. Read IMDA Governance Framework for Agentic AI
18. Read arXiv:2502.09920 (Trustworthy Agentic AI)

**Week 12 — Synthesize**
19. Write comparison table: all agentic+RAN papers, side by side on: O-RAN layer, action space, baseline, gap
20. Identify which open problem you want to work on
21. Find 3 papers from the last 6 months that address that problem partially — these become your baseline

---

## Glossary of Key Terms

| Term | Definition |
|------|-----------|
| **A1 interface** | Interface between Non-RT RIC and Near-RT RIC; used to push AI policies down |
| **AI-native** | Architecture where AI is integral to protocol design, not an add-on |
| **AI-RAN** | Running AI inference workloads and 5G RAN on shared compute hardware |
| **Agentic AI** | AI system that autonomously plans, acts, observes, and adapts toward a goal |
| **C-RAN** | Centralized RAN — baseband processing pooled in a central location |
| **D-RAN** | Distributed RAN — compute at each cell site |
| **E2 interface** | Interface between Near-RT RIC and O-CU/O-DU; used for near-real-time RAN control |
| **E2SM-KPM** | E2 Service Model for Key Performance Metrics — xApps subscribe to KPI streams |
| **E2SM-RC** | E2 Service Model for RAN Control — xApps send control messages to gNB |
| **LLM** | Large Language Model — the AI backbone of most agentic systems |
| **Near-RT RIC** | Near-Real-Time RAN Intelligent Controller; 10ms–1s control loop; hosts xApps |
| **Non-RT RIC** | Non-Real-Time RAN Intelligent Controller; >1s control loop; hosts rApps |
| **O-CU** | O-RAN Centralized Unit — handles RRC and PDCP layer |
| **O-DU** | O-RAN Distributed Unit — handles L2 (MAC, RLC) |
| **O-RU** | O-RAN Radio Unit — handles L1 and RF |
| **O1 interface** | SMO to O-DU/O-CU/O-RU — management and configuration |
| **O2 interface** | SMO to O-Cloud — infrastructure lifecycle management |
| **R1 interface** | rApps to Non-RT RIC — rApp service exposure and consumption |
| **rApp** | Non-RT RIC application; runs analytics and generates long-term policies |
| **ReAct** | Reason + Act loop — foundational architecture for LLM-based agents |
| **SMO** | Service Management and Orchestration — top-level O-RAN management layer |
| **xApp** | Near-RT RIC application; makes near-real-time RAN control decisions via E2 |

---

*Tags: `oran` · `ai-ran` · `agentic-ai` · `6g` · `smo` · `non-rt-ric` · `near-rt-ric` · `xapp` · `rapp` · `llm-for-telecom`*
