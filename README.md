# AgentTrace: A Decoupled Observability & LLM-as-a-Judge Framework

![Python](https://img.shields.io/badge/python-3.8+-blue) ![License: MIT](https://img.shields.io/badge/License-MIT-green)

---

## Executive Summary (Non-Technical)

### The Problem
Most AI today is unpredictable. Ask it the same question twice and you may get two different answers — one brilliant, one wrong. For a business operating at scale, this unpredictability is a liability that blocks AI from being used in high-stakes workflows.

### The Solution: A Digital Supervisor
**AgentTrace** is a supervision layer for AI Agents.

- It assigns a unique ID and timestamp to every **execution step** in an agent's decision path, creating a complete, auditable record of how a result was produced.
- It then deploys a second, specialized "Senior AI" — the **Judge** — to automatically audit those logs against business rules or a golden dataset.

### The Result
This framework shifts the conversation from *"it feels like the AI works"* to *"this AI has a verified 90% reliability rate across 100 stress-tested execution steps."* Companies can deploy AI with confidence: errors are caught and flagged automatically before they reach a user.

---

## Key Features

- **Decoupled Instrumentation:** Plug-and-play architecture compatible with LangChain, CrewAI, or raw API calls — no refactoring of core logic required.
- **Automated Audit (LLM-as-a-Judge):** A secondary evaluation layer that automatically grades agent responses for factuality and detail at every execution step.
- **Persona-Based Stress Testing:** Built-in simulation loop to test agent behavior across diverse user personas (e.g., "The Impatient Customer," "The Confused User").
- **Reliability Dashboard:** Integrated visualization via Matplotlib and Pandas to track success rates and identify failure modes.

---

## System Architecture

AgentTrace is built on the **Observer Pattern**, separating three concerns:

| Role | Component | Responsibility |
|:---|:---|:---|
| Doing | The Agent | Executes the task |
| Watching | `AgentTrace` Class | Records every execution step with UUID + timestamp |
| Judging | `AgentJudge` Class | Evaluates traces against business rules or a golden dataset |

This separation means the observability layer is entirely decoupled from the agent logic — you instrument without rewriting.

---

## Getting Started

### Prerequisites

```bash
pip install pandas matplotlib
```

### Usage Example

```python
from agent_trace import AgentTrace, AgentJudge

# 1. Initialize the system
tracer = AgentTrace(model_name="GPT-4")
judge = AgentJudge()

# 2. Capture an execution step
tracer.capture_step(
    agent_name="SupportBot",
    input_text="My account has been locked for 3 days — I need this fixed now.",
    output_text=(
        "I understand the urgency. To unlock your account immediately: "
        "navigate to Settings > Security > Account Recovery and follow "
        "the verification steps. If that fails, reply here with your "
        "account email and I will escalate to Tier 2 support."
    ),
    metadata={"tokens": 62, "latency": 0.38, "persona": "impatient_customer"}
)

# 3. Run an automated audit on that execution step
report = tracer.get_report()[0]
audit = judge.evaluate_factuality(report)
print(f"Execution Step Audit: {audit['audit_note']}")
# Output: "Execution Step Audit: PASS — Response meets detail and factuality threshold."
```

---

## Research & Results

I ran a **100-execution-step stress test** across 5 global cities and 4 distinct user personas to treat the agent as a true random variable.

| Metric | Result |
|:---|:---|
| **Total Execution Steps** | 100 |
| **Overall Success Rate** | 90.0% |
| **Avg. Latency per Step** | 0.45s |
| **Primary Failure Mode** | Response length below threshold |

### Key Findings

- The agent maintained a **90% factuality rate** across all persona types.
- **Friendly** and **formal** personas: near-perfect performance.
- **Impatient** persona: 10% drop in response detail — identified via execution step logs, root cause traced to prompt compression under simulated urgency.

> Identifying *why* an agent fails is 80% of the work in AI infrastructure. AgentTrace gives you the exact execution step log to find the failure point and fix it.

---

## Roadmap

- [ ] **Phase 2:** Integrate with Pinecone for RAG-specific retrieval auditing.
- [ ] **Phase 3:** Implement Cost-Per-Task tracking for token optimization.
- [ ] **Phase 4:** Export traces directly to PostgreSQL for long-term drift analysis.

---

## Author

**Sydney A. Horton**
- **Role:** AI Strategist & Infrastructure Builder
- **Location:** Atlanta, GA
- **Focus:** Building the supervision and observability layer for production AI Agent workforces.
- **GitHub:** [hortonllc6-coder](https://github.com/hortonllc6-coder/Agentic-Trace)

---

## License

Distributed under the MIT License. See `LICENSE` for more information.
