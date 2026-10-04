---
layout: post
title: "Make a UK Garage Track in Ableton Live 12: A Step-by-Step Tutorial"
date: 2026-10-04 13:00:00 +0200
description: "A beginner-friendly walkthrough of a UK garage track in Ableton Live 12: the 2-step drum grid, swing settings, wub bass, minor 9th and sus2 chords, vocal chops, 8-bar arrangement, mixing, plus a list of free plugins and royalty-free sample packs that actually fit the genre."
tags: [music, ableton, uk-garage, tutorial, music-production]
categories: [general]
---

# Make a UK Garage Track in Ableton Live 12: A Step-by-Step Tutorial

*2026-10-04 · 18 min read · [music] [ableton] [uk-garage] [tutorial] [music-production]*

UK garage sounds like it should be easy. Four or five elements, a loop, done. Then you open a drum rack, put a kick on every beat, and it turns into house with a bad hat pattern. The genre lives almost entirely in two things: where you *don't* put the kick, and how far you push the swing. Everything else is decoration on top of those two decisions.

This is the walkthrough I'd want handed to me. I'm using Ableton Live 12, but nothing here is Ableton-specific except the Groove Pool and a couple of device names. If you're in FL Studio or Logic, the grids and the settings translate directly.

The plan is a full track at 134 BPM in D minor, built in eight stages. Drum programming and groove come first, because they're the genre. Chords, bass, vocal chops, and arrangement follow. At the end there's a list of free plugins and royalty-free sample packs that actually fit UKG, and a one-page recipe you can work from without reading all of this again.

## What you need before you start

Ableton Live 12 (Intro is fine, Suite makes the synths easier). One UKG-oriented sample pack for kicks, snares, hats, and a vocal. You can build the whole thing from free material, and I've listed the packs I'd actually use in the resources section.

You do not need to buy Serum. Operator, Wavetable, and the stock Simpler/Sampler cover every sound in this tutorial.

## Stage 0: session setup

Three settings decide whether the track sounds like the genre before you've written a note.

**Tempo: 130 to 140 BPM.** This is the accepted UKG range. 134 sits comfortably in the middle and gives your vocal chops room to breathe. Speed garage leans to the lower end, modern UKG and the faster end. Live's default 120 is wrong for this.

**Key: pick a minor key.** D minor, F minor, and G minor are the common ones. Minor is where the whole emotional register lives, and the reason is in the chords (Stage 3).

**Two-track length: 8 bars.** Set your loop brace to 8 bars, not 4. UKG phrases resolve over 8. If you write in 4-bar blocks you'll end up with a loop that never goes anywhere.

Set all three now. Changing tempo after you've built drums means redoing the swing.

## Stage 1: the drums, which is really just the 2-step grid

UK garage is not four-on-the-floor. The 2-step deliberately leaves a hole where house would put its third kick, and the snare and hats carry the groove through that hole.

Everything below is one bar of 16th notes. Beats 1, 2, 3, 4 land on steps 1, 5, 9 and 13.

```
Step:   1  e  &  a   2  e  &  a   3  e  &  a   4  e  &  a
Index:  1  2  3  4   5  6  7  8   9 10 11 12  13 14 15 16
----------------------------------------------------------
Kick:   X  .  .  .   .  .  .  .   .  .  X  .   .  .  .  .
Snare:  .  .  .  .   X  .  .  .   .  .  .  .   X  .  .  .
OpenHat:.  .  X  .   .  .  .  .   .  .  .  .   .  .  .  .
Closed: .  .  .  .   .  .  X  .   X  .  .  .   .  .  X  .
```

That's the skeleton. Kick on 1 and on the "and" of 3. Snare on 2 and 4. Open hat on the "and" of 1 for lift. Closed hats at 7, 9 and 15 to fill without crowding.

The kick on step 11 instead of step 9 is the whole trick. Beat 3 is empty, so the second half of the bar tips forward, and the snare on 4 catches it. That forward lean is the 2-step.

### Build it

1. Load a drum rack. Drop in a punchy kick (a 909 sample works), a main snare (909), and a second snare (606) as your ghost snare.
2. Program the grid above.
3. Shorten both snares with the sample's amp envelope. Long snares kill the feel. You want ticks, not thumps.
4. Pitch both snares up two or three semitones. This is the "pitched up" character everyone means when they say a UKG record sounds sped up. It does, deliberately.
5. Low-pass the ghost snare so it doesn't compete with the main one in the high end.
6. A little reverb on the main snare only, on steps 5 and 13.

### Then the part that makes or breaks it: swing

Load a groove from Live's Groove Pool. Two that work, both from real UKG breakdowns:

- **SP1200 16 Swing-71**, amount set to about 60 percent.
- **Swing 16ths 73**, timing around 60 percent.

Commit the groove to the clip. Then set the global groove amount so it's applied on top, not doubled. Push it until it sounds slightly wrong, then pull back a touch. UKG over-uses swing compared to every other electronic genre. Loads of it is correct here.

### Now the variations

A single bar of the grid above is a loop, not a track. Two moves add the rest:

**Ghost kicks.** Very quiet kicks at low velocity (20 to 40 out of 127) on some of the "and" positions. They keep the low end moving without adding audible hits.

**The third-bar lift.** Copy your one-bar pattern to a two-bar pattern, then in the second bar add two extra snares near the end of the phrase. This is the small fill that stops eight bars of the same loop feeling static.

**Hats with a triplet feel.** The real UKG character comes from slipping occasional triplet figures into the 16th grid. Nudge a hat or two off the grid by hand at the end of a phrase.

**Velocity, not just position.** Consistent hats sound programmed. Alternate loud on the beat and quiet on the off hit. That's the bounce you're hearing.

## Stage 2: the wub bass

The other signature sound is a bass with a slow attack. It arrives late, which makes it sit *behind* the shuffled drums and pushes the groove forward instead of stomping on it.

Two ways to get it:

**Filter-envelope wub, in Operator or Wavetable.** A square or saw wave, low-pass filter, and a filter envelope with a real attack time (200 to 400 ms) and short decay. High resonance. Every note now swells in rather than snapping. This is the classic "wub".

**Pitch-envelope wub.** Same idea on pitch instead. A sine sub with an envelope that slides up into the note over 100 to 300 ms. Subtler, deeper, works better under a busy drum pattern.

### Getting the low end clean

You want two bass layers, not one:

- **Sub layer:** a clean sine an octave down, playing the root. Mono. No effects at all.
- **Wub layer:** the filter-envelope synth, higher up, doing the movement.

Then: high-pass the wub above roughly 100 Hz so the sub owns the bottom alone. If both layers play full-range, the low end turns to mud and no amount of EQ fixes it.

**Sidechain the sub to the kick.** Compressor on the sub, sidechain input from the kick, fast attack, short release, 3 to 6 dB of gain reduction. The kick punches through, the sub dodges it, nothing clips.

Reese bass is the other option, the detuned, wide, angry one. Two saws detuned against each other, heavy low-pass. It suits speed garage and the darker end of the genre. Use it instead of the wub, not on top of it.

## Stage 3: chords and progression patterns

Here's the thing that separates UKG from house more than the drums do. UKG harmony is jazz-adjacent and dirty. It borrows the extended chords from US garage and soul, then plays them with a dark, minor-key twist and usually on an electric piano or a filtered pad.

Three chord types carry the genre:

- **Minor 9ths (m9).** The default UKG chord. In D minor: D, F, A, C, E. That added 9th (E) is the whole sound.
- **Minor 11ths (m11).** Softer and more floating. D, F, A, C, E, G. Use these for the breakdown.
- **Sus2 and sus4.** No third, so the chord is neither happy nor sad. Deep house and UKG both live on these. In D minor, A7sus4 is A, D, E, G.

Major 7ths and flat-9 dominants show up too, mostly as passing chords. The rule is simple: pick a minor 7th, minor 9th, or sus chord, and add one extension.

### Progressions that work

Write in minor, keep it to three or four chords over eight bars, and move by small intervals. Two progressions in D minor:

**One (the standard):** Dm9 - Bbmaj7 - Gm9 - A7sus4. That's i, VI, iv, V. One chord per two bars. Eight bars total.

**Two (darker):** Dm9 - Gm9 - Bbmaj7 - C9. i, iv, VI, VII. The C9 at the end never quite resolves, which is the point.

**Three (for the breakdown):** Dm11 held for four bars, then Gm9 for four. Two chords, eight bars, lots of space for the vocal.

### Playing them so they sound right

**Inversions, not roots.** If the bass is playing root notes, the chord doesn't need to. Keep the chords in a narrow register, roughly one octave, and move the top voice by the smallest step it can take. Falling bass line, rising chord voicing, or the reverse. This is what makes the progression sound written rather than triggered.

**Voicing rule that helps:** try Dm9 as A-D-F-C-E in the left-to-right order that keeps your thumb near where the last chord's thumb was. Smooth voice leading does more than any plugin.

**Stab versus pad.** Rhythm matters as much as the notes:

- *Stabs:* short, chords on the off-beats, swung with the same groove as the drums. This is the classic sound. Draw them in on the "and" positions and let the Groove Pool shuffle them with everything else.
- *Pads:* long sustained chords, low-pass filtered, sitting under the track. Use pads in the intro and breakdown, stabs in the drop.

**Sound design for the chords.** Electric piano is the honest default, and Ableton's Electric covers it. For the rest:

- Two oscillators, detuned slightly, for width. Keep them under 6 or 7 cents or it turns sour.
- A real low-pass filter, down around 1 to 2 kHz, then a touch of resonance. UKG chords are not bright.
- Juno-style chorus, which is exactly what TAL-Chorus-LX is, listed below. Free and it's the right flavour.
- Short plate reverb, 1 to 1.5 seconds, along with a touch of ping-pong delay on the stabs only.

**Register discipline.** Keep chords above roughly 200 Hz and let the bass and sub own everything below. Two minor 9th chords played low and loud is the fastest way to make a UKG track unlistenable.

## Stage 4: melody, the pluck and the vocal chop

UKG doesn't usually have a big lead. If there's a melody, it's one of two things:

**A pluck.** A short, high, filtered synth line, two to four notes, answering the vocal rather than leading. Think of it as a question the chords ask and the pluck answers. Keep it in the gaps between the chord stabs, so the two parts never fight.

**A vocal chop doing melodic work.** This is the more authentic option and it's Stage 5.

Both live high. Above 500 Hz, filtered, thin. The mix already has weight down low.

## Stage 5: samples and the pitched-up vocal

The pitched-up vocal is the third pillar of the genre. Here's why it works: pitching a vocal up moves it away from speech range and toward an instrument. You stop hearing words and start hearing melody, which is why you can sit a pitched-up diva sample next to chords and have it function as a lead.

How to do it:

1. Find a clean acapella, ideally an R&B or soul vocal. Royalty-free is the safe route. If you're sampling a record, know that you're sampling a record.
2. Warp it. In Live, Complex Pro mode keeps pitched vocals from smearing. For clean chops, Beats mode with a short transient is tighter.
3. Transpose up. Three to seven semitones is the useful range. Past that it turns cartoonish.
4. Chop it. Slice into syllables and one-word fragments, then rebuild a new hook out of them. You're not quoting the vocal, you're using it as a one-shot library.
5. Re-tune each chop to the track key. A chop that's a semitone out will ruin the mix faster than a bad drum sound.
6. Filter and reverb it. High-pass around 150 Hz so it doesn't fight the chords, then a long reverb, wet enough that it floats.

Two chops used as a call-and-response is usually enough. The tendency is to use too many, and the loop stops sounding like a hook.

**Other sample glue that fits:** vinyl crackle or tape hiss underneath everything, an airy texture loop for atmosphere, and a riser or reverse cymbal into each drop. These are what turn a loop into a composition. A texture bed running the whole track, just audible, is the single most underrated element in the genre.

## Stage 6: arrangement, eight bars at a time

UKG arrangements are subtraction-driven. You build a full loop, then take elements out and put them back. The track is mostly a full loop with the floor falling away and returning.

Work in eight-bar blocks. Over 64 bars:

**Bars 1 to 8, intro.** Kick, hats, and a filtered pad. No bass, no full drums. Something has to be held back for the drop.

**Bars 9 to 16.** Add the full drum pattern and the texture bed. Still no sub. This is the section that makes the drop feel like a drop.

**Bars 17 to 32, first drop.** Everything. Full drums, wub bass, sub, chord stabs, vocal chops. Run it for 16 bars, with the third-bar lift fill every eighth bar.

**Bars 33 to 40, breakdown.** Strip it back: pads, a vocal chop, the texture. No kick, no sub. Hold the tension here. This is where a remixer or a DJ loops.

**Bars 41 to 48, the build back.** Bring the drums back in stages. Hats, then kick and snare, then a riser into the final drop.

**Bars 49 to 64, second drop and outro.** Full arrangement again, ideally with one new element that wasn't in the first drop, a counter-melody or a new chord in the progression. Then strip to the intro elements for the last eight bars so the track can be mixed out.

Two rules that matter more than the map. Change one thing every eight bars, or the loop dies. And never introduce an element you don't remove later.

## Stage 7: mixing and mastering, the short version

Five moves, in order of how much they matter.

**1. Low end, first and hardest.** Everything below roughly 120 Hz should be mono. One sub, one kick, and they should not overlap in time if you can help it. Sidechain the sub to the kick, 3 to 6 dB. Check the mix on a phone speaker; if the bass disappears entirely you have a mono/phase problem, and if the phone speaker distorts you've left a low-frequency buildup at 60 to 100 Hz.

**2. High-pass everything that isn't bass.** Chords, vocals, hats, texture. Every element that doesn't need low end is adding mud to the ones that do.

**3. Bus compression for glue.** A compressor on the drum bus, slow attack (30 ms), auto release, only 2 to 3 dB of reduction. Then one on the master bus, gentler still. This is what makes separate samples sound like one record.

**4. Saturation, not volume.** The wub, the drums, and the chords all benefit from a little tape or tube saturation. Saturation adds harmonics, and harmonics read as loudness, especially on small speakers. It's how you get loudness without crushing the transients.

**5. Reference, then limit.** Pick one released UKG track you like, put it on a channel, and A/B against it at matched loudness. Match the low end and the brightness to it, not to your own taste. Then a limiter, ceiling at minus 1 dB, taking off only 2 or 3 dB on the loudest hits.

If you only do one thing here, do the low-end work. UKG lives or dies on the sub sitting right.

## Free plugins that fit

Everything here is genuinely free, available for Linux, macOS and Windows unless noted, and useful for this specific genre rather than free-in-general.

**Instruments**

- **[Vital](https://vital.audio/)**: Wavetable synth, the go-to free alternative to Serum. Draw the filter envelope for your wub here.
- **[Surge XT](https://surge-synthesizer.github.io/)**: Open-source hybrid synth. Deep, and genuinely free, no account required.
- **[Dexed](https://asb2m10.github.io/dexed/)**: FM synth modelled on the Yamaha DX7. The FM bass and the bright electric-piano-adjacent tones live here.
- **[OB-Xd](https://www.discodsp.com/obxd/)**: Oberheim-style analogue synth. Good for warm pads and the wide detuned layers.
- **[TAL-NoiseMaker](https://tal-software.com/products/tal-noisemaker)**: Virtual analogue with a built-in Juno chorus and reverb. Low CPU, and the chorus alone is worth the install.
- **[Spitfire LABS](https://labs.spitfireaudio.com/)**: Free sampled instruments. The soft pianos and mallets cover the melodic side.

**Effects**

- **[TAL-Chorus-LX](https://tal-software.com/products/tal-chorus-lx)**: A standalone version of the Juno-60 chorus. This is the chord sound. Free, and it will do more for your UKG chords than any paid plugin.
- **[Valhalla Supermassive](https://valhalladsp.com/shop/reverb/valhalla-supermassive/)**: Free reverb and delay. Made for long, ambient tails, which is exactly what a UKG breakdown wants.
- **[Valhalla Freq Echo](https://valhalladsp.com/shop/delay/valhalla-freq-echo/)**: Free frequency-shifting delay. Instant dubby space on vocal chops.
- **[TDR Nova](https://www.tokyodawn.net/tdr-nova/)**: Free dynamic EQ. Use it for the sidechain-style ducking and for taming the 200 Hz region on the chords.
- **[Kilohearts Essentials](https://kilohearts.com/products/kilohearts_essentials)**: Free bundle including a good chorus, a tape-style saturator, a limiter, and a stereo widener.
- **[Analog Obsession](https://analogobsession.com/)**: Free analogue-modelled EQs, compressors and preamps. Their channel strips are the easy route to that slightly saturated old-record character.
- **[Airwindows](https://www.airwindows.com/)**: A large free plugin collection. Consistent, no-installer, and excellent for saturation and console colour.
- **[Baby Audio freeware](https://babyaud.io/freebies)**: Free chorus, phaser and reverb from a plugin maker that otherwise charges real money.
- **[Voxengo SPAN](https://www.voxengo.com/product/span/)**: Free spectrum analyser. You need one to do the low-end work in Stage 7 properly.
- **[Sitala](https://decomposer.de/sitala/)**: Free drum sampler, if you'd rather build your kit outside Drum Rack.
- **[Cardinal](https://cardinal.kx.studio/)**: Free modular synth, a fork of VCV Rack. Overkill for a first track, and a lot of fun once the basics are done.
- **[Xfer OTT](https://xferrecords.com/freeware)**: Free multiband compressor. Aggressive, and it's the reason a lot of modern loud mixes sound the way they do.

## Free sample packs that fit

All royalty-free. All free to download. Check the licence page on each before you release, since redistribution rules differ.

- **[MusicRadar SampleRadar: 316 free UK garage samples](https://www.musicradar.com/news/sampleradar-uk-garage-samples-1)**: 316 WAVs at 246 MB, sorted into 128, 130, 132, 136 and 142 BPM folders. Beats, basses and synths. The single best starting point because the tempo folders match how you'll actually browse.
- **[Ghost Syndicate: Free UK Garage Samples](https://ghostsyndicate.audio/free-garage-samples/)**: Free versions of their Control and Garage Sessions Vol. 3 packs. Shuffled drum loops, one-shots, bass sounds and vocal textures. Aimed at the darker, bass-driven end.
- **[Loop Cult CONTROL: 338 free UK Garage samples](https://audiartist.com/control-free-uk-garage-sample-pack)**: 27 drum loops, 25 kick and snare loops, 37 closed hats, 25 kicks, 26 snares, 25 bass loops, 20 synth loops and 33 MIDI files. The MIDI files are the reason to grab this one. You can study the actual patterns.
- **[Samplephonics free Garage samples](https://www.samplephonics.com/products/free/garage)**: A rotating set of free garage freebies, including 2-step percussion and UKG bass. Quality is high because these are free cuts from paid packs.
- **[Looperman UK garage loops](https://looperman.com/loops/tags/free-uk-garage-loops-samples-sounds-wavs-download)**: Community-uploaded loops, royalty-free for commercial and non-commercial use. Consistency varies, so audition before you commit. Good for filling a specific gap.
- **[Splice UK Garage genre page](https://splice.com/sounds/genres/uk-garage/samples)**: Not free, but the free account lets you browse and preview the entire catalogue, and every sound is available individually rather than as a whole pack. Worth a free account purely for the reference material.

### Study material worth the time

- **[Studio Brootle: UK Garage drum pattern with presets and bassline](https://www.studiobrootle.com/uk-garage-drum-pattern-with-presets-and-bassline)**: Free Ableton drum racks, plus Operator and Wavetable presets for the FM wub and the Reese bass. The source for the SP1200 groove setting above.
- **[Minimal Audio: Beat Breakdown UK Garage](https://blog.minimal.audio/uk-garage)**: A full build broken down element by element, at 130 BPM in D minor. Useful because it explains *why* each sample choice matters, not just where to place it.
- **[Producer Hub: UK Garage Production Guide](https://producerhub.co.uk/blog/uk-garage-production-guide)**: The clean 2-step grid and the ghost-note and velocity approach.
- **[STRANJAH: UK Garage 101, the top drum patterns](https://www.youtube.com/watch?v=S2tZsjo4GxE)**: Eleven patterns in one video: classic 2-step, tresillo, halftime, steppa, and the Jersey-club-influenced variants. Best single overview of where UKG rhythm can go after the basic grid.

## The one-page recipe

If you skip everything else, work in this order.

1. Session: 134 BPM, D minor, 8-bar loop brace.
2. Drums: kick on steps 1 and 11, snare on 5 and 13, open hat on 3, closed hats on 7, 9 and 15. Shorten and pitch up the snares.
3. Groove: SP1200 16 Swing-71 or Swing 16ths 73, amount around 60 percent. Apply to everything.
4. Bass: sub sine on the root, mono, plus a wub layer with a 200 to 400 ms filter attack. High-pass the wub at 100 Hz. Sidechain the sub to the kick.
5. Chords: Dm9 - Bbmaj7 - Gm9 - A7sus4, one per two bars. Stabs on the off-beats, smooth voice leading, low-passed, Juno chorus on top.
6. Vocal: pitch an acapella up three to seven semitones, chop it, re-tune each chop, use two fragments as call-and-response.
7. Texture: a crackle or atmosphere bed under the whole track, barely audible.
8. Arrange in eight-bar blocks. Intro without bass, first drop at bar 17, breakdown at 33, second drop at 49. Change one thing every eight bars and remove everything you add.
9. Mix: mono below 120 Hz, high-pass everything else, bus compression for glue, saturation for loudness, one reference track, limiter at minus 1 dB.

Two things decide whether it sounds like UK garage, and neither is a plugin. The kick is not on every beat. The swing is pushed further than feels correct. Get those two right and the rest of this is polish.
