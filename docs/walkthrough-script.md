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
`[sincere]` on the integrity line, `[reassuring]` on the ATS line). 18 lines over
~2 minutes; some silence between lines is expected and fine.

Timecodes below are **targets**, not yet measured — they'll be corrected to the
real recording's boundaries the same way the previous version was (word-count
estimate snapped to the nearest real `ffmpeg -af silencedetect` gap), once this
script is actually recorded.

---

> **[knowing]** Job hunting is the same work, over and over. DevJobInfo takes that part off your hands.
>
> **[confident]** Everything lands on one dashboard — jobs found, your match rate, and where every application stands.
>
> **[confident]** Plus how your match scores are spread, and your weekly AI spend, so you always know where things stand.
>
> **[knowing]** It all rests on your profile. Drop in your CV, and it fills itself in.
>
> **[confident]** Search five job boards at once — or paste a job URL straight in.
>
> **[practical]** Or save it in one click, straight from the tab you're already on — the Chrome extension adds any job posting to your pipeline without ever leaving the page.
>
> **[confident]** Every job is scored against your profile. And a thin posting gets capped, never oversold — so you know exactly which ones are worth your time.
>
> **[impressed]** Behind the scenes, DevJobInfo researches the company — and pulls its actual colors and fonts into your materials, so everything looks like it belongs there.
>
> **[confident]** Then a cover letter, written from your real history, in the language of the posting — drawing on your own saved snippets and past letters.
>
> **[confident]** A short, AI-written headline tops it off — one click to regenerate it on its own.
>
> **[sincere]** Nothing invented — only what's actually in your profile.
>
> **[sincere]** And because it's AI-written, we run it through an AI-detector — then rewrite anything that sounds too robotic, until it sounds like you again.
>
> **[reassuring]** Before you send anything, we simulate how an Applicant Tracking System actually reads it — resume and cover letter both — so nothing gets lost to a bot before a person ever sees it. Regenerate the letter and it automatically fixes what the review flagged.
>
> **[confident]** Add your own motivation for the role, so it never reads like it was written by a bot.
>
> **[confident]** And a resume tailored to this job, reordered around what this employer asked for — including a version built specifically to pass ATS parsing.
>
> **[confident]** Both download as polished PDFs, ready to send the moment you find the job.
>
> **[confident]** Every one of eighteen AI features runs on your own key — pick Gemini, Claude, Mistral, OpenAI, or Grok per feature, or let one click set each to whichever's cheapest — so there's no subscription, and you're never locked to one provider.
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
- **New in this revision (2026-08-25)**: 4 more shipped-but-unnarrated
  features folded into the existing lines with minimal added runtime:
  the dashboard line now mentions weekly AI spend alongside match rate; the
  cover-letter line now mentions it draws on your own saved snippets and past
  letters (the "Cover Letter Building Blocks" profile section); a new short
  line right after covers the AI-written headline and its standalone
  Regenerate button; and the ATS-simulation line now closes with "regenerate
  the letter and it automatically fixes what the review flagged." No new
  scenes/navigation were added — everything narrates content already on
  screen during the existing cover-letter/ATS cues. A 5th, business-model-
  level line was folded in at the same time (not part of the original 4):
  the multi-provider BYOK system (Claude/Mistral/OpenAI/Grok/Gemini, your
  own key, no app billing) had never been narrated at all despite predating
  this revision — added as a short standalone line just before the outro,
  caption-only over the still-open job detail page rather than a new scene.
- **New in this revision (2026-08-30)**: a new standalone line for the Chrome
  extension, right after the "search five job boards" line — it's a
  complementary import path (save the job you're already viewing, in one
  click, no URL copy/paste), so it earns its own beat rather than folding
  into an existing one; it also gets a **new dedicated capture** (the
  extension popup actually importing a job), not just a caption over an
  existing scene — see `e2e/videos/capture-extension-demo.spec.ts` (new).
  Also **corrected a factual error**: the closing multi-provider line
  previously implied nothing about which provider is used by default or how
  selection works; it now correctly states Gemini/Claude/Mistral/OpenAI/Grok
  are chosen **per feature** (not just "whichever you choose" for the whole
  app) and mentions the one-click "cheapest for everything" option — Gemini
  is the actual current default, not Claude, and this is genuinely new
  functionality (per-feature provider assignment + one-click cost
  optimization) that predates this revision but was never narrated.
- To re-record, generate the whole script above as **one continuous take** in
  AI Studio's Generate Speech (voice Iapetus, `gemini-3.1-flash-tts-preview`),
  the bracket tags steer delivery and are not spoken. Once you have the file,
  hand it back and the per-line split / mastering / mux / re-timing of
  `CAPTIONS` in the Remotion composition happens from the real measured
  boundaries — the timecodes above are placeholders, not final cut points.
- The recording is sped up from the raw capture to fit the target runtime, so
  on-screen captions pass quickly. The narration is the primary channel.
