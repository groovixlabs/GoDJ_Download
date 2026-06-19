# Beatmatching in GoDJ

How tempo, sync, and the tempo-return tools work together, and the intended
workflow for a full A → B transition.

## The pieces

**Beatgrid.** Every track loaded onto a deck (or dropped into the playlist) is
analyzed in the background: BPM plus the position of the first beat, as a
fixed-tempo grid. Everything beat-related — sync, quantize, beat loops, beat
jumps, the beat ticks in the stacked waveform view — derives from this grid.
If the grid looks misaligned against the audio, fix it with the grid
correction buttons beside the stacked view (next section); corrections are
saved and survive reloads.

**Grid correction buttons** (beside the stacked view, one tinted 2×2 block
per deck):

- `<BEAT` / `BEAT>` slide all the beat markers earlier/later relative to
  the waveform, one waveform bin per step (~2 ms; hold to repeat) — watch the
  zoomed view and put a tick right on the kick. **Shift-click** jumps the grid a whole beat
  instead: no tick visibly moves, but the *downbeat* re-anchors, fixing
  bar lines that land mid-phrase (e.g. after leading silence).
- `-` / `+` tighten/widen the grid *spacing* by trimming the analyzed BPM
  (0.05 BPM per step, repeats while held). Use this when ticks line up at
  the playhead but drift apart further away — the analyzed tempo is
  slightly off.

The buttons are enabled only while their deck is **paused** — grid edits on
a playing deck would audibly jerk loops and sync. All grid edits persist
with the track.

Bar lines in the sliding view are numbered (counting from the downbeat
anchor) and coloured by phrase position — every 4th bar amber, 8th coral,
16th purple, 32nd blue — so you can see phrase boundaries coming and check
that both decks' phrases line up, not just their beats.

**Tempo fader.** Each deck has a vertical TEMPO fader, ±16%, with its current
value shown below it. The BPM read-out always shows the *effective* BPM
(track BPM adjusted by the fader). Double-click the fader or use RESET to
return to 0%. Note: GoDJ has no keylock, so tempo changes also change pitch —
which is exactly why the tools below move tempo *slowly*.

**Nudge.** `< NUDGE` / `NUDGE >` are momentary pitch bends (hold to run ~4%
slow/fast, release to return). Use them for small phase corrections by ear
when mixing manually; they don't change the set tempo, and the position
shift gained while held sticks. While a sync latch is engaged the
*follower's* nudge buttons are greyed out — the lock holds phase itself and
would steer any nudge straight back.

**Step.** `< STEP` / `STEP >` move the playhead itself by 10 ms per press,
or 2 ms with shift held (auto-repeats while held) — the paused-jog-wheel
scrub. Use them to creep onto an exact transient before setting a hot cue:
coarse steps to get close, shift-steps to land on the kick. Unlike NUDGE
they *seek*, so stepping while playing makes a tiny skip.

**Step under sync lock.** On the latched *follower* (with both decks
running), STEP changes meaning: instead of seeking — which the lock would
fight — it nudges the lock's *target alignment*, and the lock gently slips
the audio into the new position. This is the live fix for "sync is engaged
but the kicks still flam": tap STEP until they merge. The correction is a
session-only offset inside the lock — **beat markers never move and
nothing is saved**; it evaporates when the latch is released. If the flam
turns out to be a genuine grid error you want fixed permanently, pause and
use the `<BEAT` / `BEAT>` buttons — grid edits belong to them alone.
Prefer single taps when correcting; holding injects offset faster than the
lock's gentle trim can follow, and it will eventually jump to catch up.

**Sync latch.** Each deck has a **SYNC** button in its pad area, just below the
**Q** button. Pressing a deck's SYNC engages it as the follower:

- deck A's **SYNC** — A follows B (B is the master)
- deck B's **SYNC** — B follows A (A is the master)

They are mutually exclusive, and each lights in its own deck's colour. (The
handover *direction* is chosen separately by the `A>B`/`A<B` toggle next to
FADE — sync and direction are decoupled.) Engaging one does a one-shot sync first — the follower's tempo fader moves to
match the master's effective BPM, and playback jumps (at most half a beat) to
the matching beat phase. While the latch stays lit, the follower keeps
tracking the master: tempo fader changes on the master are mirrored, and beat
phase is held with tiny inaudible rate trims (±1.5% max). That phase hold runs
on the **audio thread**, per block, off the exact sample position — the master
publishes its beat phase each block and the follower trims its resampling ratio
to hold it, so the lock is sample-accurate rather than chasing a stale reading
off a GUI timer. (A 20 Hz timer still does the slower work behind it: matching
tempo and the big-jump snap.) If the follower ever drifts badly (you seeked, or
restarted playback), it snaps back into phase instead of chasing. Each button is
tinted with its *master* deck's colour.

The lock is **pure grid phase**, no continuous audio
cross-correlation runs while it's latched (that only made the follower hunt and
the beat markers drift). It locks the actual *kicks* because the analyzer anchors
each grid line onto the kick at sample resolution (see "Kick-anchored grids"
below), so once the grids are phase-matched the kicks sit on top of each other.

**Kick-anchored grids.** Grid sync only locks the kicks if the grid lines sit on
the kicks. After detecting a coarse BPM and phase, the analyzer refines both
against the actual kicks: it low-passes the audio to 150 Hz, builds a sample-rate
kick-onset signal, snaps the first beat onto the kick, then **least-squares-fits
a continuous BPM and anchor over every strong kick onset in the track**  — so the 
grid stays on the kicks from start to finish, instead of being quantized to the 
coarse comb's 0.02 BPM and slowly walking off over a few minutes. Because both 
decks anchor to the same feature, phase-matched grids stack the kicks. 
(Re-analyze a track to apply this — cached grids from an
older analysis keep their old fit until you do.)

**ALIGN (manual phase align).** A one-shot kick cross-correlation, for mixes
you've beatmatched by hand instead of latching. Press it and, on deck
A's next beat, it correlates both decks' low end and nudges deck B's playhead
onto A's kick — a single quantize-style position move, not a continuous trim, and
never a grid edit. Press again before it fires to cancel; it lights while armed.
It does nothing if there's no confident kick on that beat (a breakdown). Because
a sync latch already holds phase continuously, the button is **disabled whenever
a sync lock is latched** — it's only for hand-beatmatched mixes. Both decks must
be analyzed and playing.

Sync needs both decks loaded *and* analyzed; if a latch pops back out, the
analysis isn't ready yet.

**Trigger quantize.** Starting the latched follower (PLAY) or jumping it to
a hot cue pre-aligns its position to the master's beat phase *before* the
audio runs — a nearest-phase jump of at most half a beat — so the first
audible beat is already locked rather than corrected a moment later.

**Quantize (Q).** Per-deck toggle; snaps hot-cue placement and beat-loop
starts to the grid. It doesn't affect sync itself.

## The problem these tools solve

When B follows A, B's tempo is bent away from its native value — say B is a
128 BPM track running at +3.2% to match A. After you mix over and fade A out,
B is playing alone at the wrong tempo. Snapping the fader back to 0% would be
an audible pitch lurch. Tempo drift of about 1% per second, however, is
essentially inaudible — so both tools below move tempo at exactly that rate.

### Tempo handover (the planned way — do it *during* the blend)

The transition is driven by two arming toggles — **TEMPO** and **FADE** — and
a single **TRANSITION** button that runs whichever are lit. To do a tempo
handover, arm **TEMPO** (the toggle needs a sync latch to be runnable — it
greys TRANSITION out until one is engaged), then press **TRANSITION** while
both tracks are still in the mix:

- The *master's* tempo fader glides toward the point where the follower sits
  at exactly 0%. The TRANSITION button is tinted with the master deck's colour
  while the glide runs — that's whose tempo is moving.
- Sync lock drags the follower along the whole way, beat-locked, so the mix
  stays tight while the floor drifts to the incoming track's native tempo.
- When the follower reaches 0% the button un-lights. Fade the master out,
  release the latch — done. Nothing needs resetting.

Press **TRANSITION** again to cancel mid-glide. The handover also cancels
itself if you release the latch or flip the sync direction, and if the target
is beyond the master's ±16% fader range it glides as far as it can and stops.

### The volume blend and band swap (VOL always · LOW · MID · HI)

There is **no crossfader** — blending between the decks is done on the
per-deck **channel (VOL) faders**. A handover or fade always swaps the
**volume** across (outgoing down, incoming up), using an *equal-power*
cos/sin curve so the combined loudness stays flat through the middle instead
of dipping. The three toggles to the right of **ALIGN** — **LOW · MID · HI** —
add optional EQ swaps on top (a hands-free bass transition, for example);
there is no VOL toggle because volume always swaps.

- Check **LOW** (and/or MID/HI) *before* starting the handover/fade.
- **When the HANDOVER or FADE starts (say A>B):** the incoming deck's (B)
  volume — and any checked EQ bands — are cut, so it slots in under the
  outgoing one without clashing. (Pressing SYNC no longer cuts anything; the
  cut happens only when the transition begins, so you can cue the incoming
  deck after syncing.)
- **As it runs:** the outgoing deck's (A) volume/bands glide *down* to killed
  while the incoming deck's (B) glide *up* to unity (full fader / EQ centre),
  over the number of **bars in the FADE dropdown** (at the outgoing tempo). By
  the time it lands, the decks have swapped.

The glide runs on its own timed duration, *not* the tempo distance — two
similar-BPM tracks barely move the tempo, so tying the swap to that would snap it
instantly; the bars setting gives it a smooth, musical length. The
knobs/faders physically move as it runs, so you can see and grab them. If you
cancel/release *before* it completes, the swap is undone (the bands return to
where they were); once it lands it's the new mix state.

### Fading out the old deck

Arming the **FADE** toggle and pressing **TRANSITION** blends the decks on the
channel faders over the number of bars chosen in the dropdown beside the
toggles (2/4/8/16 bars, measured at the outgoing deck's tempo); the TRANSITION
button is lit in the outgoing deck's colour while armed or running. The
**direction** is set by the `A>B`/`A<B` toggle (A>B fades deck A out to B; A<B
fades B out to A) — a fade works with or without sync, so a plain
bar-quantized blend between two unsynced tracks is one press of TRANSITION
with only FADE armed. The fade doesn't begin the instant it's triggered — it
waits for the outgoing deck's **next bar line**, so the blend always starts on
a phrase boundary (set the downbeat with the grid buttons if the bar lines are
off). Triggering with no beatgrid or a stopped deck starts immediately. When
the blend lands — and the tempo handover isn't still running — the sync latch
releases by itself: the transition is finished. Press **TRANSITION** again to
cancel.

Run it whenever the music says so: during the tempo glide, after it, or
without any handover at all.

**The TEMPO / FADE toggles + TRANSITION.** Instead of separate action buttons,
two arming toggles decide what one **TRANSITION** press does:

- **TEMPO** armed only — tempo glide, no fade (needs a sync latch).
- **FADE** armed only — the channel-fader blend, no tempo glide.
- **TEMPO + FADE** both armed — the tempo glide and the blend run together,
  the shortest hands-free transition.

Whichever finishes the transition last (fade or tempo glide) releases the
latch, so a long tempo glide is never cut short by the fade arriving first.

### Glide-on-RESET (the cleanup way — after the fade)

If you didn't do a handover: fade A out, release the latch (B keeps its bent
tempo — releasing never jumps), then press **RESET** on deck B. Instead of
snapping, the tempo glides back to 0% at 1%/sec while the BPM read-out counts
down; the RESET button is lit in the deck's accent colour during the glide.

- Press RESET again mid-glide → snap to 0% immediately.
- Shift-click RESET → always an instant snap.
- Grab the tempo fader → glide cancels, you're in manual control.
- RESET on a stopped deck snaps instantly (nothing to hear).

Sync always outranks a glide: if a latch engages the deck as follower, any
running glide is cancelled.

## The full workflow, end to end

1. Deck A is live. Load the next track on deck B, cue it up (quantized hot
   cues help land on a downbeat).
2. Press the right **SYNC** (B follows A) — B's tempo and phase lock to A.
   Check the stacked waveform: the beat ticks of both lanes should line up.
3. Start B, bring its channel up, blend with the EQs (e.g. swap basses with
   the LOW knobs).
4. Set the direction to `A>B`. While both are audible, arm **TEMPO** (and
   **FADE** too if you want the volume to ease over in the same press), then
   press **TRANSITION** and let the mix drift to B's native tempo. With FADE
   armed you're done — the volume eases over to B on the channel faders (with
   the tempo glide or after it, your choice) and the latch releases on its own.
5. Otherwise: arm **FADE** only and press **TRANSITION** when the moment feels
   right (or ride the deck VOL faders by hand), and the latch releases when the
   fade lands.
6. If you skipped step 4: press **RESET** on B and let it glide home.

## Numbers reference

| Thing | Value |
| --- | --- |
| Tempo fader range | ±16% |
| Glide / handover rate | 1% per second |
| Fade length | 2/4/8/16 bars at the outgoing deck's tempo (~12 s fallback without BPM) |
| Nudge bend | ~4% while held |
| Sync-lock rate trim | ±1.5% max |
| Phase snap threshold | 0.1 beat (beyond this the follower jumps, not chases) |
| Grid kick-anchor | sample-resolution kick onset + continuous BPM least-squares fit over all beats (150 Hz low-pass) |
| ALIGN search | onset cross-correlation, ±0.45 beat, ~1.5 ms resolution (manual one-shot only) |
| Grid shift step | 10 ms per press |
| Playhead step | 10 ms per press (2 ms with shift) |
