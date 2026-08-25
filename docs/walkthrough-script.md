# Walkthrough — voiceover script

For `docs/walkthrough.mp4` (90s, 1280×720). A narrated version exists as
`docs/walkthrough-voiced.mp4`, generated with Google's `gemini-3.1-flash-tts-preview`
(voice **Iapetus**), with per-line delivery steered by inline bracket cues. This is
what it speaks (the bracket tags below are direction, not spoken).

## How the audio is produced

1. The whole tagged script goes to `gemini-3.1-flash-tts-preview` (voice Iapetus)
   in a **single** generation in Google AI Studio
   ([aistudio.google.com/generate-speech](https://aistudio.google.com/generate-speech)),
   so it is one continuous performance. Inline cues like `[knowing]`, `[confident]`,
   `[sincere]` steer the delivery per line and are not spoken. A generation per line
   sounded like several narrators. (The browser path needs no key; scripting the API
   needs `GOOGLE_API_KEY`.)
2. The single take is split into per-line files by `scripts/split-take` logic:
   estimate each boundary from cumulative word count, then snap to the nearest real
   silence gap. Pure silence-splitting fails — the model pauses at commas as well as
   between lines — but snapping a word-count estimate to a real pause never cuts
   mid-word. No Whisper needed.
3. `scripts/build-voiceover.sh` masters each line (trim edge silence, high-pass,
   gentle compression, a presence lift, loudness normalised to −16 LUFS) and places
   it at its SRT cue, then muxes onto `walkthrough.mp4`.
4. A background music bed (`docs/audio/the-grey-room.mp3`, YouTube Audio Library,
   attribution-free) is mixed under the narration: loudness-normalised to ~11 LU
   below the voice, faded in/out, and **sidechain-ducked** by the voice so it dips
   under speech and swells in the silent gaps. Recipe: `loudnorm` bed → `afade` →
   `sidechaincompress` (music keyed by voice) → `amix` → `alimiter`.

**Delivery:** bright and energetic, like showing a friend something you are
pleased with — the bracket cues carry the arc (frustration → relief → confidence,
`[sincere]` on the integrity line, `[reassuring]` on the ATS line). 15 lines over
~90 seconds; some silence between lines is expected and fine.

Timecodes below are **targets**, not yet measured — they'll be corrected to the
real recording's boundaries the same way the previous version was (word-count
estimate snapped to the nearest real `ffmpeg -af silencedetect` gap), once this
script is actually recorded.

---

> **[knowing]** Job hunting is the same work, over and over. DevJobInfo takes that part off your hands.
>
> **[confident]** Everything lands on one dashboard — jobs found, your match rate, and where every application stands.
>
> **[confident]** Plus how your match scores are spread, so you know you're aiming at the right roles.
>
> **[knowing]** It all rests on your profile. Drop in your CV, and it fills itself in.
>
> **[confident]** Search five job boards at once — or paste a job URL straight in.
>
> **[confident]** Every job is scored against your profile. And a thin posting gets capped, never oversold — so you know exactly which ones are worth your time.
>
> **[impressed]** Behind the scenes, DevJobInfo researches the company — and pulls its actual colors and fonts into your materials, so everything looks like it belongs there.
>
> **[confident]** Then a cover letter, written from your real history, in the language of the posting.
>
> **[sincere]** Nothing invented — only what's actually in your profile.
>
> **[sincere]** And because it's AI-written, we run it through an AI-detector — then rewrite anything that sounds too robotic, until it sounds like you again.
>
> **[reassuring]** Before you send anything, we simulate how an Applicant Tracking System actually reads it — resume and cover letter both — so nothing gets lost to a bot before a person ever sees it.
>
> **[confident]** Add your own motivation for the role, so it never reads like it was written by a bot.
>
> **[confident]** And a resume tailored to this job, reordered around what this employer asked for — including a version built specifically to pass ATS parsing.
>
> **[confident]** Both download as polished PDFs, ready to send the moment you find the job.
>
> **[warm]** DevJobInfo — devjob dot info.

---

## Notes

- The on-screen captions carry their own wording; the narration deliberately
  differs so the voice adds context rather than reading the screen aloud.
- **"devjob dot info"** is spelled out for the speech engine. A human reader
  should just say "devjob dot info" naturally.
- **New in this revision** (3 lines added to cover previously-unnarrated
  features): the brand-theming line (company colors/fonts), the AI-detection
  rewrite line, and the ATS-simulation line — matching what the short promo
  video (`devjob-promo.mp4`) already covers, so both videos now tell a
  consistent, complete story.
- To re-record, generate the whole script above as **one continuous take** in
  AI Studio's Generate Speech (voice Iapetus, `gemini-3.1-flash-tts-preview`),
  the bracket tags steer delivery and are not spoken. Once you have the file,
  hand it back and the per-line split / mastering / mux / re-timing of
  `CAPTIONS` in the Remotion composition happens from the real measured
  boundaries — the timecodes above are placeholders, not final cut points.
- The recording is sped up from the raw capture to fit the target runtime, so
  on-screen captions pass quickly. The narration is the primary channel.
