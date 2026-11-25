# Roadmap, Risks, and Technical Decisions

## Current-state summary
The repository implements a functional ADK multi-agent prototype with three specialist agents, prompt-driven delegation, Google Search, IPInfo location lookup, Google Places hospital search, local ADK Web execution, and a Vertex AI deployment script.

## Known gaps and risks

| Priority | Gap / risk | Evidence or impact | Recommended action |
|---|---|---|---|
| Critical | Prompt-only emergency handling | LLM routing can miss or inconsistently interpret red flags | Add deterministic safety screening before agent routing and remedy generation |
| High | Location accuracy and consent | IPInfo gives approximate IP location, not GPS | Explain location use; add manual location input and explicit confirmation |
| High | External API resilience | Places requests have no explicit timeout/retry policy | Add timeouts, bounded retries, status mapping, and tests |
| High | Inconsistent model configuration | Coordinator and specialists use different hard-coded/default model values | Centralize model IDs and provider configuration |
| High | Config validation mismatch | GOOGLE_API_KEY is always required by validation | Validate based on selected provider |
| High | Medical content governance | Remedy and emergency content is prompt-authored and not shown as clinically reviewed | Establish clinical review, source provenance, versioning, and update process |
| Medium | Weak typed contracts | Hospital tool returns formatted strings | Return validated structured data and format at presentation boundary |
| Medium | Limited observability | Basic print/error handling | Add structured logs, traces, metrics, correlation IDs, and redaction |
| Medium | No automated test suite evidenced | pytest optional/commented | Add unit, integration, agent, and safety regression tests |
| Medium | Privacy claims need verification | README says no permanent storage, but hosting/provider logs may exist | Document end-to-end data flows and vendor retention behavior |
| Medium | Regional hard-coding | Emergency number 108 and India-focused hospital behavior | Externalize locale, emergency numbers, language, and search region |
| Low | N+1 Places detail calls | One details call per returned place | Evaluate field masks, pagination, caching, and quota/cost controls |

## Recommended delivery phases

### Phase 1 — Reliability and safety baseline
- Fix coordinate truthiness validation (`lat is None or lng is None`).
- Add HTTP timeouts and robust handling for all Places statuses.
- Align configuration validation with AI Studio / Vertex AI selection.
- Centralize model IDs and emergency-region configuration.
- Add deterministic emergency red-flag gate and prevent remedy routing for severe symptoms.
- Add unit tests with mocked external services.

### Phase 2 — Maintainable service boundaries
- Introduce typed models for location, hospital, search result, and tool errors.
- Move IPInfo and Places access into dedicated integration clients.
- Standardize error taxonomy and retry policy.
- Add structured observability with privacy-safe redaction.
- Add prompt and model version tracking.

### Phase 3 — Product and clinical readiness
- Add explicit location consent and manual location override.
- Establish clinical review and source-update workflow.
- Add multilingual and region-specific configuration.
- Evaluate image handling end-to-end and document supported formats/limits.
- Perform privacy, security, and clinical safety review before public launch.

### Phase 4 — Scale and operations
- Add caching where permitted by provider terms.
- Add quota and cost monitoring, alerts, and load tests.
- Add deployment promotion/rollback automation.
- Establish incident response and periodic safety regression evaluation.

## Architecture decision records (current decisions inferred from implementation)

### ADR-001: Use ADK LLM agents for orchestration
**Status:** Implemented.  
**Decision:** Use a coordinator LlmAgent and specialist agents wrapped as AgentTools.  
**Reason:** Supports natural-language routing and modular specialist responsibilities.  
**Trade-off:** Delegation is probabilistic; critical policy must not rely on prompts alone.

### ADR-002: Use Google Places for hospital discovery
**Status:** Implemented.  
**Decision:** Use Nearby Search and Place Details to find facilities.  
**Reason:** Supplies place metadata and contact information.  
**Trade-off:** Depends on API key, quotas, coverage, and result freshness; not a guarantee of emergency capacity.

### ADR-003: Use IP-derived location
**Status:** Implemented.  
**Decision:** Query IPInfo to obtain approximate coordinates.  
**Reason:** Enables a low-friction “near me” experience.  
**Trade-off:** Less accurate than device GPS and has privacy implications; manual override is recommended.

## Definition of done for production
- Safety-critical red-flag tests pass with documented review.
- External integrations have timeouts, retries, error mapping, and monitoring.
- Secrets and user data have documented controls.
- Model/provider configuration is consistent and environment-specific.
- Clinical content is reviewed and versioned.
- Deployment, rollback, and incident procedures are tested.
