# wavchan

A free command-line tool for working with the channels of WAV files: split a stereo or multichannel file into separate files, pack mono and stereo files into one, inspect a file's channel layout, join files end to end, and mix files together.

`wavchan` is part of the [WaveLens](https://wavelens.dev) toolkit. It only rearranges audio; it doesn't generate or analyze it.

## Get it

Download the zip from the [Releases](../../../releases) page and unzip it. It holds a folder for each system (`windows`, `macos`, `linux`), each with `wavchan` (`wavchan.exe` on Windows). There is nothing to install and no license to set up. Open a terminal in the folder for your system and run it. The [README](../README.md) has the first-run notes for each system.

The examples below write `wavchan` on its own. On macOS and Linux write `./wavchan`; in PowerShell, `.\wavchan`.

## Quick start

Combine two mono files into one stereo file, then look at the result:

```
wavchan pack --segment L:left.wav --segment R:right.wav -o stereo.wav
wavchan info --input stereo.wav
```

```
file:         stereo.wav
format:       PCM (standard)
sample rate:  48000 Hz
bit depth:    24-bit
channels:     2
duration:     1.000s (48000 frames)
channel map:  L, R (inferred -- no channel mask)
```

## Commands

| Command | What it does |
|---|---|
| `split` | One stereo or multichannel file becomes per-channel files |
| `pack` | Mono and stereo files become one multichannel or stereo file |
| `info` | Prints a file's channel layout. Reads only, writes nothing. |
| `join` | Joins files one after another in time |
| `mix` | Adds files together, sample by sample |

```
wavchan split --input <in.wav> [--as mono|stereo] [-o <dir>]
wavchan pack  --segment <CHAN[,CHAN]>:<file> [--segment ...] -o <out.wav>
wavchan info  --input <file.wav>
wavchan join  --input <a.wav> --input <b.wav> [--input ...] -o <out.wav>
wavchan mix   --input <a.wav> --input <b.wav> [--input ...] -o <out.wav>
```

## split

Writes one file per channel, named `<input>_<CHANNEL>.wav`, into the folder given with `-o` (default: next to the input).

```
wavchan split --input lrc.wav -o split_out
```

```
[Save as] split_out/lrc_L.wav
[Save as] split_out/lrc_R.wav
[Save as] split_out/lrc_C.wav
```

With `--as stereo`, a multichannel file is written as stereo pairs instead. A channel with no partner is paired with silence and its file name ends in `_0`:

```
wavchan split --input lrc.wav --as stereo -o split_pairs
```

```
[Save as] split_pairs/lrc_L_R.wav
[Save as] split_pairs/lrc_C_0.wav
```

A plain stereo file always splits into `_L` and `_R`. A mono file can't be split. A multichannel file must be in the extensible WAV format (WAVEX), which carries the channel names.

## pack

Each `--segment` names the channel or channels one input file supplies: one name for a mono file, two separated by a comma for a stereo file. Add a center channel to a stereo pair:

```
wavchan pack --segment L,R:stereo.wav --segment C:center.wav -o lrc.wav
```

The inputs must share the same sample rate, bit depth, sample type and length; `wavchan` tells you which files disagree. If the result is just left and right, you get a plain stereo file. Anything else is written as a WAVEX file with the channel layout recorded.

Channel names (not case-sensitive): `L`, `R`, `C`, `LFE1`, `Ls`, `Rs` (also `Lsr`, `Rsr`), `Lc`, `Rc`, `Cs`, `Lss`, `Rss`, `Lh`, `Rh`, `Lhr`, `Rhr`, `Ch`, `Chr`, `Oh`.

## info

Shows the sample rate, bit depth, channel count and duration of any supported file, and for a WAVEX file also the channel mask and channel names:

```
wavchan info --input lrc.wav
```

```
file:         lrc.wav
format:       WAVEX (WAVE_FORMAT_EXTENSIBLE)
sample rate:  48000 Hz
bit depth:    24-bit
channels:     3
duration:     1.000s (48000 frames)
channel mask: 0x7
channel map:  L, R, C
```

## join and mix

`join` puts files end to end: the output lasts as long as all the inputs together. The inputs must share the same sample rate, bit depth, sample type and channel count.

```
wavchan join --input low.wav --input high.wav -o step.wav
```

`mix` adds files on top of each other at unity gain, so the output is as long as the inputs, which must also have the same number of frames. If the combined peak could go past 0 dBFS, `wavchan` prints a warning and still writes the file.

```
wavchan mix --input tone1.wav --input tone2.wav -o mixed.wav
```

## Good to know

- **Formats.** 16, 24 or 32-bit integer and 32 or 64-bit floating-point WAV, plain or WAVEX. 8-bit files are rejected. The output keeps the format of the input.
- **Typical use.** WaveLens compares one mono or stereo pair at a time, so `split` is how you test 5.1 or 7.1 content, and `pack` is how you build such a file from separately generated channels.

## About `--license`

`--help` lists a `--license` option, because the licensed edition of the tool has one. This free build needs no license and ignores the option, so a command written for the licensed edition runs unchanged.

## More

- Full guide, with every command explained: [docs.wavelens.dev/tools/wavchan](https://docs.wavelens.dev/tools/wavchan/)
- Compare the results with WaveLens: [docs.wavelens.dev/quick-start](https://docs.wavelens.dev/quick-start/)
- Test signals to work with: [wavgen guide](wavgen-guide.md)
