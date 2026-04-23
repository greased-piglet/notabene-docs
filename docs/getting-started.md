# Getting Started

This walkthrough takes you from "I just downloaded the DMG" to "my first transcription worked" in roughly 5 minutes.

## Requirements

- **macOS 14 (Sonoma) or later**
- **A cloud speech-to-text API key.** In v1, Notabene targets **Azure AI Foundry** (with OpenAI-compatible `/audio/transcriptions` endpoints). You supply your own key — Notabene does not bundle any credits.
- **Internet connection.** All transcription is cloud-based in v1.

## 1. Install

1. Download the latest `Notabene.dmg` (from the release page your version points to).
2. Open the DMG and drag **Notabene.app** into `/Applications`.
3. Eject the DMG.

### The "unidentified developer" warning

Notabene is not code-signed in v1. On first launch you will see a Gatekeeper warning.

**To launch anyway:**

1. In Finder, open `/Applications`.
2. **Right-click** (or Control-click) `Notabene.app` → **Open**.
3. Click **Open** in the dialog that appears.

You only need to do this once. Normal double-click will work from then on.

## 2. First launch

On first launch, Notabene opens an **Onboarding** window that walks you through three macOS permissions and one macOS setting:

| Step | What it's for |
|---|---|
| **Microphone** | To capture your voice when you hold `Fn`. |
| **Accessibility** | To paste the transcript into whatever app has focus. |
| **Input Monitoring** | To detect `Fn` key-down / key-up system-wide. |
| **Disable built-in Fn dictation** | So Apple's built-in dictation doesn't hijack the key. |

The Onboarding window shows a **traffic-light indicator** next to each permission. Click **Refresh** after you grant a permission in System Settings to update the indicator.

Full details: [Permissions & Setup](permissions.md).

## 3. Enter your API key

Open **Settings** from the menu-bar icon (the small microphone in the menu bar).

You'll need:

- **Endpoint** — your Azure AI Foundry endpoint URL.
- **Deployment** — the transcription deployment name in your Foundry resource.
- **API version** — the API version your deployment expects (e.g. `2024-10-21` or similar).
- **API key** — your Azure Foundry API key. This is stored in the macOS **Keychain**, never in plain text on disk, and is never logged.
- **Language** — the language of speech you'll dictate in (default: `en`).

Click **Test connection** after filling these in. If the test succeeds you're ready.

## 4. Dictate

Put your cursor anywhere you can type — a note, an email, a browser text field.

1. **Hold `Fn`.** The mic icon in the menu bar animates and a floating HUD shows **Listening…** near your cursor.
2. **Speak.**
3. **Release `Fn`.** The HUD changes to **Transcribing…**, then disappears.
4. Your transcript is pasted at the cursor.

End-to-end should be under ~2 seconds for a short utterance on broadband.

## What next?

- If something didn't work, jump to [Troubleshooting](troubleshooting.md).
- If you're curious what actually happens between key-down and paste, see [How It Works](how-it-works.md).
- If you're wondering what leaves your Mac, see [Privacy](privacy.md).
