# Troubleshooting

If something isn't working, find the symptom below.

---

## Install / launch

### "Notabene can't be opened because it is from an unidentified developer"

Expected on first launch in v1. Notabene is not code-signed yet.

**Fix:** In Finder, **right-click** (or Control-click) `Notabene.app` in `/Applications` → **Open** → **Open**. One-time only.

### Nothing happens when I click the DMG

Quit any previously-mounted Notabene DMG (check the sidebar in Finder). Then re-open the DMG. If it still fails, delete and re-download — an interrupted download can leave a corrupt DMG.

### The menu-bar icon doesn't appear

- Make sure Notabene is actually running (**Activity Monitor** → search **Notabene**).
- If your menu bar is crowded, macOS can hide icons — try [Bartender](https://www.macbartender.com/) or drag the icon out of the overflow.
- Quit and relaunch.

---

## Permissions

### The Onboarding window shows 🔴 even after I granted a permission

macOS sometimes takes a few seconds to propagate permission changes.

**Fix:**

1. Wait a few seconds.
2. Click **Refresh** in the Onboarding window.
3. If still red, quit and relaunch Notabene — macOS caches some permission states per launch.

### "Accessibility" grant keeps resetting

This usually happens when you've moved or replaced Notabene.app, or reinstalled over an older version.

**Fix:** **System Settings → Privacy & Security → Accessibility** → remove Notabene → add it back via the **+** button.

### Input Monitoring won't enable

Some managed Macs (corporate MDM) have Input Monitoring locked by policy. Check with your IT admin if you cannot toggle the entry on.

---

## Test connection failures

### `401 Unauthorized`

Your API key is wrong, expired, or does not have access to the deployment.

**Fix:** open your provider's dashboard, confirm the key is valid and tied to the right resource, and paste it back into Settings.

### `404 Not Found`

The deployment name, endpoint, or API version is wrong.

**Fix:**

- Double-check **Deployment** matches the deployment name you created in Azure AI Foundry exactly (case-sensitive).
- **Endpoint** should be the base URL (e.g. `https://<resource>.cognitiveservices.azure.com`), not the full `/openai/deployments/...` path.
- **API version** should match what your Foundry deployment expects — check the sample code on the Foundry resource page.

### `400 Bad Request`

Usually a mismatched API version, or the deployment isn't a transcription model.

**Fix:**

- Confirm the deployment is a speech-to-text model (Whisper-family, `gpt-4o-transcribe`, etc.), not a chat or embeddings model.
- Try a different API version. Azure sometimes requires a specific `api-version` for preview features.

### `403 Forbidden`

Your key exists but doesn't have access to this resource / deployment.

**Fix:** confirm role assignments / subscription status in Azure.

### `Timeout` or network error on Test Connection

- Check your internet connection.
- Check whether your firewall / VPN / corporate proxy blocks the endpoint.
- Try from a different network to rule out corporate network issues.

---

## Runtime errors

### HUD says "Error: network"

Your machine couldn't reach the endpoint. Retry, check Wi-Fi, check that the endpoint resolves (`ping <your-resource>.cognitiveservices.azure.com`).

### HUD says "Error: 4xx"

Same diagnosis as Test Connection — your config is wrong. Open Settings and run Test Connection to get the specific error code.

### HUD says "Error: 5xx"

Provider-side problem. Retry in a moment. If persistent, check your provider's status page.

### HUD says "Error: audio"

Notabene couldn't capture audio. Usually a Microphone permission issue — check **System Settings → Privacy & Security → Microphone**. Also possible if another app has grabbed the input device exclusively.

---

## Transcription quality

### Transcripts are wrong / badly punctuated

- Try a different provider / model via Settings.
- Ensure **Language** matches the language you're actually speaking.
- Speak at normal volume with the mic ~30 cm away.
- Proper nouns, acronyms, and technical terms benefit from "prompt biasing" — configurable prompt support is on the roadmap.

### Transcripts miss the first word

You might be releasing `Fn` before finishing the last word, or starting to speak before pressing. Press first, pause a quarter-second, speak, finish, release.

### Paste target is wrong

Notabene pastes into whatever app has **keyboard focus** at the moment you release `Fn`. If you clicked into a window but didn't type there yet, focus may be on a different element.

**Fix:** click into the text field you want to dictate into, confirm the cursor is blinking there, then hold `Fn`.

### Nothing pastes, but transcript seems fine

- Check **Accessibility** permission — this is required for the synthetic ⌘V to work.
- Some older apps don't accept synthetic paste events. Try pasting manually from the clipboard (⌘V) to confirm — if that works, the transcript made it to the pasteboard, and the issue is just the synthetic-event path.

---

## Still stuck?

Open an issue on the [main repo](https://github.com/greased-piglet/notabene/issues) with:

- macOS version
- Notabene version (About → version number)
- What you did, what you expected, what happened
- The exact HUD error string if any
