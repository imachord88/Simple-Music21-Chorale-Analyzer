# Bach Chorale Analyzer

An interactive command-line tool for analyzing the four-part harmonized chorales of J.S. Bach. Built with [music21](https://github.com/cuthbertLab/music21), it reports a chorale's structure, labels the scale degree of every note, and labels the interval each upper voice forms above the bass. Results can be printed in the terminal, opened in MuseScore, or saved as MusicXML.

## Features

- Load a chorale from the music21 corpus or from a local score file
- Summarize measures, repeats, fermatas, time signatures, key signatures, final bass pitch, and key
- Calculate scale degrees for soprano, alto, tenor, and bass
- Calculate soprano, alto, and tenor intervals above the bass at every point where either voice moves
- Produce annotated scores for viewing or saving

## Requirements

- Python 3
- [music21](https://pypi.org/project/music21/)
- [MuseScore](https://musescore.org/) or another MusicXML reader, only for menu options 4 and 5

## Installation

Clone this repository, then install music21:

```bash
pip3 install --upgrade music21
```

To open scores from the program, point music21 to your MuseScore executable:

```python
from music21 import environment
environment.set('musicxmlPath', '/path/to/MuseScore')
```

## Usage

Run the script from the repository folder:

```bash
python3 chorale_analyzer.py
```

The program asks for:

1. Your name
2. The chorale title, such as `Aus meines Herzens Grunde`
3. The BWV number, such as `269`
4. The score source: the music21 corpus or a local file path

It then opens this menu:

| Option | Action |
| ------ | ------ |
| `1` | Show the structural analysis |
| `2` | Print scale degrees in the terminal |
| `3` | Print intervals in the terminal |
| `4` | Open the scale degree analysis in MuseScore |
| `5` | Open the interval analysis in MuseScore |
| `6` | Save the scale degree analysis as MusicXML |
| `7` | Save the interval analysis as MusicXML |
| `8` | Load a new score |
| `0` | Quit |

Saved files are named `<title> SD Analysis.musicxml` and `<title> Interval Analysis.musicxml`. The save location must end with a path separator, such as `/Users/Machine/Downloads/`.

## How the analysis works

### Key

The program builds the major and minor keys from the last key signature in the bass, then compares their tonics with the final bass pitch. With one sharp and a final bass note of E, the key is E minor. If neither tonic matches, it uses music21's key analysis of the full score.

### Scale degrees

Each note receives a scale degree from 1 to 7 relative to the key for the whole chorale. In the annotated score, the degrees appear as lyrics under the notes.

### Intervals

Intervals are generic, measured from the bass, and reduced to a value from 1 to 8:

```text
interval = ((upper_degree - bass_degree) % 7) + 1
```

In C major, C5 over G3 gives `((1 - 5) % 7) + 1 = 4`, a fourth. When the result is 1 but the two pitches differ, the label becomes 8, so C5 over C3 is labeled 8.

In the annotated score, interval labels appear below the soprano, alto, and tenor staves. The bass staff shows scale degrees.

## Limitations

- Scores must have exactly four parts in soprano, alto, tenor, bass order, and the bass must contain a key signature.
- The BWV number input must be in the program's list, even when loading a local file. 
- Scale degrees ignore accidentals, so F4 and F#4 are both degree 4 in C major.
- Intervals show size only, not quality, so major and minor thirds are both labeled 3.
- Compound intervals are reduced, and different pitches on the same scale degree are labeled 8, even when they are not a true octave apart.
- The analysis uses one key for the whole chorale, so modulations are not tracked.
