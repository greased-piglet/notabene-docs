# FAQ

## General

### What does Notabene actually do?

Hold `Fn`, speak, release — your words appear at the cursor, anywhere you can type in macOS. It's a menu-bar push-to-talk dictation tool that uses a cloud speech-to-text provider.

### How is it different from macOS's built-in dictation?

- **You bring your own API key.** Notabene doesn't bundle a model; it calls whichever provider you configure.
- **Provider choice.** macOS dictation is tied to Apple's model. Notabene targets Azure AI Foundry in v1 and any OpenAI-compatible `/audio/transcriptions` endpoint in principle.
- **No modal.** Notabene pastes into whatever you're typing in; macOS dictation pops its own UI.
- **Push-to-talk.** Hold-to-record is instant; no toggle to latch.

### Is it free?

The app itself is free in v1 (BYOK — bring your own key). You pay your cloud provider for transcription usage. Donations may be added later.

### Is it open source?

License is TBD at this stage. Check the [repo](https://github.com/greased-piglet/notabene) for current status.

### Which macOS versions are supported?

macOS 14 (Sonoma) and later.

### Is it on the Mac App Store?

No, and it's unlikely to be. The App Store requires sandboxing, which blocks both system-wide key monitoring (`CGEventTap`) and synthetic paste — the two things Notabene fundamentally needs. Distribution is via direct DMG download.

---

## Installation

### Why do I see "unidentified developer" when I open it?

Notabene is not code-signed in v1 — this is a cost/scope decision, not a security problem. To bypass the Gatekeeper warning, **right-click** `Notabene.app` in `/Applications` and choose **Open**. You only need to do this once.

Code signing with an Apple Developer ID is on the roadmap; when it ships, this warning will go away.

### Does the DMG install anything outside /Applications?

No. Drag-to-`/Applications` is the entire install. Settings are stored in standard macOS locations (`UserDefaults` + Keychain) only after you configure them.

### How do I uninstall?

1. Quit Notabene (menu-bar icon → Quit).
2. Delete `/Applications/Notabene.app`.
3. (Optional) Remove preferences: `defaults delete com.greasedpiglet.Notabene` in Terminal.
4. (Optional) Remove Keychain entry: open **Keychain Access**, search for `com.greasedpiglet.Notabene.azure`, and delete.

---

## Permissions

### Why does Notabene need Input Monitoring?

To detect `Fn` key-down / key-up system-wide. macOS requires Input Monitoring permission for any app that observes key events outside its own windows. Notabene only inspects `Fn` events — no other keystrokes are read, logged, or transmitted.

### Why does Notabene need Accessibility?

To paste the transcript into whatever app has focus. The paste is performed by placing text on the system pasteboard and then posting a synthetic ⌘V, which requires the app to be trusted in Accessibility.

### I granted Accessibility but it's still complaining. Why?

When an app's location, signature, or version changes, macOS sometimes invalidates the grant. Fix: **System Settings → Privacy & Security → Accessibility**, remove Notabene, then re-add it.

### Can I see exactly what keystrokes Notabene reads?

Notabene's event tap filters for the `Fn` key specifically. No other keystrokes are inspected or retained. See [Privacy](privacy.md) for detail.

---

## Dictation behaviour

### Why is macOS's own dictation interfering?

macOS has its own `Fn`-based dictation feature. During Onboarding, Notabene asks you to disable it at **System Settings → Keyboard → Dictation**. Turn it off, or change its shortcut to something other than `Fn`.

### How long can a single dictation be?

Limited by your provider. Azure AI Foundry and OpenAI both currently accept audio up to several minutes per request. If you regularly dictate long-form, check your provider's per-request cap. Notabene does not currently split long utterances automatically.

### Can I dictate in a language other than English?

Yes — set the **Language** field in Settings to the relevant BCP-47 code (`fr`, `de`, `es`, `ja`, etc.). In v1 you change this in Settings; per-session language switching is on the roadmap.

### Does it punctuate?

Generally yes — modern speech-to-text models produce punctuation for you. The exact behaviour depends on your provider and model. Post-transcription cleanup (a second LLM pass for punctuation, paragraph breaks, and capitalisation) is a planned enhancement.

### Does it filter profanity?

It does whatever your provider does by default. Whisper-family models typically do not filter profanity. A toggle may be added in a future release, provider permitting.

---

## Data & privacy

### Where does my audio go?

Only to the endpoint you configure in Settings. Notabene does not relay audio through any Greased Piglet server.

### Does Notabene track usage?

No. v1 has no telemetry, analytics, crash reporting, or "phone home" at all.

### Is my API key safe?

Your API key is stored in the macOS **Keychain** (service `com.greasedpiglet.Notabene.azure`). It is not written to `UserDefaults`, config files, logs, or crash reports. It is sent only to your configured endpoint, over HTTPS.

### Does Notabene retain transcripts?

No. Transcripts are pasted into the focused app and discarded; they are not written to disk by Notabene.

---

## Troubleshooting, quickly

### Test connection fails

See [Troubleshooting → Test connection failures](troubleshooting.md#test-connection-failures).

### HUD says "Error: …"

See [Troubleshooting → Runtime errors](troubleshooting.md#runtime-errors).

### Paste goes to the wrong window

See [Troubleshooting → Paste target is wrong](troubleshooting.md#paste-target-is-wrong).

---

## Feedback

Feedback, bug reports, and feature requests are welcome — use the issues tab on the [main repo](https://github.com/greased-piglet/notabene). A "Send Feedback" menu item in the app is on the roadmap.
