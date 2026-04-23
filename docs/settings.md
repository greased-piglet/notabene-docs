# Settings

Open Settings from the menu-bar icon → **Settings…**.

## Fields

### Endpoint

The base URL of your speech-to-text provider.

- **Azure AI Foundry** example: `https://<your-resource>.cognitiveservices.azure.com`
- Any OpenAI-compatible endpoint that exposes `/audio/transcriptions` should work, as long as it follows the same multipart-form request shape.

Do **not** include `/openai/deployments/...` in the endpoint — Notabene composes the full request path from the endpoint + deployment + API version.

### Deployment

Your Azure Foundry deployment name for a transcription model (e.g. a `gpt-4o-transcribe` deployment, or a Whisper deployment).

### API version

The API version string your Foundry deployment expects, e.g. `2024-10-21` or a preview variant. Check your Foundry resource's sample code — the version Notabene needs is the one that appears in the `api-version=` query parameter.

### API key

Your Azure API key.

- **Stored in the macOS Keychain**, service name `com.greasedpiglet.Notabene.azure`, account `api-key`.
- **Never** written to `UserDefaults`, config files, logs, or crash reports.
- **Never** sent anywhere except the endpoint you configured, over HTTPS.

### Language

The BCP-47 language code for the speech you'll be dictating (default `en`). Common values:

- `en` — English
- `en-US` / `en-GB` — regional variants
- `fr`, `de`, `es`, `it`, `nl`, `pt`, `ja`, `zh`, etc.

If you regularly switch languages, you'll need to change this field between sessions in v1. Per-session language switching is on the enhancements list.

---

## Test connection

The **Test connection** button makes a minimal request against your configured endpoint and reports success or failure.

- ✅ **Success** — your endpoint, deployment, API version, and key are all wired up correctly.
- ❌ **Failure** — you'll see a short error message. See [Troubleshooting](troubleshooting.md#test-connection-failures) for common causes.

Running **Test connection** is free of charge in the sense that it doesn't upload real audio, but depending on your provider's billing model a tiny request may still be counted. Check your provider's pricing if this matters to you.

---

## OpenAI-compatible providers

In v1, Notabene is primarily tested against Azure AI Foundry. Other providers that expose an OpenAI-compatible `/audio/transcriptions` endpoint should work with minor field adjustments — full multi-provider support is tracked as a planned enhancement.

---

## Where settings live

- **Endpoint, deployment, API version, language:** `UserDefaults` (standard macOS preferences).
- **API key:** macOS Keychain only.

Removing Notabene.app does not automatically remove these; to fully wipe:

1. Quit Notabene.
2. Delete `/Applications/Notabene.app`.
3. Remove preferences: `defaults delete com.greasedpiglet.Notabene` in Terminal.
4. Remove Keychain entry: open **Keychain Access**, search for `com.greasedpiglet.Notabene.azure`, and delete the entry.
