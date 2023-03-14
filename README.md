## Goal

Classify 6 different electric guitar chords (3 distinct chords, each with 2 finger positions, in total, 6 target classes) from audio.

## Dataset

### Classes

Open C major chord
Bar C major chord (root note at 5th string)
Open G major chord
Bar G major chord (root note at 6th string)
Open A major chord
Bar A major chord (root note at 6th string)
(from left to right: open C, bar C, open G, bar G, open A, bar A)

### Characteristics

40 audio clips (.m4a) per class (240 in total)
Length: 3~6 seconds, length varied.
Guitar chord was strummed after recording had started (there are various small moments of quiet at the beginning of every audio).
3 kinds of strumming patterns appeared in each of the 40 clips. There are roughly ⅓ of down stroking, ⅓ of up stroking, and ⅓ of finger picking. This is to increase the robustness of the model.
C major: C, E, G; G major: G, B, D; A major: A, C#, E.
Open vs bar chord: Both chords consist of the same notes, but in different octaves or on different strings (different strings have slight differences in sound). This is to test if the model can distinguish between similar chords (open A major and bar A major chord can be difficult to distinguish by ear sometimes due to 5 notes of them being the same notes, in the same octave, in the same order, only on different strings).
Source: Self recorded
Tools: Iphone microphone, Stratocaster Guitar, Fender Amp (clean tone)

## Preprocessing

### Librosa used for importing and sampling.
MFCC performed using Librosa.
Below are spectrogram from each target:
Chords with the same name (C_open, C_bar, G, A_open, A_bar) have almost identical frequency distribution.
Open vs. bar chords have very similar frequency distribution due to the harmonic nature of sounds. There are slight differences in the strength for each frequency between open and bar chords.
I'm curious about the ability of models to detect slight differences in strength, while overlooking timing differences.
