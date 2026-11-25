# Configuration and Deployment

## Runtime configuration
Configuration is loaded from environment variables via `python-dotenv`.

| Variable | Purpose | Current behavior |
|---|---|---|
| `GOOGLE_API_KEY` | Google AI Studio API key | Read by config; validation currently requires it even when Vertex AI is enabled |
| `GOOGLE_GENAI_USE_VERTEXAI` | Select Vertex AI mode | Parsed as boolean by lowercasing and comparing to `true` |
| `GOOGLE_CLOUD_PROJECT` | Vertex AI project | Required by validation when Vertex AI is enabled |
| `GOOGLE_CLOUD_LOCATION` | Vertex AI region | Defaults to `us-central1` |
| `GOOGLE_APPLICATION_CREDENTIALS` | Service-account credential path | Required by validation for Vertex AI |
| `GOOGLE_PLACES_API_KEY` | Places API key | Required for hospital lookup |
| `DEFAULT_MODEL` | Default specialist model | Defaults to `gemini-3-pro-preview` |

The coordinator and two specialist models are currently hard-coded in agent modules; therefore `DEFAULT_MODEL` does not control every agent.

## Local setup
1. Use Python 3.9+ as stated in README/deployment guide.
2. Install `requirements.txt`.
3. Run `python setup_env.py` or create `.env` manually.
4. Add valid Google AI and Places credentials.
5. Run `python setup_env.py --validate`.
6. Start the local ADK interface with `adk web`.

## Vertex AI deployment
The repository documents a `deploy.env` file with:
- `GOOGLE_CLOUD_PROJECT`
- `GOOGLE_CLOUD_LOCATION`
- `GOOGLE_CLOUD_STORAGE_BUCKET`
- `GOOGLE_PLACES_API_KEY`
- `GOOGLE_API_KEY`
- Optional deployment name and description.

Commands documented:
```bash
python deploy.py create
python deploy.py test <resource_id>
python deploy.py delete <resource_id>
```

The deployment guide also documents enabling Vertex AI, Cloud Resource Manager, Cloud Storage, and Places-related APIs; creating a staging bucket; assigning least-privilege IAM roles; and reviewing Vertex AI Agent Engine logs.

## Environment separation
Recommended environments:
- **Development:** isolated API keys, low quotas, local ADK.
- **Staging:** separate cloud project/bucket and test credentials.
- **Production:** restricted service account, restricted API keys, budget alerts, monitoring, and a documented rollback/redeploy procedure.

Never commit `.env`, `.env1`, `deploy.env`, service-account JSON files, or real API keys.

## Configuration defects / cleanup
1. `HealthcareAgentConfig.validate_config()` requires `GOOGLE_API_KEY` unconditionally, although Vertex AI may use service-account authentication.
2. README's example configuration and `setup_env.py` template include differing optional variables; setup template contains US-specific emergency contact variables while application config hard-codes India 108.
3. Deployment guide uses `deploy.env`, while setup helper creates `.env`; document these as separate runtime/deployment configurations.
4. Model IDs are duplicated and inconsistent across agents.
5. Fail fast at startup with actionable errors, and validate credentials according to the selected provider.
