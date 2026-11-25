# MediMind AI Agent — Software Design Index

| Metadata | Value |
|---|---|
| System | MediMind AI Agent |
| Repository | https://github.com/kakashianh/MediMind-AI-Agent |
| Design document set | `software-design/` |
| Product type | Conversational healthcare information assistant |
| Primary framework | Google Agent Development Kit (ADK) |
| Runtime | Python |
| LLM platform | Google Gemini / Google GenAI; optional Vertex AI |
| Core agents | Coordinator, Symptom Analyzer, Hospital Finder, Home Remedies Advisor |
| External services | Google Search, Google Places API, IPInfo |
| Primary deployment target | Local ADK Web; Google Cloud Vertex AI Agent Engine |
| Target context | India-focused healthcare discovery; emergency number configured as 108 |
| Design status | As-is architecture plus clearly labeled recommendations |
| Source baseline | Repository source inspected on 2026-11-25 |

## Scope
This index records the implemented system architecture and the main design constraints. It is not a clinical specification and does not claim that recommended improvements are implemented.

## Document map
1. [System Context](01-System-Context.md) — actors, boundaries, capabilities, assumptions.
2. [Architecture](02-Architecture.md) — logical components, runtime and deployment views.
3. [Agent Components](03-Agent-Components.md) — responsibilities, tools, models, implementation notes.
4. [Workflows](04-Workflows.md) — user journeys and sequence diagrams.
5. [Integrations and APIs](05-Integrations-and-APIs.md) — external systems, endpoints, dependencies.
6. [Configuration and Deployment](06-Configuration-and-Deployment.md) — environment variables and deployment lifecycle.
7. [Security, Privacy, and Safety](07-Security-Privacy-and-Safety.md) — sensitive data, security controls, clinical safety.
8. [Testing and Quality](08-Testing-and-Quality.md) — test strategy and quality attributes.
9. [Roadmap and Risks](09-Roadmap-and-Risks.md) — gaps, priorities, phased improvements, inferred ADRs.

## Source of truth
Implementation details are derived from `README.md`, `requirements.txt`, `setup_env.py`, `deploy.py`, `deploy_guide.md`, and the `healthcare_agents/` package. When this design set and source code differ, the current source code takes precedence. Prompt statements are not treated as proof of a fully implemented capability.

## Key implementation entry points
- `healthcare_agents/agent.py`
- `healthcare_agents/symptom_agent.py`
- `healthcare_agents/hospital_finder_agent.py`
- `healthcare_agents/home_remedies_agent.py`
- `healthcare_agents/prompt.py`
- `healthcare_agents/config.py`
- `deploy.py`
