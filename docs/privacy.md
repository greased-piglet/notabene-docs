# Privacy

A plain-English summary of what Notabene does with your data.

## TL;DR

- **Audio is sent only to the endpoint you configure.** Not to Greased Piglet. Not to anyone else.
- **No telemetry.** v1 has no analytics, crash reporting, or usage tracking at all.
- **API keys live in the macOS Keychain.** Never in plain text on disk, never in logs.
- **Input Monitoring** only observes the `Fn` key. No other keystrokes are read, logged, or transmitted.
- **Transcripts are pasted and discarded.** Not saved by Notabene.

---

## What leaves your Mac

| Data | Goes where | When |
|---|---|---|
| Your voice recording | The endpoint you configured in Settings, over HTTPS | When you release `Fn` after dictating |
| Your API key | The endpoint you configured, as an `api-key` request header | With each transcription request |
| Language code | The same endpoint, as a form field | With each transcription request |

Nothing else is transmitted. No anonymous IDs, no OS version, no crash traces, no usage counts.

## What does *not* leave your Mac

- Keystrokes (other than the fact that `Fn` was pressed/released, which is never transmitted)
- Mouse or trackpad activity
- Screen content
- File contents
- The contents of other apps (focused or otherwise)
- Any prior pasteboard content
- Your transcripts, after they're pasted

## Storage on your Mac

| Where | What | Notes |
|---|---|---|
| macOS **Keychain** (`com.greasedpiglet.Notabene.azure`) | Your API key | Standard Keychain protection |
| macOS **UserDefaults** (`com.greasedpiglet.Notabene`) | Endpoint, deployment, API version, language, UI preferences | No secrets |
| **Memory only** (not disk) | Audio recording during a dictation session | Discarded immediately after upload |
| **System pasteboard** | The transcript, momentarily | Restored to previous pasteboard contents (where feasible) after paste |

Notabene does **not** write logs, transcripts, or audio files to disk.

## Input Monitoring — scope

macOS requires Input Monitoring permission for any app that reads key events outside its own windows. Notabene uses this permission narrowly:

- The event tap filters for **`Fn` key-down and key-up only**.
- No other keystrokes are inspected, stored, or transmitted.
- No keystroke timing or metadata is collected beyond what's needed to know when to start and stop recording.

If you want to verify this, the source code is in the [main repo](https://github.com/greased-piglet/notabene). The relevant file is `Notabene/Hotkey/GlobalKeyMonitor.swift`.

## Your provider's privacy practices

Notabene sends audio and an API key to the speech-to-text provider you configure. **Your provider's privacy policy applies to that data** once it leaves your Mac.

Azure AI Foundry, OpenAI, and most other major cloud STT providers publish retention and training-use policies. Check the policy for the specific resource/deployment you've configured. If you're dictating sensitive content, verify:

- Whether your provider retains audio or transcripts.
- Whether your provider uses your data to train models.
- Whether retention/training can be disabled via resource settings.

## Telemetry

v1 has **none**. No analytics SDK, no crash reporter, no "phone home" at startup. The app makes outbound network requests only when you trigger a transcription or click **Test connection**.

If this changes in a future version, it will be announced here and in release notes, and you will be able to opt out.

## Contact

Privacy questions: open an issue on the [main repo](https://github.com/greased-piglet/notabene/issues) or use the feedback channel (once added).
