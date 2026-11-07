# Sax Transcriber — how it works (plain-language notes)

This file explains each part of the app without technical jargon. It is updated at every step.

## The big picture

You play a melody. The app listens, works out which notes you played and how long each lasted, lines them up with the beat, rewrites them for alto saxophone, and draws the sheet music. Everything happens inside your phone or computer: the sound is never sent anywhere.

## Files in this folder

| File | What it is |
|---|---|
| `index.html` | The whole app: the page you see plus the instructions that make it work. |
| `NOTES.md` | This explanation. |
| `LICENSE` | The MIT licence: anyone may use or copy the app, with no guarantees. |
| `.gitignore` | A list of junk files (like Windows thumbnails) that version control should ignore. |
| `.nojekyll` | An empty marker file telling GitHub Pages to serve the files exactly as they are, without trying to "build" a website from them. |

## Version control (git)

The folder is a *git repository*. Think of it as a save-history: each time we finish a step we take a snapshot ("commit"), so we can always go back. The same history is what gets uploaded to GitHub to publish the app.

Saved versions are signed `arlequin-otter` with GitHub's private "noreply" email, so your real email is never published.

## Publishing (GitHub Pages)

- Code lives at: https://github.com/arlequin-otter/sax-transcriber
- The app lives at: https://arlequin-otter.github.io/sax-transcriber/
- Every time a new version is uploaded ("pushed"), GitHub Pages updates the app automatically within a minute or two.
- GitHub Pages was switched on once, in the repository's **Settings → Pages**, with "Deploy from a branch", branch `main`, folder `/ (root)`.
- The address is `https`, which is what lets phones use the microphone.

## Step 1 — Getting your melody into the app ✅

**What you see:** a "Start recording" button, a timer, a red loudness bar, and a "Load an audio file" button. After recording or loading, a picture of the sound and a play bar appear.

**How it works:**

- **Recording.** When you tap Start, the browser asks permission to use the microphone. Phones normally "clean up" the microphone for phone calls (removing echo, background noise and evening out the volume). Those filters mangle musical notes, so the app switches them off.
- **The red bar** shows how loudly the microphone hears you. Aim for it to move well but not stay pinned at the far right (that means it's distorting).
- **Stop** ends the recording and switches the microphone off completely.
- **Loading a file** accepts common audio formats (MP3, WAV, M4A, and the browser's own recordings).
- **Decoding.** Either way, the sound is turned into a long list of numbers describing the sound wave. That list is kept in the app's memory, ready for note detection in step 2.
- **The picture** (waveform) is tall where the sound is loud and flat where it is quiet. It is just a quick visual check that something was captured.

**Why the microphone needs a secure (https) address:** browsers only let a page use the microphone if the page comes from a secure address, so that random websites can't secretly listen. Opening the file directly on your computer counts as safe; on your phone, the app will be served from GitHub Pages, which is secure.

## Libraries used so far

None yet. Step 1 only uses features built into Chrome.

Planned (approved):

| Library | Licence | Maintainer | Why |
|---|---|---|---|
| VexFlow 5.0.0 | MIT | VexFlow open-source project | Draws the sheet music. |
| TensorFlow.js 4.22.0 | Apache 2.0 | Google | The engine Basic Pitch runs on. |
| Basic Pitch 1.0.1 | Apache 2.0 | Spotify | Detects the notes. **Policy exception approved on 2026-11-07:** the browser version has not been updated since January 2023. |
