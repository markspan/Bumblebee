# Bumblebee

A small desktop app that converts **XDF** recordings (Lab Streaming Layer) into
**BrainVision** files (`.vhdr` / `.vmrk` / `.eeg`) for analysis in BrainVision
Analyzer and other EEG tools.

Point it at an `.xdf` file, pick which stream holds your signal, optionally pick
the marker streams to keep, and Bumblebee writes a BrainVision dataset next to
the original file.

## What it does

- Reads any `.xdf` / `.xdfz` file and lists every stream it contains.
- Lets you choose **one signal stream** (EEG, ECG, EMG, …) to export.
- Lets you choose **any number of marker/trigger streams** whose events become
  BrainVision annotations, time-aligned to the signal.
- Streams that self-identify as `EEG` are scaled from µV to V (BrainVision's
  expected unit); every other stream type is exported in its raw LSL units with
  no scaling.
- Channel names are taken from the stream metadata when present, otherwise
  labelled `CH1`, `CH2`, …
- Runs the conversion on a background thread with a live log, so the window
  stays responsive.

## Output

The result is written to the **same folder** as the source file, with the same
base name and a `.vhdr` extension (plus its companion `.vmrk` and `.eeg`). For
`session1.xdf` you get `session1.vhdr`. Any existing files of that name are
overwritten.

## Using the app

1. **Open XDF …** and select your recording. Bumblebee loads it and fills two
   tables — signal streams on top, marker/trigger streams below. (It sorts
   streams automatically: anything typed `marker`, `trigger`, `events`, etc., or
   with a sample rate of 0, goes to the marker table; everything else is a
   signal.)
2. **Select the signal stream** to export in the top table (exactly one). If
   there is only one signal stream it is selected for you.
3. **Optionally select marker streams** in the bottom table. Use Ctrl-click or
   Shift-click to pick several; leave it empty to export with no annotations.
4. Click **Convert to BrainVision**. Watch the log; a dialog confirms the output
   path when it finishes.

## Running from source

The project uses [uv](https://docs.astral.sh/uv/) for dependency management.

```bash
git clone https://github.com/markspan/Bumblebee.git
cd Bumblebee
uv sync            # creates .venv and installs mne, pyxdf, pybv, PySide6
uv run python Bumblebee.py
```

`uv run` uses the project's virtual environment automatically, so there's no
need to activate anything. If you prefer to activate it:

```bash
# Windows PowerShell
.venv\Scripts\Activate.ps1
python Bumblebee.py
```

## Building a standalone Windows executable

Two build routes are provided; either produces a single self-contained
`bumblebee.exe` that needs no Python install on the target machine.

**PyInstaller** (fast builds, bundles the interpreter):

```bash
uv add --dev pyinstaller     # once
uv run pyinstaller bumblebee.spec
# → dist\bumblebee.exe
```

**Nuitka** (slower builds, compiles to machine code for faster startup):

```bat
nuitka_compile.bat
# → bumblebee.exe
```

Both embed `bumblebee.ico` as the application icon.

## Requirements

- Python ≥ 3.13
- [mne](https://mne.tools/), [pyxdf](https://github.com/xdf-modules/pyxdf),
  [pybv](https://github.com/bids-standard/pybv), [PySide6](https://doc.qt.io/qtforpython/)

All are installed automatically by `uv sync` from the pinned `uv.lock`.

## Licence

See [LICENSE](LICENSE).
