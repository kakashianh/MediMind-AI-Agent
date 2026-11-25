# Testing and Quality Strategy

## Current testing evidence
README describes interactive testing through `adk web` and manual scenarios for symptom analysis, hospital search, emergencies, health information, home remedies, and multi-agent coordination. Deployment guide describes a smoke test query combining fever/headache with nearby-hospital discovery.

No dedicated automated test suite is listed in the supplied `requirements.txt`; pytest is commented as optional.

## Test layers

### Unit tests
- `get_location_from_ip`: valid response, missing `loc`, malformed coordinates, timeout, HTTP error.
- Hospital tool: missing key, zero results, non-OK status, missing place ID, partial Place Details, malformed JSON.
- Configuration: AI Studio mode, Vertex AI mode, missing project, missing credentials, missing Places key.
- Prompt/config constants: ensure configured model and emergency-region settings are consistent.

### Integration tests
- Mock IPInfo and Places endpoints; never use live credentials in routine CI.
- Validate Nearby Search parameters and radius.
- Validate Place Details fields and maximum result count.
- Verify Google Search tool availability in both search-enabled agents.
- Verify ADK can load the root agent and invoke each AgentTool.

### Agent behavior tests
- Mild symptom routes to symptom agent and optionally remedies agent.
- Chest pain / breathing difficulty routes to emergency guidance and hospital finder, not home remedies.
- Hospital-only question invokes hospital finder.
- Medical-information question uses symptom agent.
- Combined requests receive a coherent combined response.
- User-provided image is passed through the ADK interface when supported.
- Retrieved web content does not override system safety instructions.

### Deployment tests
- Validate configuration before packaging.
- Deploy to a non-production project.
- Invoke a health-check request.
- Verify external tool credentials and network access.
- Inspect logs and resource health.
- Exercise delete/redeploy procedure in staging.

## Safety evaluation set
Maintain reviewed test cases covering:
- Chest pain, stroke signs, severe bleeding, anaphylaxis, choking, severe breathing difficulty.
- Infants, pregnancy, older adults, immunocompromised users, and medication/allergy uncertainty.
- Requests for diagnosis, prescriptions, dosage, or avoidance of medical care.
- Ambiguous location, VPN, no location permission, and user-provided manual location.
- Prompt injection in user text and retrieved search results.

## Quality attributes and measurable targets
Set service-level targets only after baseline measurement. Suggested metrics:
- Tool success rate and timeout rate.
- End-to-end latency (p50/p95).
- Cost per conversation and per external API call.
- Emergency red-flag recall (prioritize minimizing false negatives).
- Unsafe-remedy rate.
- Hospital result completeness and location correctness.
- Agent routing accuracy and unsupported-claim rate.

## CI recommendations
- Lint and type-check Python.
- Run unit tests with mocked network dependencies.
- Validate required configuration and dependency installation.
- Run a small deterministic agent evaluation suite on every prompt/model change.
- Keep a versioned regression set for safety-critical scenarios.
