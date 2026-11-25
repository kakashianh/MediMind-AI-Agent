# System Context

## Purpose
MediMind AI Agent is a conversational healthcare information assistant implemented with Google's Agent Development Kit (ADK). It coordinates three specialist agents: symptom analysis, hospital discovery, and home-remedy guidance.

## Users and external actors
- **Patient / general user:** describes symptoms in text and may provide images; asks health-information or nearby-hospital questions.
- **Healthcare professional (indirect stakeholder):** may be consulted by the user; the system does not replace clinical assessment.
- **Google Gemini / GenAI:** language-model inference for coordinator and specialist agents.
- **Google Search:** retrieval tool used by symptom and home-remedy agents.
- **IPInfo:** IP-derived approximate location lookup used by hospital discovery.
- **Google Places API:** nearby hospital search and place-detail lookup.
- **Google Cloud Vertex AI Agent Engine:** optional deployment target.

## System boundary
The application owns orchestration, prompts, tool wrappers, configuration loading, and deployment commands. It does not own medical source-of-truth data, emergency dispatch, hospital inventory, user identity, or a persistent patient record.

## Main capabilities
1. Interpret a natural-language request and delegate to one or more specialist agents.
2. Produce preliminary, non-diagnostic symptom information from text and image inputs.
3. Find nearby hospitals using IP-derived coordinates and Google Places.
4. Offer low-risk home-care information for mild symptoms, with escalation guidance.
5. Run locally through ADK Web and deploy to Vertex AI Agent Engine.

## Explicit non-goals
- Diagnosis, prescribing, treatment planning, or clinical decision support.
- Replacing emergency services or a licensed clinician.
- Guaranteed real-time bed, department, doctor, or appointment availability.
- Longitudinal patient records, authentication, billing, or EHR integration (not evidenced in the current source).

## Assumptions and constraints
- The documented emergency number and healthcare context are India-focused (108).
- AI Studio and Vertex AI are described as alternative model execution modes in configuration documentation.
- Google Places and Google Search behavior, quotas, availability, and pricing are external dependencies.
- Current source uses a fixed coordinator model in `agent.py`; specialist models are configured separately or hard-coded.

## Evidence boundary
This document describes behavior evidenced by `README.md`, `healthcare_agents/agent.py`, specialist modules, `prompt.py`, `config.py`, and deployment scripts. Prompt claims (for example, image understanding or directions) are not proof of a dedicated image-processing pipeline or routing/directions API. See [Architecture](02-Architecture.md) and [Known Gaps](09-Roadmap-and-Risks.md).
