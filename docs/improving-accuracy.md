# Improving Transcription Accuracy

A design note capturing how Notabene should handle a class of problem every cloud dictation app faces: the same words getting mistranscribed over and over — proper nouns, colleague names, product names, jargon, acronyms. "Notabene" becomes "notebook". "SwiftUI" becomes "swift you I". "Greased Piglet" becomes nonsense.

This page summarises a design conversation — **it is not yet implemented**. It's captured here so the thinking isn't lost when the question comes back later.

---

## The question

> What's a good way to solve the problem of common words which are often mistranscribed by the speech-to-text API? Is it sensible to have a dictionary against which the transcription is checked? Does it make sense to have a second AI look at the transcription and check it for grammatical correctness? What would be your recommendation in terms of solving this problem?

---

## Short answer

**Neither a local dictionary alone nor a second-AI grammar pass alone is the right first move.**

The single highest-leverage fix is the **STT `prompt` parameter** — a short text sent alongside the audio that biases Whisper-family recognition toward supplied vocabulary. It prevents the error instead of patching it, costs nothing extra, and adds zero latency. Notabene already sends this field to Azure AI Foundry; it just isn't surfaced clearly to users.

Layer a small **local replacements dictionary** on top for the cases the STT consistently mis-hears the same way.

**Defer** the second-LLM cleanup pass until there's evidence the first two tiers don't cover it — it costs real money per utterance under BYOK, and doubles end-to-end latency beyond the app's 2-second target.

---

## Recommended tiered approach

### Tier 1 — Vocabulary biasing via the STT prompt

**What:** Expose the existing STT `prompt` parameter as a user-facing **Vocabulary** field in Settings. The user lists names, jargon, and product terms they dictate regularly; the field is sent to the transcriber with every request.

**Cost:** Zero — it's already part of the API contract.

**Latency added:** Zero — same HTTP call, same payload size (roughly).

**Strengths:**
- Prevents errors rather than patching them.
- Works across all Whisper-family and OpenAI-compatible providers without adapter changes.
- User-tunable: adjusting vocabulary immediately changes recognition behaviour.

**Limits:**
- Whisper treats the prompt as ~224 tokens of "prior context". Over-stuffing it causes the model to hallucinate listed terms into unrelated speech. There's a soft cap around ~150 tokens before quality starts to degrade.
- Doesn't help first-time errors for words the user hasn't thought to add.
- Doesn't fix homophones or context-dependent errors ("read" vs "red" vs "Reed").

**Design note:** The current Settings surface has a single "Transcription Prompt" text box which combines a language-lock instruction ("Output ONLY {language} text…") with space for vocabulary. Refactor this into a fixed internal scaffold plus a clean user-facing "Vocabulary" field — users shouldn't have to preserve the instruction boilerplate to add a term, and the app can warn about the token budget.

---

### Tier 2 — Local replacements dictionary

**What:** A user-editable list of `wrong => right` rules applied to the transcript after the STT response and before paste. Whole-word, case-insensitive, regex-escaped, first-match-wins.

**Cost:** Zero — local string work.

**Latency added:** Negligible (single-digit milliseconds for realistic rule counts).

**Strengths:**
- Deterministic. A rule either fires or it doesn't.
- Offline.
- Good for the "always wrong, always wrong the same way" cases that bias vocabulary can't fix — e.g. the model consistently hears "Greased" as "greezed". One rule, permanent fix.
- User retains full control; nothing is guessed or inferred.

**Limits:**
- Blunt. Rules can't be context-sensitive. `"bene" => "Notabene"` would mangle the word "benefit" without word-boundary anchoring.
- Doesn't scale. Users can't enumerate every possible mistranscription — this is a patch, not a solution.
- Risk of silent over-correction if a rule fires in unexpected contexts.

**Safeguards to build in:**
- Word-boundary matching by default (`\b…\b`).
- Case-insensitive match, case-preserving replacement — so `"swiftui"` becomes `"SwiftUI"` regardless of how the STT cased the input.
- Rules applied in declaration order, each as a single pass — no cascading.
- Keep the *raw* transcript available (e.g. in a menu entry) so users can recover the original if a rule misfires.

---

### Tier 3 — Second-LLM cleanup pass (defer)

**What:** After the STT response, a second API call to an LLM (e.g. Claude) asks it to rewrite the transcript for grammar, punctuation, and capitalisation.

**Why it's tempting:** It's the only tier that fixes context-dependent errors — homophones, bad paragraph breaks, missing commas, incorrectly segmented sentences.

**Why to defer:**
- **Latency.** A realistic Claude round-trip is 600–1500 ms. Added to a ~1-second STT call, the app blows through its 2-second end-to-end target for short utterances.
- **Cost.** BYOK means the user pays per utterance, invisibly. Doubling API calls doubles spend.
- **Over-correction.** LLMs don't just fix errors — they reword. In a dictation tool, rephrasing the user's intended wording is worse than leaving a minor error in place.
- **Unclear ROI until Tiers 1 + 2 are in.** If most residual errors turn out to be lexical (names, jargon), Tiers 1 + 2 already solve them and Tier 3 adds nothing.

**When to revisit:** Once Tiers 1 + 2 are shipped and real usage shows a residual class of errors that are *grammatical* (punctuation, homophones, run-on sentences) rather than *lexical* (proper nouns, jargon). At that point the latency-for-quality trade becomes a user-facing opt-in toggle ("Polish with AI — adds ~1 second"), default off.

---

## Summary table

| Tier | Fix | Cost | Latency | Best for |
|---|---|---|---|---|
| 1 | STT vocabulary biasing (prompt field) | Zero | Zero | Names, jargon, product terms — lexical errors |
| 2 | Local replacements dictionary | Zero | ~0 ms | Always-wrong-always-the-same cases Tier 1 misses |
| 3 | Second-LLM grammar/punctuation pass | Per-call API spend | +600–1500 ms | Context-dependent errors — only if Tiers 1+2 aren't enough |

---

## Related material

- `docs/settings.md` — describes the current Settings fields, including the existing "Transcription Prompt" that Tier 1 would refactor.
- `docs/how-it-works.md` — shows the current pipeline from key-release to paste; Tier 2 would slot in just before the paste step.
- In the main app repo: `docs/enhancements.md` items **4.4** ("Make the backend prompt configurable") and **5.5** ("Document what role the STT prompt parameter plays") map onto Tier 1; **5.4** ("Post-transcription AI formatting pass") maps onto Tier 3.
