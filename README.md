# Music-Looper

A dance rehearsal app that takes the phone handling out of practice. In Play Store
internal testing on Android, and in device testing on iOS.

> **What this repo is.** The source is private — Music-Looper is a product I intend to
> release commercially, so the implementation stays closed. This page is the public
> write-up: what the app does and which parts were actually hard to build. Happy to
> walk an interviewer through the code live, or grant repo access on request.

<!-- DEMO CLIP GOES HERE. Drop a 15-30s screen recording of a drill running:
     open a song, mark an A/B section, let the tempo step up on its own.
     Upload it by dragging the file into a GitHub issue, then paste the URL as:
     https://github.com/user-attachments/assets/<id>
     A clip of it working proves more than any diagram. -->

## What it does that other apps do not

Any music app can slow a song down or set an A/B loop. That part is table stakes, and
it is not why this exists.

- **Automates the tempo increments and saves them as presets.** You are not reaching
  for a speed control mid-drill. The app steps you up rep by rep on its own, and it
  tells you how long the whole drill will take before you press play.
- **Saves the loop points too**, so you are not re-marking the same eight-count at the
  start of every session.
- **Sections video the same way.** Cut a reference clip into A/B parts and loop one
  movement until it is clean enough to put into the choreography.
- **Moves the sound to the phone speaker while you film**, and hands it back to your
  earbuds afterwards, without ever disconnecting them. Filming a rep used to mean
  unpairing Bluetooth and pairing it again.
- **Spotify handoff**, so when a drill finishes the music keeps playing.

It came out of my own rehearsal setup. I teach salsa four classes a week, and the
friction there was never the dancing.

## The parts that were actually hard

**Real-time time-stretching.** Playback slows to 50% with no pitch shift, so a
half-speed song still sounds like the song rather than a tape being dragged. This
drives both the core rehearsal loop and the timed workout mode.

**Six custom native modules.** React Native either had no equivalent or the dominant
binding had been retired, so I wrote them in Kotlin: on-device video transcoding for
reference clips; a frame cache so scrubbing lands on the exact frame rather than the
nearest keyframe; per-stream audio output routing, done by setting Oboe's device id
rather than the device-wide Android call that would hijack routing for every other
app; a scheduling primitive that survives the OS suspending JavaScript timers;
text-to-speech rendered to a file, because Android clamps media-stream gain to unity
and a spoken cue can never otherwise out-shout a song at full volume; and voice
capture.

**A waveform that holds the whole song.** Multi-row, pinch-zoomable, 2D-pannable,
drawn with Skia. The song is rasterized once into static pictures and pan and zoom are
GPU transforms on a group, so moving around the track re-renders nothing in React and
the playhead does not stutter while audio is running. Gestures are handled off the
JavaScript thread with Reanimated worklets.

**Spotify handoff.** OAuth 2.0 and the App Remote SDK, so the music continues when a
drill ends. The five-second gap between the drill ending and the song starting turned
out to be a cold SDK connection plus a duplicate play call in my own code, which is
the half I could actually fix.

## Stack

TypeScript, React Native, Expo, Kotlin, Skia, Reanimated, react-native-audio-api,
Spotify App Remote SDK. Built with Claude Code.

## Status

In Play Store internal testing on Android; iOS runs on my own devices. Next up: a test
suite over the loop and speed-ramp logic, then a wider beta.

---

Built by Luis Escobar — [github.com/Escobar-Luis](https://github.com/Escobar-Luis) ·
[linkedin.com/in/luisalfredoescobar](https://www.linkedin.com/in/luisalfredoescobar)
