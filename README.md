# WaveLens free tools

Two free command-line tools from the [WaveLens](https://wavelens.dev) audio testing toolkit:

| Tool | What it does |
|---|---|
| **wavgen** | Generates WAV test signals: tones, sweeps, level steps, noise, square and sawtooth waves, with an optional sync tone. |
| **wavchan** | Works with the channels of WAV files: split, pack, inspect, join and mix. |

Version 0.7.0-beta, for Windows (64-bit), macOS (Intel) and Linux (64-bit Intel/AMD). There is nothing to install and no license to set up.

## Download

Get the zip from the [Releases](https://github.com/wavelens-dev/wavelens-free-tools/releases) page and unzip it anywhere. It is the file named `wavelens-free-tools-…zip`; ignore the "Source code" archives GitHub adds to every release, which hold only this README and the guides. It holds one folder per system, `windows`, `macos` and `linux`. Each has `wavgen`, `wavchan` and a `SHA256SUMS` file. Use the folder for your system, and run the tools from a terminal opened in that folder.

### Windows

Use Command Prompt or PowerShell (in PowerShell put `.\` in front, for example `.\wavgen`).

**Check your download.** `certutil -hashfile wavgen.exe SHA256` prints the hash to compare with the one in `SHA256SUMS`.

**Windows may warn about an unknown publisher.** These early beta builds aren't code-signed yet, so Windows SmartScreen can show a warning the first time you run them. Check the hash, then choose "More info", then "Run anyway".

### macOS

Open Terminal, go to the `macos` folder, and run the tools with `./` in front, for example `./wavgen --help`. The builds are for Intel Macs; Apple Silicon Macs run them through Rosetta 2, which macOS offers to install the first time.

**Check your download.** `shasum -a 256 -c SHA256SUMS` should print `OK` for both files.

**macOS may refuse to open them.** These early beta builds aren't signed or notarized by Apple yet, so macOS can block the first run of a downloaded copy. Check the hash, then clear the download flag from inside the `macos` folder:

```
xattr -dr com.apple.quarantine .
```

### Linux

Open a terminal, go to the `linux` folder, and run the tools with `./` in front, for example `./wavgen --help`. Each tool is a single self-contained file for 64-bit Intel/AMD processors, so there is nothing to install. If the shell answers "permission denied", run `chmod +x wavgen wavchan` once.

**Check your download.** `sha256sum -c SHA256SUMS` should print `OK` for both files.

## Quick start

The examples write `wavgen` and `wavchan` on their own; on macOS and Linux write `./wavgen` and `./wavchan`.

A 5-second 1 kHz test tone at −20 dBFS:

```
wavgen --mode tone --tone 1000:-20 --samplerate 48000 --bitdepth 24 --duration 5 -o tone.wav
```

Combine two mono files into one stereo file, then look at the result:

```
wavchan pack --segment L:left.wav --segment R:right.wav -o stereo.wav
wavchan info --input stereo.wav
```

## Guides

- [wavgen guide](docs/wavgen-guide.md): every mode and option, with examples.
- [wavchan guide](docs/wavchan-guide.md): split, pack, info, join and mix.
- Full online guides: [wavgen](https://docs.wavelens.dev/tools/wavgen/) and [wavchan](https://docs.wavelens.dev/tools/wavchan/).

`--help` lists a `--license` option because the licensed edition of these tools has one. These free builds need no license and ignore it.

## Using them with WaveLens

`wavgen` makes the reference signals and `wavchan` prepares them, for example by splitting 5.1 or 7.1 content into the stereo pairs that WaveLens compares. `wavelens` then compares a processed file against its reference and writes a report. Start here: [Your first comparison](https://docs.wavelens.dev/quick-start/).

## Questions and bug reports

Open an issue in this repository.
