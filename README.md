# 🚀 The Agentic MLOps Stack
### Deploying High-Performance Twi ASR with Secure Agentic Sandboxes

Welcome to the documentation for a production-ready AI infrastructure project. This series documents the journey from a raw Whisper model to a fully containerized, agent-orchestrated transcription ecosystem.

## 🛠 Tech Stack
*   **AI Engine:** OpenAI Whisper (Small) fine-tuned for Twi (Serlabs).
*   **Inference Orchestration:** BentoML, PyTorch, Librosa.
*   **Agent Architecture:** ADK (Agent Development Kit), Docker Sandboxes.
*   **Infrastructure:** Docker Compose, GitHub-ready CI/CD patterns.

---

## 📚 Series Roadmap

### Chapter 1: The MLOps Foundation (Inference)
Learn how to transform heavy ML models into lightweight, high-performance microservices.
1.  **[The Strategy](./1_mlops_foundation/1_the_strategy.md):** Why models belong in containers.
2.  **[Implementation](./1_mlops_foundation/2_implementation.md):** Building the Twi ASR server with BentoML.
3.  **[Deep Dive: Whisper Twi](./1_mlops_foundation/3_deep_dive_whisper_twi.md):** Model architecture and FP16 optimizations.
4.  **[Scaling & Ops](./1_mlops_foundation/4_scaling_ops.md):** Health checks, resource limits, and RTF tracking.

### Chapter 2: The Agentic Layer (Sandboxing)
Moving from "Chat" to "Action" by providing agents with safe workbenches.
5.  **[Why Sandboxing?](./2_agent_sandboxing/5_why_sandboxing.md):** Solving the security risks of LLM code execution.
6.  **[Building the Workbench](./2_agent_sandboxing/6_building_the_workbench.md):** Ephemeral Docker environments for agents.
7.  **[Advanced Isolation](./2_agent_sandboxing/7_advanced_isolation.md):** Managed state, network egress, and human-in-the-loop.

### Chapter 3: Integration & Deployment
The "Holy Grail"—connecting the brain to the workbench.
8.  **[The Agentic Bridge](./3_integration_deployment/8_the_agentic_bridge.md):** Orchestrating inference models via sandboxed agents.
9.  **[One-Click Deployment](./3_integration_deployment/9_one_click_deployment.md):** Launching the full stack with Docker Compose.

---

## 🌟 Key Highlights
*   **Security-First:** Implemented strict Docker isolation for AI-generated code.
*   **Optimized Performance:** Reduced inference latency using FP16 and optimized base images.
*   **Production Ready:** Full support for health monitoring, RTF metrics, and horizontal scaling.
*   **Low-Resource Focus:** Specialized support for Twi ASR, bridging the gap for under-represented languages.

---
*Created with ❤️
