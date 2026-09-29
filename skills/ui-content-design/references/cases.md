# Cases

Decided copy, one row each. Append; never rewrite. A case belongs here only when the reasoning
was not obvious in advance — applying the section contract in `SKILL.md` is not a case.

Format per case: **the surface**, before, after, and one sentence of why.

---

## Settings › Privacy — three telemetry opt-ins

Source of the pattern this skill was written against.

**Before** — the shared facts copied into every row:

```
[ ] Send anonymous usage counts to Company. Which screens get used, never the
    contents of your work. Off unless you tick it; change it in Settings.

[ ] Send crash reports to Company. Technical details about what the app was doing
    when it stopped working, never the contents of your work. Off unless you tick
    it; change it in Settings.

[ ] Send performance timings to Company. How long screens take to open and save,
    never the contents of your work. Off unless you tick it; change it in Settings.
```

**After:**

```
Privacy

Anything you turn on here is sent to Acme to help improve the app.
None of it includes the contents of your work.

[ ] Usage data
    Which screens and features you open, and how often.

[ ] Crash reports
    Technical details about what the app was doing when it stopped working.

[ ] Performance data
    How long screens take to open and save.
```

**Why:** the recipient, the exclusion and the default were each stated three times; said once
above the group they read as a policy, repeated per row they read as defensive. "Change it in
Settings" was a dead instruction — the reader is in Settings — and "off unless you tick it" is
already said by an unchecked box. ~95 words to ~55 with nothing removed.

---

## Crash reports — disclosing the one surprising inclusion

**Before:** `Technical details about what the app was doing when it stopped working.`

**After:** `What the app was doing when it quit unexpectedly, including the name of the
document that was open.`

**Why:** the difference line exists to carry what distinguishes this control, and the genuinely
surprising thing about crash reports is the filename. Omitting it is the move that makes the
whole section untrustworthy when someone later finds out; disclosing it costs one clause.
Contrast with the row above, which is correct when no such surprise exists.

---

## "Anonymous" → the mechanism

**Before:** `Anonymous usage data, not linked to your account.`

**After:** `Reports are labeled with a random ID for this installation, not with your name or
email address.`

**Why:** "anonymous" is a legal term of art, and most telemetry pipelines carry a persistent
install or device ID that defeats it. The mechanism is checkable by whoever owns the pipeline;
the adjective is only checkable after someone disputes it. Ship this line only once that owner
has confirmed the ID is per-install and not per-user.

---

## Save failure on a preference toggle

**Before:** `Couldn't save your changes.`

**After:** `We couldn't save that change. Your setting is still off.` — with the control
reverted to the last persisted value.

**Why:** the anxious question on a failed privacy toggle is not *what broke* but *which way did
it land*. Naming the surviving state answers it; a UI that keeps displaying a preference the
server rejected is worse than the error.
