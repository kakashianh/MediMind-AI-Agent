# Agent Components

## 1. Coordinator — `healthcare_coordinator`
**Source:** `healthcare_agents/agent.py`, `COORDINATOR_PROMPT`.

Responsibilities:
- Understand the user request.
- Route to symptom analysis, hospital discovery, and/or home-remedy guidance.
- Combine specialist outputs.
- Prioritize emergencies and include disclaimers.

Implementation:
- ADK `LlmAgent`.
- Model is currently set directly to `gemini-3-pro-preview` in `agent.py`.
- Specialist agents are exposed as `AgentTool` instances.

## 2. Symptom Analyzer — `symptom_analyzer`
**Source:** `healthcare_agents/symptom_agent.py`, `SYMPTOM_ANALYZER_PROMPT`.

Responsibilities:
- Explain symptoms and health conditions in accessible language.
- Ask clarifying questions.
- Provide preliminary, non-diagnostic assessments.
- Discuss visible features in user-provided images where the model interface supports image input.
- Provide emergency guidance and use Google Search for additional information.

Implementation:
- ADK `LlmAgent`.
- Model from `config.DEFAULT_MODEL`.
- Tool: ADK `google_search`.

Constraints:
- No structured symptom classifier, validated clinical knowledge base, or dedicated image preprocessing pipeline is evidenced.
- Prompt instructions explicitly prohibit definitive diagnosis and recommend professional evaluation.

## 3. Hospital Finder — `hospital_finder`
**Source:** `healthcare_agents/hospital_finder_agent.py`, `HOSPITAL_FINDER_PROMPT`.

Responsibilities:
- Determine approximate user location from IP.
- Search nearby hospitals.
- Return hospital name, address, phone, rating, website, and opening status when available.
- Remind users to call 108 for emergencies.

Implementation:
- ADK `LlmAgent`, model currently hard-coded as `gemini-2.5-flash`.
- Function tool: `find_nearby_hospitals(radius=5000)`.
- `get_location_from_ip()` requests `https://ipinfo.io/json` with a 10-second timeout.
- Google Places Nearby Search uses type `hospital`; at most 10 results are enriched through Place Details.

Known implementation details:
- Hospital API requests do not currently specify explicit timeouts.
- Location validation uses `if not lat or not lng`, which incorrectly treats valid zero coordinates as missing.
- The Places response path expects status `OK`; alternate statuses such as `ZERO_RESULTS` need explicit handling.
- Each result may cause an additional Place Details request, creating an N+1 API pattern.
- The function returns formatted text rather than structured records.

## 4. Home Remedies Advisor — `home_remedies_advisor`
**Source:** `healthcare_agents/home_remedies_agent.py`, `HOME_REMEDIES_PROMPT`.

Responsibilities:
- Suggest natural/home-care measures only for mild symptoms.
- Explain preparation and usage.
- Mention contraindications, allergies, and escalation conditions.
- Use Google Search for additional information.

Implementation:
- ADK `LlmAgent`, model currently hard-coded as `gemini-2.5-flash`.
- Tool: ADK `google_search`.

Safety boundary:
- Prompt excludes severe pain, breathing difficulty, chest pain, severe allergic reactions, high fever, persistent symptoms, and emergencies.
- Prompt content is a behavioral instruction, not a guaranteed clinical safeguard; deterministic policy enforcement is recommended.

## Shared prompt and configuration modules
- `prompt.py`: coordinator and specialist instructions; also contains future/reference dictionaries for emergency guidance, common symptoms, health information, and home remedies.
- `config.py`: `HealthcareAgentConfig`, environment-variable parsing, validation, Vertex AI configuration helpers.
- The dictionaries marked “To Be Use in the Future” are not wired as tools or runtime data sources in the current agent modules.
