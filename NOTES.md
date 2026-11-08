# Sax Transcriber — how it works (plain-language notes)

This file explains each part of the app without technical jargon. It is updated at every step.

## The big picture

You play a melody. The app listens, works out which notes you played and how long each lasted, lines them up with the beat, rewrites them for alto saxophone, and draws the sheet music. Everything happens inside your phone or computer: the sound is never sent anywhere.

## Files in this folder

| File | What it is |
|---|---|
| `index.html` | The whole app: the page you see plus the instructions that make it work. |
| `basic-pitch.js` | Spotify's note-detection code (see step 2). Kept in its own file so it's clear which part was written by Spotify. |
| `licenses/basic-pitch-LICENSE.txt` | Spotify's licence for that code (Apache 2.0), which must travel with it. |
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

## Step 2 — Finding the notes ✅

**What you see:** a "Detect notes" button, a progress bar, then a row of boxes, one per note (for example **A4** 1.50s · 0.49s = the note A4, starting 1.5 seconds in, lasting about half a second). "Play detected notes" plays them back with a simple electronic tone so you can compare by ear with your recording.

**How it works:**

- **The first time** you tap Detect, the app downloads the note detector: Google's TensorFlow.js (the "engine" that runs trained AI models) and Spotify's Basic Pitch model (the trained "ear"). About 3 MB, once; afterwards Chrome remembers it.
- **Preparing the sound.** Basic Pitch expects a single channel (mono) at 22,050 samples per second, so the recording is converted to that first.
- **Listening.** The model looks at the sound in 2-second slices and, for each tiny moment (about every 12 thousandths of a second), estimates how likely each of the 88 piano notes is to be starting or sounding. All this runs on your device.
- **Turning that into notes.** Spotify's code then turns those likelihoods into a list of notes with a start time and a length. Notes shorter than about 0.13 s are ignored, as are sounds below C2 or above C7 (rumble and squeaks).
- **One note at a time.** Basic Pitch can hear chords, but a sax can't play them. If two notes start together, the app keeps the stronger one (the other is usually a "ghost" overtone). If a note is still ringing when the next one starts, the first is cut short.
- **Concert pitch.** The note names shown here are the *sounding* notes (what a piano would play). They will be converted to saxophone (written) notes in step 4.

**Why Spotify's code is copied into `basic-pitch.js` instead of loaded from the internet:** the ready-made internet version of Basic Pitch automatically pulls in an old TensorFlow.js (from 2022) and an extra MIDI library we don't need. So the app uses Spotify's own published code (unchanged detection logic, only the unneeded MIDI part removed), with the approved TensorFlow.js 4.22.0 and Spotify's model, both loaded from jsDelivr at exact versions. The top of `basic-pitch.js` lists exactly what was changed.

**Internet use:** besides the app's own files, the only downloads are TensorFlow.js and the Basic Pitch model, both from jsDelivr. Your audio is never sent anywhere.

## Libraries used so far

| Library | Version | Licence | Maintainer | Loaded from | Why |
|---|---|---|---|---|---|
| TensorFlow.js | 4.22.0 | Apache 2.0 | Google | jsDelivr | Runs the note-detection model. |
| Basic Pitch model | 1.0.1 | Apache 2.0 | Spotify | jsDelivr | The trained "ear" that recognises notes. |
| Basic Pitch code | 1.0.1 | Apache 2.0 | Spotify | copied into `basic-pitch.js` | Turns the model's output into notes. **Policy exception approved on 2026-11-07:** not updated since January 2023. |

Planned: VexFlow 5.0.0 (MIT, VexFlow open-source project) to draw the sheet music.
