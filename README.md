# GoDJ — User Manual

GoDJ is a two‑deck DJ application: load tracks into the library, drop them onto
Deck A or Deck B, beat‑match them, and mix from one to the other. It analyses
every track for tempo and beat positions so it can sync the decks, quantise
loops and cues, and automate transitions.

This manual starts with a hands‑on tutorial, then explains the everyday
workflow, and finishes with a reference to every control. For the deep
mechanics of sync and the tempo handover, see **[beatmatch.md](beatmatch.md)**.

There are **no menus** — every control is on the main screen, and the **bottom
bar shows help text** for whatever your mouse is hovering over.

---

## 1. Quick tutorial — your first mix

1. **Add music.** Drag a few audio files (or a folder) onto the **playlist** at
   the bottom of the window. Each track is analysed in the background; its BPM
   appears in the list when it's ready.
2. **Load the decks.** In a track's row, click **A** to load it onto the left
   deck and another track's **B** to load the right deck. (Decks load *only*
   from these buttons.)
3. **Start Deck A.** Press the ▶ (play) button on Deck A. Bring its **VOL**
   fader up if needed.
4. **Prepare Deck B.** On Deck B, set a cue point and press its **SYNC** button
   (just below the **Q** button). Deck B's tempo and beats now lock to Deck A.
   Start Deck B and check the **sliding waveform** in the middle — the beat
   ticks of both lanes should line up.
5. **Set the blend direction.** The **A>B / A<B** button (next to **FADE**)
   shows which way the mix will go. Pressing Deck B's SYNC sets it to **A>B**
   (fade Deck A out, Deck B in) automatically.
6. **Transition.** Press **FADE**. GoDJ blends the two decks on their channel
   faders over the chosen number of bars (equal‑power, so the level stays
   steady), starting on the next bar line. When it lands, Deck A is out and the
   sync latch releases.

That's a full mix. Everything below is detail and refinement.

---

## 2. The screen at a glance

```
 ┌───────────────┬───────────────────────────┬───────────────┐
 │   DECK A      │   sliding waveforms (both  │   DECK B      │
 │   (teal)      │   decks) + zoom + grid     │   (orange)    │
 │               │   edit buttons             │               │
 ├───────────────┴───────────────────────────┴───────────────┤
 │   MIXER (centre): GAIN · HI · MID · LOW · COLOR + VOL      │
 │   fader per deck (the two VOL faders meet in the middle)  │
 ├───────────────────────────────────────────────────────────┤
 │   TRANSITION ROW: FADE bars · TEMPO · FADE · TRANSITION ·  │
 │   A>B · ALIGN · LOW MID HI                                 │
 ├───────────────────────────────────────────────────────────┤
 │   PLAYLIST (library)                                       │
 ├───────────────────────────────────────────────────────────┤
 │   help/info bar            [ MIDI ]   [ AUDIO ]           │
 └───────────────────────────────────────────────────────────┘
```

- **Deck A** is teal and on the left; **Deck B** is orange and on the right.
- The **mixer** sits between them.
- The **sliding (stacked) waveform** at the top shows both decks scrolling past
  a fixed centre playhead, so you can see beat alignment at a glance.

---

## 3. Workflow

### Building the library
Drop files or folders onto the playlist. Tracks are analysed on a background
thread (BPM, beat grid, **musical key**, a colour waveform, and rough phrase
sections). The key is shown both as a name (e.g. `Am`) and a Camelot code
(e.g. `8A`) — tracks whose Camelot numbers are equal or adjacent (and same
letter) mix harmonically. Use the
playlist's **NEW / LOAD / SAVE** buttons to start a fresh list or swap it with a
saved one. Tracks shown in **amber** were analysed by an older version — re‑run
analysis on them when convenient.

### Loading and cueing a deck
Click a row's **A** or **B** to load that deck. Find your start point with the
waveform (click to seek) and the **STEP** buttons, then set the main cue with
**CUE** while paused. Quantised hot cues (with **Q** on) snap to the beat.

### Beat‑matching (sync)
Press a deck's **SYNC** (below its **Q**) to make it follow the other:

- Deck **B**'s SYNC → B follows A (A is master).
- Deck **A**'s SYNC → A follows B (B is master).

Sync matches the follower's tempo and holds its beat phase continuously. If the
kicks still flam slightly, tap **STEP** on the follower to nudge the phase (a
temporary, unsaved correction). The two SYNC buttons are mutually exclusive.

### Transitioning
Two arming toggles decide what the transition does, then one button runs it:

- **TEMPO** — glides the *master* deck's tempo until the follower sits at its
  own natural tempo, beat‑locked the whole way, so the floor drifts onto the
  incoming track's tempo. Needs a sync latch.
- **FADE** — blends the decks on the channel faders over the **FADE bars**
  setting, in the **A>B / A<B** direction. Equal‑power, so there's no loudness
  dip. It starts on the outgoing deck's next bar line.
- **TRANSITION** — runs whichever toggles are armed (arm both to glide the
  tempo and blend together in one press). Press again to cancel.

Tick **LOW / MID / HI** before a transition to also swap those EQ bands across
(e.g. a hands‑free bass swap). Volume always swaps; the EQ toggles are extra.
See **[beatmatch.md](beatmatch.md)** for the full detail.

> There is **no crossfader** — blending is done on the per‑deck channel (VOL)
> faders. Mix manually by riding the two VOL faders, or let FADE do it.

### Looping
Use the **LOOP row** (IN / OUT / EXIT / ½ / X2) for manual loops, or **BEAT
LOOP** pad mode for instant beat‑length loops. With **Q** on, loop points snap
to the beat. Loop markers and the looped region are drawn on the sliding
waveform.

### Effects
Each deck has a **Beat FX** slot (its FX row) for a post‑fader, beat‑synced
effect, and a **COLOR** filter/effect knob per channel in the mixer (pre‑fader,
DJM‑style). Both lock to the track's tempo.

---

## 4. Function reference

### Deck (one per side)

| Control | What it does |
| --- | --- |
| **Waveform overview** | Whole‑track view; click to seek. Coloured by frequency energy; cue markers shown. |
| **▶ / ⏸ (PLAY)** | Play / pause. **Shift‑click** starts *both* decks together. |
| **CUE** | Paused away from the cue: sets the cue here. Paused at the cue: plays while held (audition), snaps back on release. Playing: stops and returns to the cue. |
| **« » (NUDGE)** | Hold to pitch‑bend slower / faster (temporary; releases back to tempo). Disabled on a sync follower. |
| **\|◄ ►\| (STEP)** | Nudge the playhead a hair (hold to repeat; **shift** = finer). On a sync follower it slips the phase instead. |
| **VOL fader** | This deck's channel level (the deck‑to‑deck blend control). Lives in the central **mixer** strip; the two decks' VOL faders meet in the middle. |
| **TEMPO fader** | ±16% tempo (and pitch — there's no keylock), on the deck. Double‑click or **RESET** to return to 0%. |
| **RESET** | Tempo back to 0% (glides home on a playing deck, snaps when stopped); also resets this deck's mixer **VOLUME** to full, **EQ** to 0 dB, and **COLOR** to centre. |
| **PFL** | Headphone‑cue placeholder (not routed yet). In the mixer strip, under the VOL fader. |
| **Read‑outs** | Track title, musical **key** (name + Camelot), effective BPM, tempo %, and elapsed / total time. |
| **Q** | Quantise toggle — snaps cues and loop points to the beat. |
| **SYNC** | (Below Q.) Engage this deck as the sync follower; lit in the deck colour. Also sets the FADE direction. |

### Performance pads (8 per deck) + mode row

Pick a mode with the row above the pads; **shift‑click a pad clears** it.

| Mode | Pads do |
| --- | --- |
| **HOT CUE** | Set / jump to 8 hot cues. |
| **BEAT LOOP** | Toggle beat‑length loops (⅛ … 16 beats). |
| **SAMPLER** | Trigger the 8 shared sampler slots. |
| **BEAT JUMP** | Jump backward / forward by whole beats (keeps phase). |

### Loop row

| Button | Action |
| --- | --- |
| **LOOP IN** | Set the loop in‑point at the playhead (beat‑snapped when Q is on); the button lights while armed. |
| **LOOP OUT** | Set the out‑point and start looping. |
| **EXIT** | Leave the loop and keep playing; lit while a loop runs. |
| **½ / X2** | Halve / double the running loop length. |

### Beat FX row (per deck, post‑fader)

- **Effect selector:** PHASER, ECHO, REVERB, FLANGER, CHORUS, DISTORT, WAH,
  TREMOLO, PING‑PONG.
- **Beats:** effect time in beats (⅛, ¼, ½, 1, 2, 4).
- **Depth slider** and **ON** toggle.

### Mixer (centre, per channel)

Five knobs top to bottom — **GAIN**, **HI**, **MID**, **LOW**, **COLOR** — each
double‑clickable to reset. Below COLOR is its **effect type** selector:
FILTER, REVERB, DUAL DELAY, NOISE, GATER. The COLOR knob is centre‑off: FILTER
sweeps low‑pass one way / high‑pass the other; the others ramp up from centre in
either direction. Beside each channel's knobs is its **VOL** fader (the
deck‑to‑deck blend control) with a **PFL** button under it; the two channels'
VOL faders sit together in the centre. A thin **level meter** runs alongside
each VOL fader, showing that channel's **pre‑fader** level (after GAIN/EQ,
before the fader) — green/amber/red, so you can set gain before bringing the
fader up.

### Sliding (stacked) waveform + grid editing

- Both decks scroll past a centre playhead with **beat ticks**, **bar numbers**,
  a **phrase band**, and **loop markers**.
- **Zoom + / −** change the visible time span.
- **Double‑click a phrase** to relabel / split / merge a section.
- **Grid edit buttons** (per deck, paused only): `||<` `>||` move the beat
  markers earlier / later (10 ms/step; **shift** moves the downbeat by a whole
  beat); `||-` `||+` tighten / widen the grid spacing (±0.05 BPM). Use these to
  line a tick up with a kick.

### Transition row (centre)

| Control | What it does |
| --- | --- |
| **FADE bars** | Length of the FADE / band swap: 2 / 4 / 8 / 16 bars. |
| **TEMPO** | Arm the tempo glide (master's tempo glides so the follower ends at its native 0%, beat‑locked). Needs a sync latch. |
| **FADE** | Arm the channel‑fader blend over the chosen bars, in the A>B/A<B direction. |
| **TRANSITION** | Run the transition using whichever of TEMPO / FADE are armed (both = glide and blend together). Click again to cancel. |
| **A>B / A<B** | The handover direction (which deck fades out). Follows the last SYNC press; click to flip. |
| **ALIGN** | One‑shot kick cross‑correlation for a hand‑beatmatched mix (not while sync is latched). |
| **LOW / MID / HI** | Also swap these EQ bands during the transition (volume always swaps). |

### Playlist (library)

Drag in files/folders; each row has a title, length, BPM, **Key** (name +
Camelot, sortable), and **A / B** load buttons. **NEW / LOAD / SAVE** manage
playlist files. Right‑click a row for remove / re‑analyse. Amber rows need
re‑analysis (e.g. tracks analysed before key detection existed).

---

## 5. MIDI control

Open **MIDI** (bottom bar) to use a controller:

1. Tick your device under **MIDI INPUTS** (use **RESCAN** if it's not listed).
2. For each function, click **LEARN** then move/press the control on your
   hardware to bind it. **X** clears a binding.
3. **SAVE / LOAD** store and recall a mapping profile per controller (LOAD
   replaces the bindings and keeps working across restarts).

Mappable per deck: play, cue, sync, tempo, channel volume, EQ hi/mid/low,
colour, jog wheel, the 8 pads, loop in/out/exit/halve/double, beat‑FX on, and
the four pad‑mode selectors. The jog wheel bends pitch while playing and seeks
while paused. (There is no scratch engine, and the crossfader entry is unused.)

---

## 6. What's saved

GoDJ saves automatically to your user app‑data folder and restores on launch:
the playlist with hot cues and main cues, sampler slot assignments, track
analyses, the audio device setup, and your MIDI mapping. Audio device and buffer
size are chosen under **AUDIO** (smaller buffers = lower latency).

---

## 7. Quick reference (numbers)

| Thing | Value |
| --- | --- |
| Tempo range | ±16% (no keylock) |
| Sync phase‑hold trim | ±1.5% max |
| Nudge bend | ~4% while held |
| STEP | 10 ms (2 ms with shift) |
| Grid shift | 10 ms per step |
| Grid spacing | ±0.05 BPM per step |
| FADE / transition length | 2 / 4 / 8 / 16 bars |
| Beat loops | ⅛ to 16 beats |
| Performance pads / hot cues / sampler slots | 8 each |

For the sync, tempo‑handover and band‑swap internals, see
**[beatmatch.md](beatmatch.md)**.
