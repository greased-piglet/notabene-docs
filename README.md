# Notabene Docs

User-facing help, FAQ, and setup notes for **Notabene** — the macOS menu-bar dictation app.

> Hold `Fn`. Speak. Release. Your words appear at the cursor.

Notabene is a small native menu-bar utility that turns your voice into text, anywhere you can type. It sends audio to a cloud speech-to-text provider you configure (Azure AI Foundry in v1), and pastes the transcript at your cursor using a synthetic ⌘V.

It's designed to feel like a first-party macOS feature — push-to-talk, invisible until invoked, zero UI overhead.

---

## Contents

- [Getting Started](docs/getting-started.md) — install, permissions, first transcription
- [Permissions & Setup](docs/permissions.md) — Microphone, Accessibility, Input Monitoring, and disabling macOS's built-in Fn dictation
- [Settings](docs/settings.md) — provider endpoint, deployment, API version, language, API key
- [How It Works](docs/how-it-works.md) — what happens between key-down and paste
- [FAQ](docs/faq.md) — common questions and install issues
- [Troubleshooting](docs/troubleshooting.md) — errors, failed transcriptions, permission oddities
- [Improving Transcription Accuracy](docs/improving-accuracy.md) — design note on handling commonly mistranscribed words (vocabulary biasing, replacements, LLM cleanup)
- [Privacy](docs/privacy.md) — what leaves your Mac, what doesn't

---

## Made by

[Greased Piglet Software](https://github.com/greased-piglet) — made in England.

This documentation site is maintained separately from the app source so it can be updated without shipping a new build.
