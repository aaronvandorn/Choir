# Choir — vowel formant synthesizer

A browser-based, generative choral synthesizer built around vowel formant filtering. A repeating melodic motif that slowly mutates drives pitch and rhythm, a vowel sequence steps along with the notes like syllables in a sung line, and a harmony stack follows the melody inside your chosen key and mode. Everything runs live in the Web Audio API — no build step, no dependencies, just `index.html`.

**Live demo:** https://aaronvandorn.github.io/Choir/

## How it works

A bank of detuned sawtooth oscillators (the "choir voices") each pass through three parallel bandpass filters tuned to the current vowel's formant frequencies (F1/F2/F3).

- **Pitch and rhythm** come from a short *motif*: a phrase of 2-16 notes, each with its own pitch, length, and optional rest. The first pass states the motif cleanly, then it mutates a little on each repeat, under separate Pitch and Rhythm *evolution* controls.
- **Vowels** are a second sequence of values along an a → e → i → o → u continuum. By default it steps on note onsets, so each note is a syllable, and long notes lean toward the open vowels (a, o).
- **Harmony** voices sit at intervals above the melody, snapped to the nearest note of the key and mode, so the choir always stays in key.

## Controls

**Voice display**
Live readout of the current pitch, vowel, and motif position (step and pass number), plus a vowel-space plot tracking the formant position as it glides.

**Choir voices**
- *Voice count* — how many oscillators sing together
- *Detune spread* — chorus-style micro-detuning across the voices, in cents
- *Harmonic* — how the voices are stacked above the melody. At 0% they double it at the unison and octave. As the knob turns up they move through fifths, thirds, and sixths, then sevenths and seconds, and finally wide compound intervals (ninths, tenths, elevenths, thirteenths). Every voice is snapped into the current key and mode, and voices are folded down an octave above about 1.1 kHz to keep the top singable
- *Formant resonance* — Q of the formant filters
- *Master volume*

**Pitch — quantized sample & hold**
- *Key* / *Mode* — the scale the motif draws from (major, minor, dorian, pentatonic, etc.)
- *Range* — how many octaves the pitch pool spans
- *Glide time* — portamento between notes
- *Number of notes* — how many distinct pitches are in the pool, evenly spread across the key, mode, and range (default 8)
- *Melodic contour* — the overall shape the phrase follows: Arch (default), Rising, Falling, Wave, or Free. Motion is mostly stepwise, with occasional leaps that usually step back the other way
- *Leapiness* — from stepwise to leaping; how often the melody jumps instead of moving by step
- *Motif length* — how many notes long the repeating phrase is (2-16). Changing it live lengthens or trims the phrase without losing what is already there
- *New motif* — throws the phrase away and writes a fresh one
- *Pitch evolution* — how much the motif's pitches change each time it repeats. 0% repeats the phrase exactly, the middle drifts gradually (and keeps the chosen contour), and 100% re-rolls pitches almost freely

**Rhythm engine**
Each slot in the motif gets a note length from whichever values are checked (whole down to 16th, plus dotted and triplet 8ths).
- *Tempo* — BPM the note values are measured against
- *Gate length* — how much of each note's duration actually sounds before the next one
- *Attack* / *Release* — amplitude envelope per note
- *Rhythm evolution* — how often a slot's note length (and whether it is a rest) is re-rolled on later passes. Independent of pitch evolution, so you can hold the melody still and vary the rhythm, or the reverse
- *Rest* — when enabled, a rest is weighted as one more peer option alongside the checked note lengths

**Vocal expression**
Per-note inflections, chosen the same way as rhythm note lengths.
- *Inflections* — tick any of Vibrato (delayed, fades in, deeper on long notes), Scoop (slide up into the note), Breath (a puff of air at the onset), Accent (louder, sharper attack, brighter filter). *Plain* is a peer option, like rest
- *Per note* — **One per note** gives each motif slot one inflection from those ticked; **All together** applies every ticked one to every note
- *Amount* — overall strength
- *Contour* — how intensity is shaped: Even, Follows the melody (higher notes get more), or Builds to phrase end
- *Expression evolution* — static repeats the same inflections each pass, middle drifts, 100% re-rolls them

**Filter envelope**
A filter over the whole choir output, swept by its own ADSR on every note: type (low-pass, high-pass, band-pass), cutoff, resonance, envelope amount (±4 octaves), and attack / decay / sustain / release.

**Vowel — quantized sample & hold**
- *Vowel changes* — **On note onsets** (default) steps the vowel sequence with the notes, so each note is a syllable, and rests do not count. **Free clock** steps it on its own rate instead
- *Clock rate* — how often a new vowel is sampled (free clock mode only)
- *Change every* — how many notes share a vowel (note-synced mode only)
- *Glide time* — how long the formants take to slide into a new vowel. Shorter values give crisper syllables, longer ones smear into legato
- *Long-note open vowels* — how strongly longer notes are pulled toward the open vowels a and o, the way a held sung syllable opens up. Short notes are left alone (note-synced mode only)
- *Steps* — length of the vowel sequence (2-16). It loops independently of the motif length, so the two drift against each other
- *Evolution* — how much the sequence's values change each pass: static, evolving, or random

**LFO + attenuverter**
A sine LFO with a bipolar depth control, routable to either pitch (vibrato) or the formant frequencies (a warbling morph).

**Reverb**
On/off toggle plus five procedurally generated impulse responses — Room, Hall, Plate, Spring, Cathedral — with a wet/dry mix control.

**Patterns**
Save the whole instrument as a named pattern: every control, the exact motif (pitches, lengths, rests, inflections), and the vowel sequence. Click a saved pattern to recall it; it begins from a clean statement of the phrase. Patterns are kept in the browser's local storage, so they persist on the same browser and device.

**Export**
While Choir runs, the last 60 seconds of what actually played is kept in memory (nothing is uploaded), randomness and all. It stays available after you press Stop.
- *Capture last minute* — snapshots the buffer into a timeline showing the waveform and the melody notes
- *Snap to* — Phrases (each pass through the motif), Bars, or Beats. Tap a cell to select it, tap again to extend, then tap to move the nearer edge
- *Preview* — plays the selection back
- *Export WAV* — the selected audio exactly as heard, volume, filter and reverb included (16-bit stereo)
- *Export MIDI* — one track per choir voice (voice 1 is the melody) plus a tempo track, using the Choir Aahs sound. Note lengths follow the gate length; vibrato, scoop and breath are not written to MIDI. Tempo is taken from the first note of the selection

**Light / dark mode**
Toggle in the top bar; light mode is the default.

## Running it

Just open `index.html` in a browser, or visit the GitHub Pages link above. Press **Start** to begin (browsers require a user gesture before audio can play).

## Deploying updates

```bash
git add index.html README.md
git commit -m "describe your change"
git push
```

GitHub Pages rebuilds automatically after a push to `main`.

## Jam Link

Press **Link** in this app and in Logic Rhythm, Boolean Melody Machine and Choir (open each in its own tab or window of the same browser) and they share tempo, start/stop, key and mode, and saved scenes, all locked to one beat grid. **Create room** or **Join** with a short code to do the same with people in other places, and hear each other's apps. See [JAM-LINK.md](JAM-LINK.md) for how it works.

Notes for this app: notes are now placed on a look-ahead timer against the beat grid (no timer drift), and the tempo slider runs 40–240 BPM.


## Controls

Every ranged control is a potentiometer: a knob plus a number box. Drag the knob up/right to raise it (hold Shift for fine steps), drag the number up and down the same way, or click the number and type a value (Enter commits, Esc cancels, Up/Down arrows step, Shift = ×10). Double-click a knob to reset it; arrow keys, Home and End work when a knob has focus; the mouse wheel nudges the focused control. Panels are grouped by function set with a colour per set; the round **i** button in each header shows its description. The Jam Link lives in a tab at the bottom of the page.
