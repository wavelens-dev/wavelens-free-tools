# wavgen

A free command-line generator for WAV test signals: tones, sweeps, level steps, noise and band-limited square and sawtooth waves. The output is precise and repeatable, which makes it suited to testing audio codecs and audio processing such as equalizers, virtualizers and dynamics processors.

`wavgen` is part of the [WaveLens](https://wavelens.dev) toolkit. It only generates audio; it doesn't analyze it.

## Get it

Download the zip from the [Releases](../../../releases) page and unzip it. It holds a folder for each system (`windows`, `macos`, `linux`), each with `wavgen` (`wavgen.exe` on Windows). There is nothing to install and no license to set up. Open a terminal in the folder for your system and run it. The [README](../README.md) has the first-run notes for each system.

The examples below write `wavgen` on its own. On macOS and Linux write `./wavgen`; in PowerShell, `.\wavgen`.

## Quick start

A 5-second, 1 kHz tone at −20 dBFS, 48 kHz, 24-bit:

```
wavgen --mode tone --tone 1000:-20 --samplerate 48000 --bitdepth 24 --duration 5 -o calibration_1khz.wav
```

```
wrote calibration_1khz.wav (48000 Hz, 24-bit, 1 ch, 5.000s, 240000 frames)
```

## Options

Every command takes a mode, that mode's own options, and these shared options.

| Option | Description |
|---|---|
| `--mode <mode>` | One of the modes below. Required. |
| `--samplerate <Hz>` | 22,050 to 192,000. Required. |
| `--bitdepth <16\|24\|32>` | Integer PCM bits per sample. Required. |
| `--duration <sec>` | Length in seconds, greater than 0. Fractions are fine. Required, except for `step-seq`. |
| `--channels <1\|2>` | 1 (mono, the default) or 2. Stereo puts the same signal on both channels. |
| `-o <file>` | Output file. Overwritten if it exists. Required. |
| `--synctone` | Wrap the content in a sync tone and silence (see below). |
| `--invert` | Flip the polarity of every sample. |
| `--dc-offset <fraction>` | Add a DC bias, as a fraction of full scale from −1.0 to 1.0 (0.1 = 10%). |

## Modes

| Mode | Makes | Mode options |
|---|---|---|
| `tone` | One or more sine tones summed together | `--tone FREQ:AMPDB[:inv]`, repeatable |
| `linear-sweep` | A sweep at a constant number of Hz per second | `--start FREQ:AMPDB --end FREQ:AMPDB` |
| `log-sweep` | A sweep at a constant number of octaves per second | `--start FREQ:AMPDB --end FREQ:AMPDB` (frequencies above 0) |
| `step-sweep` | One frequency, level stepped by a fixed amount | `--freq HZ --start-amp AMPDB --step STEPDB --step-duration SEC` |
| `step-seq` | One frequency, level jumping between segments | `--freq HZ --segment AMPDB:DURSEC`, repeatable |
| `silence` | Digital silence | none |
| `noise` | White or pink noise | `--color white\|pink --level RMSDBFS [--seed N]` |
| `square` | Band-limited square wave | `--freq HZ --amp AMPDB` |
| `sawtooth` | Band-limited sawtooth wave | `--freq HZ --amp AMPDB` |

## Examples

Three tones at once, for a spectral test:

```
wavgen --mode tone --tone 500:-12 --tone 2000:-12 --tone 8000:-18 --samplerate 48000 --bitdepth 24 --duration 10 -o multitone.wav
```

A stereo logarithmic sweep from 20 Hz to 20 kHz:

```
wavgen --mode log-sweep --start 20:-6 --end 20000:-6 --samplerate 48000 --bitdepth 24 --duration 10 --channels 2 -o sweep.wav
```

A level staircase, 0 dBFS stepping down 6 dB every second:

```
wavgen --mode step-sweep --freq 1000 --start-amp 0 --step -6 --step-duration 1 --samplerate 48000 --bitdepth 24 --duration 5 -o amplitude_steps.wav
```

Repeatable pink noise at −20 dBFS RMS (the same seed always gives the same file):

```
wavgen --mode noise --color pink --level -20 --seed 42 --samplerate 48000 --bitdepth 24 --duration 10 -o pink_noise.wav
```

## Good to know

- **Levels.** Tones, sweeps, square and sawtooth levels are peak dBFS, so 0 dBFS reaches exactly full scale. Noise is set in RMS dBFS.
- **Clipping.** If several tones could add up past 0 dBFS, `wavgen` warns but still writes the file.
- **Stereo.** `--channels 2` duplicates one signal onto both channels. For different content on each channel, generate two mono files and combine them with [`wavchan`](wavchan-guide.md).
- **Sync tone.** With `--synctone` the file becomes: 1 s silence, a short sync tone, 1 s silence, your content, 1 s silence, the sync tone again, 1 s silence. `--duration` still means the content, so 5 s of content gives a 10.5 s file. The WaveLens `trim` command finds the tone and cuts the file back to the content.
- **Output.** Standard WAV with 16, 24 or 32-bit integer samples.

## About `--license`

`--help` lists a `--license` option, because the licensed edition of the tool has one. This free build needs no license and ignores the option, so a command written for the licensed edition runs unchanged.

## More

- Full guide, with every mode and option explained: [docs.wavelens.dev/tools/wavgen](https://docs.wavelens.dev/tools/wavgen/)
- Compare a processed file against its reference with WaveLens: [docs.wavelens.dev/quick-start](https://docs.wavelens.dev/quick-start/)
- Combine, split or mix the files you generate: [wavchan guide](wavchan-guide.md)
