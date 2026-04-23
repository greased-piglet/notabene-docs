# Permissions & Setup

Notabene needs three macOS permissions and one system-settings tweak to work. This page explains each one, why Notabene needs it, and how to grant or revoke it.

You can reopen the guided **Permissions & Setup** window any time from the menu-bar icon → **Permissions & Setup…**.

---

## 1. Microphone

**Why:** Notabene records audio while you hold `Fn`. Without microphone access it has nothing to transcribe.

**How to grant:**

1. When first prompted, click **Allow** in the system dialog.
2. If you missed the dialog: **System Settings → Privacy & Security → Microphone**, and toggle **Notabene** on.

**To revoke:** the same toggle in System Settings.

---

## 2. Accessibility

**Why:** Notabene pastes the transcript into the app you're using by synthesising `⌘V`. Posting synthetic keyboard events on modern macOS requires the app to be trusted in Accessibility.

**How to grant:**

1. **System Settings → Privacy & Security → Accessibility**.
2. Click the **+** button, navigate to `/Applications/Notabene.app`, and add it.
3. Make sure the toggle next to **Notabene** is on.

**If Notabene was updated or moved:** you may need to remove and re-add it in this list, as macOS sometimes invalidates the Accessibility grant when the app's signature/location changes.

---

## 3. Input Monitoring

**Why:** Notabene listens for `Fn` key-down and key-up events system-wide, using `CGEventTap`. macOS requires **Input Monitoring** permission to observe key events outside Notabene's own windows.

**How to grant:**

1. **System Settings → Privacy & Security → Input Monitoring**.
2. Add **Notabene** via the **+** button, and ensure the toggle is on.

**Note:** Input Monitoring only lets Notabene *observe* the `Fn` key — it does not log or transmit any other keystrokes. See [Privacy](privacy.md).

---

## 4. Disable macOS's built-in Fn dictation

**Why:** macOS has its own dictation feature bound to `Fn` (or double-press `Fn`, depending on your system version). If it stays enabled, the two will fight over the same key.

**How to disable:**

1. **System Settings → Keyboard**.
2. Find **Dictation** (it may be under **Text Input** depending on macOS version).
3. Toggle **Dictation** **off**, OR change the **Shortcut** away from `Fn`.

Turning dictation off entirely is the cleanest option. If you want to keep Apple's dictation available via some other shortcut, changing the shortcut is fine — just make sure it isn't `Fn`.

---

## Traffic-light indicators

The **Permissions & Setup** window shows a 🔴 / 🟡 / 🟢 indicator next to each permission:

- 🟢 **Granted** — Notabene can use this capability.
- 🔴 **Not granted** — this step still needs action.
- 🟡 **Unknown / needs refresh** — click **Refresh** to re-check after granting in System Settings.

macOS sometimes takes a moment to propagate permission changes; if the indicator doesn't update immediately, wait a few seconds and click **Refresh**.

---

## Sandboxing note

Notabene is intentionally **not sandboxed**. The features above — especially `CGEventTap` for global key monitoring and the Accessibility-based paste — are not permitted for sandboxed apps. This is also why Notabene is not on the Mac App Store.

If a future macOS release changes the entitlement model for these capabilities, you'll see updates here.
