# Agentic AI in 5G–6G Networks
**Personal Research Notes — AI-RAN · O-RAN SMO · Agentic AI**

> I work in 5G/6G. This repository is my personal knowledge base as I learn the AI side of the RAN stack. Everything here is written from papers, specs, and whitepapers in my own words so that I can actually understand the concepts, not just reference them.

---

## Table of Contents

- [How to Read This Repo](#how-to-read-this-repo)
- [Part 1 — What is Agentic AI](#part-1--what-is-agentic-ai)
- [Part 2 — O-RAN Architecture and SMO](#part-2--o-ran-architecture-and-smo)
- [Part 3 — AI-RAN](#part-3--ai-ran)
- [Part 4 — Agentic AI Applied to RAN](#part-4--agentic-ai-applied-to-ran)
- [Part 5 — 6G and AI-Native Networks](#part-5--6g-and-ai-native-networks)
- [Part 6 — Agentic Design Patterns](#part-6--agentic-design-patterns)
- [Part 7 — Governance Safety and Ethics](#part-7--governance-safety-and-ethics)
- [Papers Reading List](#papers-reading-list)
- [Reading Order](#reading-order)
- [Glossary](#glossary)

---

## How to Read This Repo

This is not a course. It is my personal study material — concepts that took me longer to understand are explained in more detail, concepts that came quickly are shorter. If you already have a strong O-RAN background, go to Part 1 first to build the agentic AI foundation, then come back to Part 2 to see where agents fit in the architecture. If you already know agentic AI, go directly to Part 4 where both streams converge.

---

## Part 1 — What is Agentic AI

### The Difference Between Generative AI and Agentic AI

When LLMs first went mainstream, everyone saw the same pattern — you type something, the model generates a response. That is a one-shot input-output mapping. The model has no goal, no plan, no memory of what happened before, and no feedback loop telling it whether its output was correct. That is generative AI.

Agentic AI is fundamentally different. The ReAct paper (Yao et al., arXiv:2210.03629) was the first to formally show that LLMs are not limited to generating text — they can generate both verbal reasoning traces and task-specific actions in an interleaved manner. As the paper describes it, reasoning traces help the model induce, track, and update action plans as well as handle exceptions, while actions allow it to interface with external sources such as knowledge bases or environments to gather additional information. This means the model thinks in one step and does something in the next, then observes the result and thinks again. The loop continues until the goal is reached.

The Agentic AI-RAN paper (arXiv:2602.24115) defines this in the O-RAN context: agentic AI systems with explicit planning, tool use, memory, and self-management offer a natural way to structure long-lived control loops. Long-lived means this is not a one-time decision — the agent continuously monitors the network, makes changes, and learns from its own performance over time.

### Planning — Breaking a Goal into Steps

The planning module breaks a high-level goal into a concrete sequence of steps. This is fundamentally different from an RL xApp, which maps the current state directly to an action through a learned policy. That mapping was built during training and fails in novel situations the model never encountered. A planning-based agent reasons from first principles, constructs steps, and then executes — which is why it can handle situations it has never seen before.

The Agentic AI-RAN paper (arXiv:2602.24115) formalizes this as the Plan-Act-Observe-Reflect primitive. At each decision epoch the agent receives a context made up of KPM/KQI snapshots, topology, and slice mix, along with a goal that encodes objectives, constraints including isolation and fairness, and budgets for latency, CPU, and E2 bandwidth. The agent then selects a short sequence of O-RAN skills — such as PRB reallocation, power capping, or handover pinning — and executes them. This is the Plan phase.

### Memory — How the Agent Remembers

Without memory, an agentic system starts fresh every time. It has no record of what worked yesterday, what failed last week, or what patterns repeat every Friday evening. The RAN Cortex paper (arXiv:2505.07842) specifically identifies this as the core problem: these agents remain fundamentally stateless, treating each decision as isolated, lacking any persistent memory of prior events or outcomes. This reactive behavior constrains optimization, especially in environments where network dynamics exhibit episodic or recurring patterns.

The CoALA framework (arXiv:2309.02427, Princeton) formally categorizes memory in AI agents into four types drawn from cognitive science. In-context memory is whatever is inside the LLM context window during the current session. Episodic memory holds records of specific past events. Semantic memory holds general factual knowledge. Procedural memory holds rules and skills — knowledge about how things are done.

RAN Cortex implements these memory types for O-RAN through a specific four-element architecture: a context encoder that transforms network state into high-dimensional embeddings, a vector-based memory store of past network episodes, a recall engine that retrieves semantically similar past situations, and a policy interface that supplies historical context to AI agents in real time or near-real time. When a new network event arrives, the agent first retrieves similar past episodes from memory and incorporates them into the current decision. This is how the agent improves over time without retraining.

### Tool Use — How the Agent Interacts with the Real World

Tools are what connect an LLM-based agent to the real world. Without tools, the agent only generates text. With tools, it can actually do things. The Agentic AI-RAN paper (arXiv:2602.24115) introduces this as the Skills as Tool-Use primitive. In O-RAN, a skill is described as a thin, verifiable wrapper around a controllable primitive that the agent invokes as a tool. The verifiable part matters — the operator must be able to know exactly what a skill does and what its effect is, and this must be automatically auditable.

The paper organizes the task landscape into three clusters where these skills operate: network slice life-cycle management, radio resource management closed loops, and cross-cutting security, privacy, and compliance. Each cluster has different skills that use different O-RAN interfaces.

### Reflection — How the Agent Catches Its Own Mistakes

The ReAct paper (arXiv:2210.03629) showed an important finding in its results: ReAct overcomes issues of hallucination and error propagation prevalent in chain-of-thought reasoning by interacting with external environments, and generates human-like task-solving trajectories that are more interpretable than baselines without reasoning traces. This happens because when the agent generates a reasoning trace — in the form of a Thought — it can catch mistakes in its own reasoning before it acts.

Reflection goes one step further. After taking an action, the agent observes what happened and evaluates whether it matches the expected outcome. The Agentic AI-RAN paper (arXiv:2602.24115) formalizes this as the Observe-Reflect phase. Memory spans short-term caches at the Near-RT level, episodic logs per decision, and long-term knowledge curated at the higher layers. Evidence is generated by design at each commit — decision-level records link goals, compact context, selected actions, and observed outcomes, supporting audit and regulatory reporting without exposing raw subscriber data.

### Multi-Agent Systems — Why One Agent Is Not Enough

The Wireless Large AI Model paper (arXiv:2504.14653) describes agentic AI-RAN as enabling systems to perceive, reason, decide, and act autonomously. The challenge is that optimizing multiple objectives simultaneously — resource allocation, service assurance, energy efficiency — is too complex for a single agent operating alone.

The Agentic AI for Intent-driven Optimization paper (arXiv:2602.22539) implements a concrete multi-agent structure in cell-free O-RAN. A supervisor agent translates operator intents into an optimization objective and minimum rate requirements. A user weighting agent retrieves relevant prior experience from a memory module to determine user priority weights for precoding. If the intent includes an energy-saving objective, an O-RU management agent is activated to determine the set of active O-RUs using a deep reinforcement learning algorithm. A monitoring agent continuously measures user data rates and coordinates with the other agents to guarantee minimum rate requirements are satisfied. The paper reports that this framework reduces the number of active O-RUs by a factor of four while maintaining the minimum rate requirements — a concrete quantitative result from a real implementation.

---

## Part 2 — O-RAN Architecture and SMO

### Why O-RAN Exists

Traditional RAN was a closed ecosystem. You bought a complete base station from a single vendor — radio hardware, baseband processing software, management interface — all proprietary, all from the same company, communicating through proprietary protocols. You could not mix components from different vendors. You could not write your own optimization algorithm and run it inside the RAN. All innovation came from a handful of major vendors.

Polese et al. (arXiv:2202.01032) — the most widely cited O-RAN survey — describes this problem and O-RAN's solution: define open interfaces between all components of the RAN stack so different vendors can interoperate, and define a RAN Intelligent Controller platform where third parties can deploy their own AI/ML optimization applications.

### The O-RAN Disaggregation

The traditional monolithic base station is split into three separate software-defined network functions.

The O-RU (Open Radio Unit) is the physical radio hardware — antenna, RF electronics, and the very bottom of L1. It connects to the O-DU via the fronthaul interface using eCPRI (enhanced Common Public Radio Interface). The fronthaul has extremely tight latency requirements — in C-RAN deployments, under 100 microseconds one way.

The O-DU (Open Distributed Unit) handles the remaining L1 (channel coding — LDPC encode/decode, rate matching), L2 MAC (scheduling, HARQ), and L2 RLC. The O-DU communicates with the O-CU via the F1 interface — F1-C for the control plane and F1-U for the user plane.

The O-CU (Open Centralized Unit) handles the upper protocol layers: RRC (Radio Resource Control — connection management, measurement reporting, handover decisions), SDAP, and PDCP. The O-CU is split into O-CU-CP (Control Plane) and O-CU-UP (User Plane) connected via the E1 interface. The O-CU connects to the 5G Core via N2 and N3.

### SMO — The Top-Level Manager

The SMO (Service Management and Orchestration) sits at the top of the O-RAN hierarchy. It manages the entire O-RAN deployment through two main interfaces.

The O1 interface connects the SMO to the O-DU, O-CU, and O-RU. This is the management interface — the SMO uses it to configure network functions, push software updates, collect performance management data in the form of PM files, and receive fault management alarms. O1 uses NETCONF/YANG protocols. It is essentially the configure-and-observe interface.

The O2 interface connects the SMO to the O-Cloud. The O-Cloud is the cloud infrastructure — servers, storage, networking — on which O-RAN network functions run as containerized workloads. Through O2 the SMO can deploy new network function instances, scale them based on traffic demand, and manage hardware resources.

### Non-RT RIC and rApps — The Long-Horizon Intelligence Layer

The Non-RT RIC (Non-Real-Time RAN Intelligent Controller) is the AI/ML brain embedded within the SMO. It operates on timescales greater than one second — typically minutes, hours, or longer. This is where strategic, long-horizon intelligence lives.

The Non-RT RIC does three main things. It trains and manages ML models using the large volumes of historical network data available through the SMO — PM files, KPI histories, configuration logs. It runs batch analytics on this historical data to identify patterns and trends. And it generates A1 policies that it pushes down to the Near-RT RIC via the A1 interface. An A1 policy is not a direct command — it is a constraint or guidance that the Near-RT RIC enforces in real time.

rApps are third-party applications running inside the Non-RT RIC through the R1 interface — the interface that connects rApps to the Non-RT RIC framework for exposing and consuming services. The RAN Intelligent Controller paper (ScienceDirect 2024) implemented and evaluated rApps for energy efficiency, interference management, and predictive maintenance on a real private 5G network testbed. The paper found that ML-based models with sequence input outperform models that use only instance input, and that the best ML models achieved more than 90% accuracy across all applications.

### Near-RT RIC and xApps — The Fast Control Loop

The Near-RT RIC operates on 10 millisecond to 1 second control loops. It connects to the O-CU and O-DU via the E2 interface, which operates in two directions.

In the subscription direction, an xApp tells the Near-RT RIC what measurements it wants from which cells at what frequency. The RIC passes this subscription to the O-CU/O-DU via E2, and the gNB starts sending the requested measurements. Measurement types are defined by E2 Service Models. E2SM-KPM (Key Performance Metrics) lets an xApp subscribe to UE-level and cell-level performance metrics — DL/UL throughput, SINR, RSRP, CQI, BLER, PRB utilization, handover statistics — in near real time. E2SM-RC (RAN Control) is used for the control direction.

In the control direction, the xApp sends control messages via E2SM-RC to directly change RAN parameters on the gNB. What xApps can control includes: handover A3 offset and time-to-trigger, per-UE scheduling priority weights, beam selection for specific UEs, PRB allocation between slices, DRX parameter configuration, and carrier aggregation on/off.

A key challenge in deployments with multiple xApps is conflict detection. If xApp A wants to increase the A3 offset for a cell and xApp B simultaneously wants to decrease it, the Near-RT RIC needs a conflict mediation mechanism to resolve this before sending contradictory commands to the gNB.

### The Full Control Loop — How All Layers Work Together

The complete chain from the Non-RT RIC down to the radio hardware can be illustrated through an energy saving flow. The Non-RT RIC rApp analyzes historical traffic data, identifies cells that consistently carry very low traffic during certain hours, and generates an A1 policy specifying the conditions under which those cells can go to sleep. That policy is pushed via A1 to the Near-RT RIC. The energy saving xApp receives the policy and registers it as an operating constraint. It monitors PRB utilization of those cells via E2SM-KPM subscription. When utilization drops below the threshold for the required duration, the xApp sends an E2SM-RC control message to the O-DU to deactivate the carrier. Before the busy period begins, the xApp reactivates the carrier. This is the closed loop — from long-horizon analytics in the Non-RT RIC, through A1 policy, to near-real-time enforcement by the xApp, through E2 control to the actual RAN hardware.

This is exactly the structure that agentic AI maps onto. At each layer, instead of a static ML model, there is a planning-memory-tool-use-reflection capable system that can handle situations the model was never explicitly trained for.

---

## Part 3 — AI-RAN

### What AI-RAN Means

AI-RAN is a different concept from O-RAN's AI/ML. O-RAN AI/ML uses intelligence to control an existing 5G network. AI-RAN means running 5G baseband processing and AI inference workloads on the same physical compute hardware simultaneously.

Traditional 5G BBUs ran on dedicated ASICs or FPGAs — purpose-built chips optimized for specific signal processing tasks like LDPC encoding/decoding, FFT/IFFT, MIMO precoding. These chips are extremely fast and power-efficient for those specific tasks, but they cannot do anything else. You cannot run a language model on an LDPC accelerator.

The AI-RAN Alliance (2024 Vision Whitepaper, available at ai-ran.org) defines AI-RAN as replacing or supplementing those dedicated chips with general-purpose GPU compute. Modern GPUs are fast enough to run the full 5G L1 stack in software — NVIDIA's cuBB (CUDA Baseband Library) does exactly this — while simultaneously having idle compute capacity that can be used for AI inference workloads.

The value proposition is straightforward. A cell site's compute sits mostly idle during off-peak hours. With AI-RAN, during those idle hours the same GPU hardware can serve AI inference requests — generating revenue from compute that was previously doing nothing. During peak traffic hours, the scheduler dynamically shifts more compute to the 5G baseband and less to AI workloads.

### NVIDIA's Platform

The Interplay of AI-and-RAN paper (arXiv:2503.07420) specifically analyzes GPU compute sharing between 5G and AI workloads for a converged 6G platform. NVIDIA ARC (AI-RAN Controller) is the intelligence layer that decides how to share GPU compute between the 5G baseband and AI inference workloads. It must guarantee that L1 processing latency requirements are always met — L1 must complete within 3GPP timing requirements, typically a few hundred microseconds from a subframe boundary — while using remaining capacity for AI inference without interference. NVIDIA's ARC-Compact (announced May 2025) is designed specifically for cell site deployment in D-RAN scenarios where a compact unit at the cell site runs both 5G and AI simultaneously.

Spectrum-X is NVIDIA's networking platform for connecting GPU-equipped cell sites or centralized AI-RAN clusters with the high bandwidth and low latency needed for GPU-to-GPU communication at AI-RAN scale. BlueField DPUs handle fronthaul packet processing (eCPRI) without consuming CPU or GPU cycles, freeing the main GPU for baseband and AI workloads.

### C-RAN vs D-RAN for AI-RAN

In D-RAN, every cell site has its own compute. In AI-RAN terms, this means putting a GPU server or compact appliance at every site. The advantage is no fronthaul latency concern since compute is co-located with the radio. The disadvantage is that each site's GPU is underutilized most of the time, resources cannot be pooled across sites, and managing many separate GPU servers is operationally expensive.

In C-RAN, baseband processing is pooled in a central hub that serves many O-RUs. In AI-RAN terms, you build a GPU cluster at the hub that simultaneously serves 5G baseband for all attached O-RUs and runs AI inference workloads. Resource utilization is much better because traffic from different cells peaks at different times. The disadvantage is the requirement for ultra-low latency fronthaul fiber between the O-RUs and the central hub.

The direct connection between AI-RAN hardware and agentic AI software is this: an agentic system in the O-RAN SMO or Non-RT RIC needs compute to run LLM inference. If that inference runs in a remote cloud, round-trip latency adds hundreds of milliseconds to every reasoning step, costs are high, and sensitive network KPI data leaves the operator's infrastructure. With AI-RAN, the GPU compute needed for the agentic LLM is co-located with the RAN within the operator's own infrastructure. This is what makes real-time on-premises agentic RAN control technically and economically feasible.

---

## Part 4 — Agentic AI Applied to RAN

### Where Existing O-RAN AI Falls Short

Most existing O-RAN AI/ML work uses a narrow approach: train an RL model offline, deploy it as an xApp, subscribe to E2SM-KPM metrics, use those metrics as the RL state to select control actions. This works within its limits, but the limits are significant.

The Agentic AI-RAN paper (arXiv:2602.24115) states the core problem directly in its abstract: O-RAN exposes rich control and telemetry interfaces across the Non-RT RIC, Near-RT RIC, and distributed units, but also makes it harder to operate multi-tenant, multi-objective RANs in a safe and auditable manner. Multi-tenant and multi-objective — this is exactly where RL xApps fail. An RL xApp optimizes one specific KPI. When there are multiple tenants, multiple objectives, and those objectives conflict with each other, RL alone is not sufficient.

The ReAct paper (arXiv:2210.03629) demonstrated that the LLM-based agentic approach outperformed imitation and reinforcement learning methods by an absolute success rate of 34% on the ALFWorld benchmark, using only one or two in-context examples. This matters because it shows that the agentic approach works in zero-shot or few-shot settings where RL would need extensive training data and environment interaction.

### The Agentic AI-RAN Architecture

The Agentic AI-RAN paper (arXiv:2602.24115) is the most complete proposal for how agentic AI maps onto the O-RAN architecture. The paper defines four agentic primitives specifically for O-RAN.

The first is the Plan-Act-Observe-Reflect loop. The paper describes it as a timing-aware control cycle aligned with the Non-RT, Near-RT, and RT layers. Timing-aware is critical — the agent running at the Non-RT RIC level can operate with a minutes-scale loop, while the agent at the Near-RT RIC level must stay within the 10ms to 1s constraint. A single generic loop would not work across both.

The second primitive is Skills as Tool-Use. In O-RAN, a skill is a thin, verifiable wrapper around a controllable primitive that the agent invokes as a tool. Verifiable means the skill's behavior and effect can be audited automatically — which is necessary when autonomous agents are making changes to live network configurations.

The third primitive is Memory and Evidence. The paper specifies that memory spans short-term caches at the Near-RT level, episodic logs per decision, and long-term knowledge curated at the SMO/Non-RT level. Evidence is generated by design at each commit — decision-level records link goals, compact context, selected actions, and observed outcomes, supporting audit and regulatory reporting without exposing raw subscriber data. This is an elegant solution to the privacy problem: auditability without subscriber data exposure.

The fourth primitive is Self-Management Gates. This mechanism controls when the agent acts autonomously and when it escalates to a human. High-impact actions trigger automatic human confirmation. The paper showed in a multi-cell O-RAN simulation that an agent with these four primitives improved slice life-cycle and RRM performance compared to conventional ML/RL xApps.

### RAN Cortex — Solving the Memory Problem

The RAN Cortex paper (arXiv:2505.07842) is specifically about giving O-RAN agents persistent memory. Its abstract states the problem clearly: existing AI-based RAN decision systems remain fundamentally stateless, treating each decision as isolated. The paper proposes a memory-augmented architecture with four components: a context encoder that transforms network state into high-dimensional embeddings, a vector-based memory store of past network episodes, a recall engine that retrieves semantically similar past situations, and a policy interface that supplies historical context to agents in real time.

The paper identifies three concrete benefits for RAN. Improved sample efficiency — learning from prior cases reduces the need for retraining or replay buffers. Contextual generalization — retrieved memory allows policies to adapt to similar unseen states based on related prior knowledge. Temporal awareness — recognition of recurring patterns like peak traffic times or stadium events.

The paper demonstrates these benefits through two specific use cases: stadium traffic mitigation and mobility management in drone corridors. Both are scenarios with strong episodic structure — stadium events repeat regularly, drone corridors appear in specific locations repeatedly. Memory allows the agent to learn from these recurring patterns.

### Edge Agentic AI and MobiLLM

The Edge Agentic AI Framework paper (arXiv:2507.21696) implements AutoGen-based multi-agent coordination at the O-RAN edge. AutoGen (arXiv:2308.08155) is Microsoft's framework where multiple LLM agents have conversations with each other to solve problems. This paper shows that generalist AI frameworks with telecom-specific customization are effective for O-RAN use cases. It references real-world deployments — NVIDIA's Aerial platform for AI-RAN and SoftBank's deployment optimizing power consumption in AI-RAN data centers — to anchor the academic work in what is actually happening in industry.

The MobiLLM paper (arXiv:2509.21634) addresses the security angle. When an agentic AI autonomously changes network configurations, it also creates an attack surface. If E2 KPI data is compromised — or a fake gNB inserts false measurements — the agent's decisions can be manipulated. MobiLLM proposes an agentic framework that handles closed-loop threat detection and mitigation in 6G Open RANs, specifically addressing how an LLM-based agent detects threats, plans responses, and mitigates them automatically while keeping the process safe and auditable.

---

## Part 5 — 6G and AI-Native Networks

### AI-Assisted vs AI-Native

Understanding 6G requires understanding this distinction clearly.

In the 5G O-RAN approach, the network protocol stack is designed exactly as 3GPP specified — the NR air interface, the protocol layers (PHY, MAC, RLC, PDCP, RRC), the handover procedures, the scheduling algorithms all work as standardized. AI and ML are deployed on top as an optimization layer — reading measurements, adjusting parameters, making configuration changes. AI is an add-on. Remove it and the network still functions, just less optimally.

In AI-native 6G design, the protocol stack itself has AI as an integral component. 3GPP Release 18 (5G Advanced) began the first standardization of AI/ML for the NR air interface with three specific use cases. CSI feedback compression pairs an encoder at the UE with a decoder at the gNB — the UE compresses channel measurements into a compact latent representation, and the gNB reconstructs the channel from the compressed feedback. This autoencoder pair is trained end-to-end and can achieve better compression than the codebook-based methods that 3GPP designed by hand. Beam management enhancement uses AI-assisted prediction to reduce or eliminate the exhaustive beam sweeping overhead of mmWave systems. Positioning accuracy improvement uses AI to enhance location estimation. These are still AI-assisted rather than fully AI-native — optional enhancements on top of the existing NR protocol — but the direction is clearly toward full AI-native design.

ITU-R has published the IMT-2030 framework (Recommendation M.2160) as the international 6G standardization framework. AI and ML are listed as fundamental technology components — not optional, but integral to the system design.

### 3GPP Roadmap

3GPP is not jumping to AI-native in a single step. Release 18 began AI/ML standardization for the air interface. Release 19 is expanding AI/ML to more use cases and studying AI/ML for core network functions. It is also studying AI/ML model lifecycle management — training, versioning, deployment — as a network function in its own right. Release 20 and beyond, aligned with ITU-R IMT-2030, targets the full AI-native architecture where AI is woven into the protocol design rather than layered on top.

### Bharat 6G Vision

India's Department of Telecommunications published the Bharat 6G Vision Document in 2023. The document calls for India to be a 6G technology creator rather than just a consumer, with a goal of owning at least 10% of global 6G patents — a significant shift from India's historically adoption-focused role in telecom. The vision explicitly identifies AI-native design as a priority: the network should be designed with AI as a core component, not retrofitted. Energy efficiency and self-configuring, self-optimizing, self-healing network capabilities are also explicitly stated as requirements.

---

## Part 6 — Agentic Design Patterns

### Why Patterns Matter

The Agentic AI-RAN paper (arXiv:2602.24115) formalizes O-RAN-specific agentic primitives that are essentially telecom-specific design patterns. The Agentic Design Patterns paper (arXiv:2601.19752) by Belcak et al. provides a formal taxonomy of general patterns. Reading both together shows how general patterns map onto O-RAN's specific timing and interface constraints.

### Chain of Thought

Chain of Thought is the foundation of all agentic reasoning. The insight was that asking an LLM to think step by step before answering dramatically improves accuracy on complex problems. Each reasoning step conditions the next, building up to a more reliable conclusion rather than making a leap that skips necessary logic. In agentic systems, CoT is the mechanism behind the Thought step in every reasoning loop.

### ReAct

The ReAct paper (arXiv:2210.03629) describes the approach as generating both verbal reasoning traces and actions in an interleaved manner, allowing the model to perform dynamic reasoning to create, maintain, and adjust high-level plans for acting, while also interacting with external environments to incorporate additional information into reasoning. In practice, ReAct is implemented as a prompt template that structures LLM output into alternating Thought and Action sections. The framework parses the Action section, executes the tool call, appends the Observation to the context, and calls the LLM again with the extended context. This continues until the LLM outputs a Final Answer marker.

### Reflection

Reflection adds a self-critique step after each action. Rather than immediately moving to the next step after observing an outcome, the agent evaluates: was this the right action? Did it achieve what was intended? What would be done differently? The Agentic AI-RAN paper's Observe-Reflect phase implements this in O-RAN with automatic evidence logging at each commit, so reflection is not just an internal reasoning process but a documented audit trail.

### Planning and Decomposition

For complex, multi-step goals, a pure ReAct loop can lose track of the high-level objective while dealing with individual tool calls. The planning pattern adds an explicit up-front planning step — a numbered list of steps the agent intends to take — which is then tracked and updated during execution. The Agentic AI-RAN paper aligns planning explicitly with O-RAN timing: planning at the Non-RT RIC level operates on a minutes scale, while planning at the Near-RT RIC level must stay within the 10ms to 1s constraint.

### Multi-Agent Orchestration

The Agentic AI for Intent-driven Optimization paper (arXiv:2602.22539) implements multi-agent orchestration concretely in cell-free O-RAN. The supervisor agent translates intent, the user weighting agent handles resource prioritization using memory retrieval, the O-RU management agent handles energy saving using DRL, and the monitoring agent ensures minimum rate requirements are maintained across all agents. To enhance scalability, the paper adopts a parameter-efficient fine-tuning method that enables the same underlying LLM to be used across different agents. This shows a practical solution to the cost problem of running multiple large models simultaneously.

### Memory-Augmented Retrieval

The RAN Cortex paper (arXiv:2505.07842) formalizes this pattern for O-RAN. The paper notes that user behavior, traffic load, interference, and mobility exhibit episodic structure — recurrent patterns that reappear across space and time. Memory-augmented retrieval is the mechanism for exploiting this structure. The recall engine retrieves semantically similar past network situations using cosine similarity or learned kernel metrics over the embedding space. The retrieved episodes are then supplied to the policy interface, which conditions the agent's decision on both the live current state and the recalled memory.

### Constitutional Constraints and Self-Management Gates

The Agentic AI-RAN paper's self-management gates implement this pattern specifically for O-RAN. Constraints are hard-coded independently of the agent's reasoning — they define the boundaries of the action space that the agent can never cross regardless of what its planning produces. Actions with impact scores above a defined threshold trigger automatic human escalation rather than autonomous execution. This is the mechanism that allows autonomous operation on lower-impact decisions while keeping human authority over high-impact changes.

---

## Part 7 — Governance Safety and Ethics

### Why This Is Non-Negotiable in Telecom

The MobiLLM paper (arXiv:2509.21634) opens with this point explicitly: deploying agentic AI in 6G O-RAN creates security and safety challenges that were not present in traditional ML deployments. A misconfigured beam or an incorrect handover parameter change can cause service outages affecting thousands or millions of users, including emergency services.

The Agentic AI-RAN paper (arXiv:2602.24115) makes the multi-tenant dimension explicit: in a shared O-RAN infrastructure serving multiple operators' network slices, one operator's agentic controller can inadvertently affect another operator's slice. This is not a hypothetical risk — it is an architectural consequence of the shared control plane.

### The Risk Landscape

Cascading failures arise because in a densely interconnected network, an optimization in one cell affects neighboring cells. An agent that optimizes locally without modeling the impact on neighboring cells can create an interference chain that oscillates and amplifies rather than converges. This is agent coordination failure — individually rational actions by multiple agents produce a collectively irrational outcome.

Distribution shift means an agent optimized on one period of network data will fail when conditions change. The agent has no way to detect that its learned patterns no longer apply to the current environment.

Goal misalignment occurs when the agent's formal objective does not capture all the dimensions that matter. An agent instructed to maximize throughput will do exactly that — even if doing so depletes spectrum reserved for QoS-sensitive traffic, drives energy consumption up, or creates interference in neighboring spectrum bands. Telecom goals are multi-dimensional and the formal objective must reflect all of them.

Adversarial inputs are a specific risk in O-RAN because the agent's observations arrive via E2 KPI subscriptions from the gNB. If a gNB is compromised or a fake gNB is inserted into the network, it can send false KPI measurements that manipulate the agent's decisions. MobiLLM (arXiv:2509.21634) specifically addresses this threat model.

### Technical Safety Mechanisms

The Agentic AI-RAN paper's self-management gates implement guardrails that constrain the action space. Actions outside defined safe ranges are rejected before execution, deterministically and independently of the agent's reasoning — so even if the planning module produces a recommendation outside the safe boundary, it will not reach the network.

Shadow mode deployment is the standard path for introducing a new agent to a live network. The agent subscribes to real data and generates recommendations, but nothing is executed. Recommendations are logged and reviewed. The operator validates behavior before enabling autonomous mode.

The RAN Cortex paper's episodic memory architecture provides natural support for automatic rollback. Every action generates an episodic log entry with the pre-action network state, the action taken, the predicted outcome, and the actual outcome. When KPI monitoring detects degradation after an action, the pre-action configuration can be restored automatically from the log.

### Regulatory and Policy Context

NIST's AI Risk Management Framework (2023) and the EU AI Act — which classifies systems making consequential decisions in critical infrastructure as high-risk — both explicitly require explainability, auditability, and continuous monitoring for such systems. Telecom qualifies under these definitions. The ReAct loop's reasoning trace provides the natural basis for explainability — every decision has a logged chain of reasoning. The Agentic AI-RAN paper's evidence generation mechanism provides the auditability layer. Continuous monitoring frameworks and incident reporting specific to autonomous RAN AI remain an active research gap.

---

## Papers Reading List

All arXiv links are permanent and free to access.

### Foundational Surveys

| Paper | Link |
|-------|------|
| Understanding O-RAN: Architecture, Interfaces, Algorithms, Security — Polese et al. 2023 | https://arxiv.org/abs/2202.01032 |
| ReAct: Synergizing Reasoning and Acting in Language Models — Yao et al. 2022 | https://arxiv.org/abs/2210.03629 |
| Cognitive Architectures for Language Agents (CoALA) — Wang et al. 2023 | https://arxiv.org/abs/2309.02427 |
| Towards Trustworthy Agentic AI: A Comprehensive Survey 2025 | https://arxiv.org/abs/2502.09920 |
| From System 1 to System 2: A Survey of Reasoning LLMs 2025 | https://arxiv.org/abs/2502.17419 |

### Core Agentic AI and RAN Papers

| Paper | Link |
|-------|------|
| Agentic AI-RAN: Intent-Driven, Explainable, Self-Evolving Open RAN 2026 | https://arxiv.org/abs/2602.24115 |
| RAN Cortex: Memory-Augmented Intelligence for Context-Aware O-RAN 2025 | https://arxiv.org/abs/2505.07842 |
| Edge Agentic AI Framework for Autonomous Network Optimisation in O-RAN 2025 | https://arxiv.org/abs/2507.21696 |
| MobiLLM: Agentic AI for Closed-Loop Threat Mitigation in 6G Open RANs 2025 | https://arxiv.org/abs/2509.21634 |
| Agentic AI for Intent-driven Optimization in Cell-free O-RAN 2026 | https://arxiv.org/abs/2602.22539 |
| The Interplay of AI-and-RAN: Dynamic Resource Allocation for Converged 6G 2025 | https://arxiv.org/abs/2503.07420 |
| Agentic AI meets NAS: Proactive Traffic Prediction for AI-RAN 2025 | https://arxiv.org/abs/2510.00851 |
| Wireless Large AI Model: Shaping the AI-Native Future of 6G 2025 | https://arxiv.org/abs/2504.14653 |

### Agentic AI Frameworks and Design

| Paper | Link |
|-------|------|
| Enhancing AI Systems with Agentic Workflow Patterns 2026 | https://arxiv.org/abs/2601.12560 |
| Agentic Design Patterns — Belcak et al. 2026 | https://arxiv.org/abs/2601.19752 |
| AutoGen: Enabling Next-Gen LLM Apps via Multi-Agent Conversation 2023 | https://arxiv.org/abs/2308.08155 |
| Agentic AI: A Comprehensive Survey of Architectures and Applications 2025 | https://arxiv.org/abs/2510.25445 |

---

## Reading Order

**Step 1** — Read arXiv:2202.01032 (Polese et al. O-RAN survey). This is the foundation for everything in this repo. Focus on the disaggregation of O-RU, O-DU, and O-CU; the interface table covering A1, E2, O1, and O2; and the RIC architecture distinguishing Non-RT from Near-RT and rApps from xApps. Part 2 of this README will make much more sense after reading this paper.

**Step 2** — Read arXiv:2210.03629 (ReAct paper). This is a short paper — read it fully. It is the foundation of all agentic AI. Note the specific results: 34% improvement over RL on ALFWorld, and the reduction in hallucination on HotpotQA. These numbers will serve as reference points when evaluating claims in the RAN papers.

**Step 3** — Read arXiv:2309.02427 (CoALA / Cognitive Architectures for Language Agents). Focus on Sections 2 and 3, which cover the memory taxonomy and agent architecture. This is the source for the memory types discussed in Part 1.

**Step 4** — Read arXiv:2602.24115 (Agentic AI-RAN paper). This is the central paper for everything this repo is about. Note three things specifically: the four agentic primitives and exactly how they map to O-RAN layers and interfaces; the self-management gates mechanism and how it handles the safety requirement; and the multi-cell simulation results comparing against conventional ML/RL xApps.

**Step 5** — Read arXiv:2505.07842 (RAN Cortex). Focus on the memory architecture for O-RAN — what problem the paper identifies (stateless agents), the four-element solution it proposes, and how it demonstrates improvement in the stadium traffic mitigation use case.

**Step 6** — Read arXiv:2507.21696 and arXiv:2509.21634 (Edge Agentic AI and MobiLLM). These are complementary to Step 4 — the edge deployment angle and the security angle respectively.

**Step 7** — Read arXiv:2503.07420 (Interplay of AI and RAN). This is the AI-RAN hardware side — GPU compute sharing between 5G and AI workloads. After this paper, Part 3 of this README becomes much more concrete.

**Step 8** — Read arXiv:2602.22539 (Agentic AI for Intent-driven Optimization in Cell-free O-RAN). Another concrete multi-agent implementation with specific quantitative results. Note the O-RU count reduction numbers and the minimum rate guarantee mechanism.

**Step 9** — Read arXiv:2601.19752 and arXiv:2601.12560 (Agentic Design Patterns and Workflow Patterns). Having read all the application papers, the formal taxonomy of patterns in these papers will be much more meaningful — you will be able to recognize which patterns each RAN paper was using.

**Step 10** — Read arXiv:2502.09920 (Trustworthy Agentic AI) and arXiv:2502.17419 (Reasoning LLMs survey). The governance and safety research foundation, and the mechanistic explanation for why modern LLMs can do the kind of step-by-step reasoning that agentic systems require.

---

## Glossary

| Term | Definition |
|------|-----------|
| **A1 interface** | Interface from the Non-RT RIC down to the Near-RT RIC used to push AI/ML policies. Operates on seconds to minutes timescales. It is the strategic guidance channel from the slow intelligence layer to the fast one. |
| **AI-native** | Network design where AI is integral to how the protocols work, not an optional add-on. In 6G this means AI performing channel estimation, beam management, and CSI feedback compression at the protocol level rather than sitting on top. |
| **AI-RAN** | Running 5G baseband processing and AI inference workloads on the same GPU hardware at the cell site or central hub. The hardware convergence story, separate from the software intelligence of O-RAN. |
| **Agentic AI** | An AI system that has a goal, makes a plan to reach it, uses tools to take real actions, observes the results, and continues until the goal is reached without a human directing each step. |
| **AutoGen** | Microsoft's open-source framework for multi-agent systems where multiple LLM agents have structured conversations to solve problems. Used in several O-RAN agentic papers as the coordination layer. |
| **C-RAN** | Centralized RAN. Baseband processing is pooled in a central hub serving many remote radio heads. Enables GPU pooling for AI-RAN but requires ultra-low latency fronthaul fiber. |
| **Chain of Thought** | Prompting an LLM to reason step by step before producing an answer. Forces intermediate reasoning steps that improve accuracy on complex problems. The basis of the Thought step in every agentic reasoning loop. |
| **CoALA** | Cognitive Architectures for Language Agents — Princeton 2023 framework paper (arXiv:2309.02427) that formally categorizes AI agent memory into four types: in-context, episodic, semantic, and procedural. |
| **Constitutional constraints** | Hard rules the agent can never violate regardless of its reasoning. Checked deterministically before any action executes. Implemented independently of the AI to guarantee they hold. |
| **cuBB** | NVIDIA CUDA Baseband Library. Software implementation of the full 5G L1 stack running on GPU, enabling the same hardware to run both 5G processing and AI inference. |
| **D-RAN** | Distributed RAN. Compute at each cell site. Simpler fronthaul requirements but less resource pooling than C-RAN. |
| **E1 interface** | Between O-CU-CP and O-CU-UP. |
| **E2 interface** | Between the Near-RT RIC and O-CU/O-DU. The interface through which xApps subscribe to KPI measurements (E2SM-KPM) and send control commands to the gNB (E2SM-RC). |
| **E2SM-KPM** | E2 Service Model for Key Performance Metrics. Used by xApps to subscribe to near-real-time UE-level and cell-level measurements from the gNB. |
| **E2SM-RC** | E2 Service Model for RAN Control. Used by xApps to send control commands that change RAN parameters — handover thresholds, scheduling weights, beam configuration, and others. |
| **eCPRI** | Enhanced Common Public Radio Interface. Protocol used on the fronthaul link between O-DU and O-RU. Has strict latency requirements, particularly in C-RAN deployments. |
| **Episodic memory** | Records of specific past events in an agentic system — what the agent did and what the outcome was. Retrieved and used to guide decisions in similar future situations. |
| **F1 interface** | Between O-DU and O-CU. F1-C for control plane, F1-U for user plane. |
| **LangGraph** | LangChain framework for building multi-agent workflows as directed graphs where each node is an agent or a tool. Used for implementing multi-agent orchestration patterns. |
| **LLM** | Large Language Model. The reasoning backbone of modern agentic systems. Given a context of observations, memory, and goal, it generates what to do next. |
| **Near-RT RIC** | Near-Real-Time RAN Intelligent Controller. Operates on 10ms to 1 second control loops. Hosts xApps that directly control the RAN via the E2 interface. The fast intelligence layer. |
| **Non-RT RIC** | Non-Real-Time RAN Intelligent Controller. Operates on timescales greater than 1 second — typically minutes to hours. Hosted within the SMO. Trains ML models, runs analytics, generates A1 policies. Hosts rApps via the R1 interface. The slow, strategic intelligence layer. |
| **O-Cloud** | Cloud infrastructure — servers, storage, networking — on which O-RAN network functions run as containerized workloads. Managed by the SMO via the O2 interface. |
| **O-CU** | Open Centralized Unit. Handles upper protocol layers: RRC, SDAP, PDCP. Split into O-CU-CP and O-CU-UP connected via E1. |
| **O-DU** | Open Distributed Unit. Handles lower L2 — MAC and RLC — and part of L1 including channel coding. |
| **O-RAN** | Open Radio Access Network. Disaggregates the traditional monolithic base station into separate open-interface components, adds a programmable intelligence layer through the SMO, Non-RT RIC, and Near-RT RIC, and defines open interfaces for multi-vendor interoperability. |
| **O-RU** | Open Radio Unit. The radio hardware — antenna, RF electronics, and bottom of L1. Connected to the O-DU via fronthaul using eCPRI. |
| **O1 interface** | Between SMO and O-DU/O-CU/O-RU. Used for management: configuration, PM data collection, and fault alarms. Uses NETCONF/YANG. |
| **O2 interface** | Between SMO and O-Cloud. Used for infrastructure lifecycle management: deploying, scaling, and managing containerized network functions. |
| **Plan-Act-Observe-Reflect** | Core agentic primitive for O-RAN defined in the Agentic AI-RAN paper (arXiv:2602.24115). Timing-aware — the loop cadence is aligned with the Non-RT, Near-RT, and RT control layers. |
| **R1 interface** | Between rApps and the Non-RT RIC framework. The interface through which rApps expose and consume services from the Non-RT RIC. |
| **RAN Cortex** | Memory-augmented architecture for O-RAN agents proposed in arXiv:2505.07842. Composed of a context encoder, vector-based memory store, recall engine, and policy interface. Adds persistent memory to agents that are otherwise stateless. |
| **rApp** | Non-RT RIC Application. Third-party software running inside the Non-RT RIC via the R1 interface. Operates on long timescales. Use cases include energy saving, predictive maintenance, and interference management. |
| **RAG** | Retrieval-Augmented Generation. Retrieving relevant documents from an external knowledge base before generating a response. Extended in agentic systems to episodic memory retrieval from past network events. |
| **ReAct** | Reason plus Act. The fundamental execution loop of LLM-based agentic systems: observe, reason in a Thought step, act in an Action step, observe the result, reason again, and continue until the goal is reached. |
| **Reflection** | Agentic design pattern where the agent evaluates its own past actions after observing the outcome. Prevents premature success and improves multi-step problem solving. Implemented as the Reflect phase in the Agentic AI-RAN paper. |
| **Self-management gates** | Mechanism in the Agentic AI-RAN paper (arXiv:2602.24115) that governs when the agent acts autonomously and when it escalates to a human. Actions with impact scores above a threshold require human confirmation before execution. |
| **Shadow mode** | Deploying an agent such that it generates recommendations but executes nothing. Used to validate behavior on real data before enabling autonomous operation. |
| **Skills** | O-RAN-specific tool concept from the Agentic AI-RAN paper (arXiv:2602.24115). Thin, verifiable wrappers around controllable primitives that abstract the complexity of E2 interface calls behind a clean interface the planning agent can invoke. |
| **SMO** | Service Management and Orchestration. The top layer of the O-RAN management hierarchy. Manages network functions via O1, manages O-Cloud via O2, and hosts the Non-RT RIC. |
| **xApp** | Near-RT RIC Application. Third-party software deployed in the Near-RT RIC that subscribes to E2SM-KPM measurements and sends E2SM-RC control commands. Operates on 10ms to 1 second timescales. |
| **Y1 interface** | From the Near-RT RIC to external consumers. Used to expose RAN analytics information to applications that want to consume network intelligence without directly controlling the RAN. |
