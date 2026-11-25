# Security, Privacy, and Healthcare Safety

## Sensitive-data classification
User messages and images may contain health information and personal identifiers. Treat all conversation content as sensitive even though the current README states that the application does not permanently store personal health information.

## Current behavior evidenced in source
- API credentials are read from environment variables.
- IP-derived location is requested from IPInfo during hospital lookup.
- Hospital lookup sends coordinates and a Places API key to Google.
- No application-owned patient database or durable medical record store is evidenced.
- Prompts instruct agents not to provide definitive diagnoses and to recommend professional care.
- India emergency number 108 is hard-coded in configuration and prompts.

## Security controls required for production
- Store secrets in a managed secret store; never commit credentials.
- Restrict Google API keys by API and application/environment; rotate exposed keys.
- Use least-privilege service accounts and separate projects for environments.
- Enforce HTTPS for all integrations.
- Add request timeouts, bounded retries, rate limiting, and quotas.
- Redact API keys, user text, images, and precise location from logs.
- Define retention, deletion, access, and incident-response policies.
- Review vendor data-processing terms and applicable privacy/health-data law for target markets.
- Validate and sanitize tool inputs and external API responses.

## Location privacy
IP-based location is approximate, can be wrong, and is still personal data. Explain the mechanism before use, provide manual location override, and avoid retaining coordinates longer than needed. Do not describe IP lookup as precise GPS.

## Clinical safety policy
The system is informational and must not diagnose, prescribe, or delay urgent care.

Recommended response priority:
1. Detect emergency indicators and provide immediate escalation instructions.
2. Recommend contacting local emergency services / a qualified clinician.
3. Only then provide supporting general information.
4. Offer home remedies only after ruling out obvious red flags and only for mild concerns.
5. Communicate uncertainty and limitations.

The existing prompts mention 108 and India. Before deployment in another country, emergency numbers and escalation language must be localized and clinically reviewed.

## Safety gaps to address
- Prompt-only safety is probabilistic. Add deterministic red-flag screening and a safety gate before remedy suggestions.
- Add age, pregnancy, allergy, medication, and relevant contraindication checks where clinically appropriate; do not infer missing facts.
- Remove or clinically validate potentially unsafe remedy instructions. In particular, steam inhalation with hot water and essential oils can create burn/irritation risks; salt-water or other dosing advice needs clear limits.
- Ensure emergency guidance is sourced from current, authoritative clinical guidance and reviewed by qualified professionals.
- Do not present hospital ratings or opening status as a guarantee of emergency capability or availability.
- Add abuse, prompt-injection, and malicious retrieved-content defenses; external search results must never override safety policy.
