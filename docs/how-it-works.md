# How It Works

A short tour of what happens between `Fn` key-down and the transcript appearing at your cursor. Useful if you're curious, or if you're debugging something weird.

## The pipeline

```
Fn key-down
    │
    ▼
CGEventTap fires key-down event
    │
    ▼
AVAudioEngine starts capturing mic → in-memory buffer (WAV)
    │
    ▼
Floating HUD shows "Listening…" near the cursor
    │                                   (you are speaking)
Fn key-up
    │
    ▼
AVAudioEngine stops, buffer is finalised
    │
    ▼
HUD shows "Transcribing…"
    │
    ▼
multipart/form-data POST → configured endpoint
    │   (audio file + language + model/deployment)
    ▼
Transcript returned as JSON
    │
    ▼
Transcript placed on NSPasteboard
    │
    ▼
Synthetic ⌘V posted via CGEvent → frontmost app pastes
    │
    ▼
HUD disappears (on success) or shows "Error: …" (on failure)
```

## Why `Fn`?

`Fn` is the right key for push-to-talk because:

- It has no other default behaviour on most Mac keyboards (the built-in dictation shortcut is disabled during Notabene onboarding).
- It's reachable without leaving the home row.
- Holding it doesn't trigger modifier combos for the focused app.

## Why synthetic ⌘V instead of direct text insertion?

Direct text insertion (via Accessibility APIs or AppleScript) is brittle and app-specific. Posting a synthetic `⌘V` works in any app that supports paste — which is effectively every Mac app.

The tradeoff: Notabene temporarily writes your transcript to the system pasteboard. Its prior contents are restored (where feasible) after paste.

## Why cloud, not on-device?

v1 uses cloud transcription (Azure AI Foundry) for two reasons:

1. **Quality.** Current cloud models (Whisper-family and successors) beat on-device options for all but the simplest utterances, especially with accents, proper nouns, and technical vocabulary.
2. **Scope.** On-device models require bundling a multi-gigabyte model and shipping code for GPU inference. That's a separate product's worth of engineering.

On-device transcription is not ruled out for later versions.

## What happens on error?

- **Network failure / timeout:** HUD shows `Error: network`. Nothing is pasted. Your utterance is lost (v1 does not retry automatically).
- **4xx from provider** (bad key, bad deployment, bad API version): HUD shows the error. Open Settings and re-check your configuration. Use **Test connection** to confirm.
- **5xx from provider:** HUD shows `Error: server`. Try again; if persistent, check your provider's status page.

## What the transcribe request sends

A typical request looks like (headers simplified):

```
POST <endpoint>/openai/deployments/<deployment>/audio/transcriptions?api-version=<version>
Content-Type: multipart/form-data
api-key: <your-key>

--boundary
Content-Disposition: form-data; name="file"; filename="recording.wav"
Content-Type: audio/wav

<binary WAV bytes>
--boundary
Content-Disposition: form-data; name="language"

en
--boundary--
```

No telemetry. No additional tracking fields. Just your audio plus the language tag.

## What it does *not* do

- ❌ Does not stream audio live (upload is one-shot after release).
- ❌ Does not log your transcripts anywhere on your Mac.
- ❌ Does not send analytics, crash reports, or usage data to Greased Piglet. (v1 has no telemetry at all.)
- ❌ Does not observe or transmit any keystrokes other than `Fn`. See [Privacy](privacy.md).
