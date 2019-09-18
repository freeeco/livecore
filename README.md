# LiveCore: Increasing Liveness in a Low-level Dataflow Programming Environment
---

![LiveCore Banner](https://i.imgur.com/tMiYKKs.png)

Poster presented by David Alexander ([@freeeco](https://github.com/freeeco)) and Jack Armitage ([@jarmitage](https://github.com/jarmitage)) at [Audio Mostly 2019](https://audiomostly.com), University of Nottingham, Nottingham, UK.


## Introduction
---

Liveness is an important factor in live coding but frequently liveness focuses on high-level, textual environments.
While these environments offer many abstraction capabilities, users of low-level dataflow programming environments could also benefit from increased liveness.
In this work we introduce LiveCore: a macro library for the low-level dataflow environment [Reaktor Core](https://www.native-instruments.com/fileadmin/ni_media/downloads/manuals/REAKTOR_6_Building_in_Core_English_2015_11.pdf) enabling live coding.
LiveCore manages program state at audio rates and provides a suite of modules for musical pattern generation, sequencing and synthesis.
LiveCore increases liveness in Reaktor Core from an editable dataflow program, to one with continuous audio suitable for musical performance.

---

## Setup

- Clone or download this repo
- Unzip the LiveCore ensemble
- Open the ensemble in Reaktor
- More instructions coming soon... contact us if you're impatient and want help!

## Design
---

LiveCore is a library of modules for live coding in Reaktor Core and consists of macros or Blocks containing code with sets of input and output ports.
Each macro contains as few operations as possible thus keeping the code efficient and compact.
Macros that are connected and therefor should execute are compiled into a few dozen bytes each. Disconnected macros are ignored by the compiler.
The result is a single block of executable code that can sit inside the CPU's program cache without any calls to external functions.

The fundamental building block of the library is a Phase Driver: a ramp waveform generator that represents a musical period, which could be for example two measures long.
The Phase Driver is based on a simple phase accumulator that increments every audio clock and wraps back to 0 when it reaches 1.
Additional blocks can subdivide this cycle into smaller periods, which can be quantised and used to look up tables of values for parameter modulation or note data. 
The output of the ramp generator can be modified using a waveshaper to achieve different grooves or more complex rhythmic patterns.
It can also function as an audio-rate oscillator by using a further waveshaper to create different wave-forms or by using it to read samples from a wavetable using the Sample Reader described later in this section.
If wrapping is disabled, ramps can be triggered in oneshot mode to drive sample playback or be used as envelopes if followed by a function generator or another waveshaper.

### Modules
---

- [Phase Driver](#phase-driver)

#### Phase Driver

Ramp generator that can run in 'one-shot' or 'looping' mode.

![Phase Driver](https://i.imgur.com/55xRAVY.png)

#### Sequencer

Quantises the output of the Phase Driver and uses the result to select pattern data from it's inputs.

![Sequencer](https://i.imgur.com/cJZeVMz.png)

#### Splitter

Divides an input phase into sub-phases.

![Splitter](https://i.imgur.com/8dODKH6.png)

#### Gate

Creates a gate from a Phase Driver using the function `out = x < gate length`.

![Gate](https://i.imgur.com/FD8675k.png)

#### Mixer

A simple module that sums together all the inputs.

![Mixer](https://i.imgur.com/R41SWi4.png)

#### Limiter

Creates an up-ramp until the level at the input is reached and then a down-ramp.
Triggered from a Gate it can be used as an envelope, and 
it can also be used as a signal smoother or filter.

![Limiter](https://i.imgur.com/AQF9i72.png)

#### Waveshaper

Creates different oscillator shapes from the output of the Phase Driver. 
It can be placed before sequencers to create tempo modulations or accelerations, simulate jitter and swing or otherwise alter pattern timing.
It can also be placed between the Phase Driver and the Sample Reader to modify for example playback speed or direction.

![Waveshaper](https://i.imgur.com/7EINPD3.png)

#### Reader

Reads samples from a table and has inputs for `Table Reference` and `Position`. 
Attaching a Phase Driver to the position input will play through the sample.
Short samples can be used as wavetables for more complex oscillator shapes than the Waveshaper.

![Reader](https://i.imgur.com/ragtr1r.png)
