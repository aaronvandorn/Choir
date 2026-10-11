# Phrase styles: how genres shape a vocal line

Choir's **Phrase style** setting (Rhythm engine panel) replaces the free note-length roll with a
four-bar phrase shaped like a genre. This page is the analysis behind those masks: what makes a
sung line sound like country rather than pop, measured in the things a melody generator can
control — note values, syllables per bar, where lines start and stop, how notes sit against the
beat, and where the pitch goes.

Most of this page is about **4/4 time**; [Other meters](#other-meters) covers 3/4, 6/8 and
12/8. Beats are Choir's own beats (with Half time on, each of those beats spans two beats of the
shared clock).

## The common skeleton

Almost every popular-song genre builds a vocal phrase the same way underneath:

- **Lines are two bars long, phrases are four.** A lyric line usually takes about one bar to sing
  and one bar to breathe, land, or hand over to an instrument. Two lines make a four-bar phrase,
  which matches the four-bar chord loops and drum patterns everything else is built on.
- **Question, then answer.** The first line ends *open*, on the 3rd or 5th of the chord, so it
  sounds unfinished. The second line *comes home* to the key's root (the cadence). This is the
  single strongest cue that a phrase is a phrase and not a string of notes.
- **Line endings are long.** The last stressed syllable of a line is held — a dotted half, a
  half, or a quarter tied over — while the words before it move quickly. Short-short-short-LONG
  is the basic shape of a sung line in nearly every genre.
- **Pickups.** Many lines start *before* the bar line with one to three quick notes (an
  anacrusis, as in "and I / *said*..."), so the first stressed word lands on beat 1.
- **One syllable per note is the default.** Genres differ most in when they break that rule:
  a **melisma** sings one syllable across several notes (the R&B and gospel run).

What changes from genre to genre is the density, the placement against the beat, the timing
feel, and the pitch habits.

## Genre by genre

| Style | Syllables per bar (sung bar) | Main note values | Where lines start | Line endings | Melisma | Timing feel | Pitch habits |
|---|---|---|---|---|---|---|---|
| **Pop** | 6–10 | 8ths, some 16ths, syncopated | Pickup or the "and" of 1 | Held note, often anticipated (lands on the "and" of 4) | Rare | Straight, on the beat | Arch per line, repeated-note hooks, small range |
| **Country** | 7–10 (story-dense) | 8ths, dotted 8th + 16th | Pickup of 1–3 notes | A long held note to close each line | Rare | Straight to lightly swung | Falling lines, lots of repeated notes, scoops into phrases, stepwise |
| **R&B / Soul** | 4–8 syllables, but more notes | 16ths, syncopated, triplets | After the downbeat | A run (melisma) into a held note | Frequent, at line ends | Swung 16ths, sung behind the beat | Wide range, runs that fall stepwise, vibrato on holds |
| **Rock** | 4–8 | Quarters and 8ths | On beat 1 | Short and strong, then space | Rare | Straight, on or ahead of the beat | Narrow range, repetition, accented downbeats |
| **Hip-hop flow** | 10–16+ | 16ths, dense and syncopated | Anywhere; often off the beat | The rhyme lands near beat 4 | None | Swung 16ths, slightly behind | Near-monotone (two or three pitches), accents on the syncopations |
| **Folk / Ballad** | 6–8 | Quarters and 8ths | Pickup or beat 1 | Half-note line endings | Rare | Straight, legato | Common-meter lines (8 then 6 syllables), stepwise arch |
| **Gospel** | 4–8 syllables, more notes | 8ths, triplet runs | Call on 1; response after | Long held notes with vibrato | Frequent | Swung 8ths | Call-and-response, big leaps up, runs down to the cadence |
| **Blues** | 5–8 | Shuffle 8ths (triplet feel) | Pickup or beat 1 | Held note, then space for the instrument's answer | Small | Full triplet swing, laid back | Falling lines, scoops and bent notes, AAB lyric form |
| **Reggae** | 5–8 | 8ths, syncopated | After the downbeat (beat 2 or the "and") | Short, with a lot of space | Rare | Nearly straight, well behind the beat | Gentle wave shape, repeated notes |
| **Chorale / Hymn** | 4–6 | Quarters | Beat 1 | Half or whole note every two bars (fermata feel) | None | Straight, legato | All voices move together, stepwise, clear cadences |

A few notes on the numbers. Syllables per bar are typical figures for the *sung* bar of a line
(the breathing bar is mostly empty). Hip-hop is the outlier because the voice is a rhythm
instrument: the 16th-note grid is filled and pitch matters little. R&B and gospel have *fewer*
syllables than pop but *more notes*, because the extra notes are melismas on a single vowel.

## How the masks implement this

Each style is a small set of two-bar **line rhythms**: "a" lines that end open and "b" lines that
cadence. A phrase is one "a" line plus one "b" line, so it always fills exactly 16 beats and
starts on a bar line. The lines are written in a compact notation in `index.html`
(`PHRASE_STYLES`):

```
w h q e s   whole, half, quarter, 8th, 16th     .  dotted     t  triplet ("et")
r           rest ("rq" = quarter rest)
~           melisma: same syllable as the note before (legato, no new vowel)
^           open line ending: lands on the 3rd or 5th
!           cadence: lands on the key root
=           repeats the pitch of the note before
>           accent
|           bar line (for reading)
```

For example, a country "a" line — a quarter note, a run of story-telling 8ths with a repeated
note, a held ending, and a two-note pickup into the next line:

```
q e e= e e q | h.^ re e
```

On top of the rhythm, each style sets:

- **Swing** (8ths or 16ths, from straight to full triplet) and a **laid-back** delay in
  milliseconds, so R&B, reggae and blues sit behind the beat while rock sits on it.
- **Contour** per line: arch (pop, folk, R&B, gospel, chorale), falling (country, blues), wave
  (rock, reggae) or flat (hip-hop). Picking a style sets Contour to "Genre's own shape, per
  line"; choose any other contour to override it.
- **Repeat** — how often a note simply repeats the one before (high in hip-hop and rock, low in
  folk and R&B).
- **Leaps** — a multiplier on the Leapiness control (gospel and R&B leap more, chorale and
  hip-hop less), and for rock and hip-hop a narrow **range window** around the first note.
- **Syllable length** — how much of each short note is sung (clipped in hip-hop and reggae, full
  in folk and chorale). Line endings are always held.
- **Where inflections go**, chosen from the ones ticked in Vocal expression: accents on the
  marked beats (pop, rock, hip-hop, reggae), scoops into the first note of a line (country, R&B,
  gospel, blues), vibrato on held notes.

Phrases always set out from a chord tone. With **Vowel changes** on note onsets, each syllable
gets a new vowel and melisma notes keep the vowel they started with, so a run sounds like one
sung word.

**Rhythm evolution** works per line when a style is on: on each pass, each line has that chance
of switching to another rhythm of the same kind, so the phrase changes a line at a time and
keeps its question-and-answer shape. Pitch evolution still varies the notes, and line endings
are re-landed afterwards so cadences stay intact.

## Other meters

The **Meter** menu next to Style picks 4/4, 3/4, 6/8 or 12/8. The same question-and-answer
skeleton holds in every meter; what changes is the bar length, how the beat divides, and which
genres actually live there.

| Meter | Beat (what Tempo counts) | Beat divides into | Line / phrase | Genres with lines | Where it comes from |
|---|---|---|---|---|---|
| **4/4** | quarter note | two 8ths (swing optional) | 2 bars / 4 bars | all ten | the default for nearly all popular music |
| **3/4** | quarter note | two 8ths | 4 bars / 8 bars | Pop, Country, Folk, Gospel, Chorale | waltzes, country waltzes, hymns, folk songs |
| **6/8** | dotted quarter, two per bar | three 8ths | 4 bars / 8 bars | Pop, Country, Rock, Folk, Chorale | jigs and folk ballads, lullabies, power ballads |
| **12/8** | dotted quarter, four per bar | three 8ths | 2 bars / 4 bars | Pop, Country, R&B, Rock, Gospel, Blues | slow blues, doo-wop and soul ballads, gospel, shuffles |

**Why lines are longer in 3/4 and 6/8.** Their bars are short (three quarters, or two dotted
quarters), so a lyric line usually spans four bars rather than two. A 3/4 hymn line such as
"Amazing Grace" covers four bars; so does a 6/8 folk ballad line. That makes their phrases
eight bars long.

**Why the beat is a dotted quarter in 6/8 and 12/8.** That is how these meters are felt and
counted: a 12/8 slow blues is "one-and-a, two-and-a, three-and-a, four-and-a", four beats each
split in three. Counting it this way has a practical payoff: a 12/8 bar is exactly one 4/4 bar
of a linked drum machine, so Choir's 12/8 gospel or blues lines sit on top of a 4/4 shuffle beat
with every bar line shared. Swing is switched off in 6/8 and 12/8 because the triplet feel is
already in the meter.

**3/4 against 4/4 drums.** A linked drum machine still plays 4/4, so in 3/4 the bar lines drift
apart and meet again every 12 beats (three of Choir's bars to four drum bars). That polymeter can
sound good, but for a straight waltz, set the drums to a three-beat pattern if they have one.

**Genre habits in the new meters.** Country waltzes lean on a quarter-note pickup on beat 3 and
a dotted-half held to close each line. 3/4 hymns and chorales move in quarters with dotted-half
cadences. 6/8 folk and pop use "long-short" quarter + 8th pairs (the lilt of a jig or a
lullaby). 12/8 R&B and gospel put their melismas in triplet 8ths and hold the ends of lines for a
dotted half (two beats). Hip-hop and reggae have no lines outside 4/4 and are greyed out in the
other meters.

The snapshot's bar grid and the **Export MIDI** time signature follow the meter of a styled
phrase. In 6/8 and 12/8 the MIDI tempo is written in quarter notes (1.5 × the app's tempo), so
the file opens at the right speed in a DAW.

## Adding a style

Add an entry to `PHRASE_STYLES` with a few "a" and "b" lines in 4/4 and the parameters above,
and an `<option>` in the Style menu. Lines for other meters go in `STYLE_METERS` under the
style's id. Each line must fill exactly its bars (8 beats in 4/4, 12 in 3/4, 8 in 6/8 and 12/8,
counting dotted quarters as beats in the compound meters); the page warns in the browser console
if one does not.
