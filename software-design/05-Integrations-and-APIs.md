# Integrations and APIs

## Google Gemini / Google GenAI
**Purpose:** LLM inference for coordinator and specialist agents.

- ADK `LlmAgent` is the agent abstraction.
- Coordinator model: literal `gemini-3-pro-preview` in `agent.py`.
- Symptom model: `config.DEFAULT_MODEL`, default `gemini-3-pro-preview`.
- Hospital and home-remedy models: literal `gemini-2.5-flash`.
- AI Studio / Vertex AI selection is represented by `GOOGLE_GENAI_USE_VERTEXAI`.

**Risks:** model availability and naming may change; the configured model values are inconsistent and should be centralized.

## Google Search tool
**Consumers:** Symptom Analyzer and Home Remedies Advisor.

**Purpose:** retrieve additional public information in response to user questions. Search results should be treated as untrusted external content and summarized with source awareness. Current code uses ADK's built-in `google_search` tool; no custom search-index or source persistence layer is present.

## IPInfo
**Endpoint:** `GET https://ipinfo.io/json`

**Purpose:** approximate location derived from request IP.

**Returned fields used:** `loc` (latitude,longitude), `city`, and `region`.

**Timeout:** 10 seconds in current code.

**Limitations:** IP geolocation may be inaccurate, especially with VPNs, mobile networks, proxies, and carrier NAT. It is not precise device GPS. Users should be offered manual location entry and a privacy explanation.

## Google Places API
**Endpoints used:**
- Nearby Search: `https://maps.googleapis.com/maps/api/place/nearbysearch/json`
- Place Details: `https://maps.googleapis.com/maps/api/place/details/json`

**Nearby Search parameters:** `location`, `radius` (default 5000 metres), `type=hospital`, `key`.

**Place Details fields:** `name`, `formatted_address`, `formatted_phone_number`, `rating`, `opening_hours`, `website`.

**Output:** up to 10 formatted facility entries. Opening status is shown as “Open now” only when the response has a truthy `opening_hours.open_now`; otherwise it is “Status unknown”.

**Required improvements:** explicit connect/read timeouts, status mapping, quota-aware retries, key restriction, response schema validation, and structured logging. Consider current Places API migration/version requirements before changing endpoints.

## Vertex AI Agent Engine
**Purpose:** optional managed deployment.

**Configuration described in repository:** Google Cloud project, region, Cloud Storage staging bucket, Places API key, Google AI API key, deployment name/description.

**Operational actions described:** create, test, delete; inspect/list resources and logs with gcloud commands.

## Dependency inventory
From `requirements.txt`:
- `google-adk>=1.15.1`
- `google-genai==1.40.0`
- `requests>=2.31.0`
- `python-dotenv>=1.0.0`
- Optional deployment dependency: `google-cloud-aiplatform[agent_engines]>=1.93.1`

Pinning and compatibility should be validated in CI; the optional deployment package should be installed in the deployment environment when required.
