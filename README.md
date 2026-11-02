Self-directed learning project: decoding phonological features from EEG–TMS speech-perception data.

Dataset: EEG dataset for speech decoding, OpenNeuro ds006104 / NEMAR on006104 (CC0 licence). 24 participants from two studies; 61 EEG channels (extended 10-20 system); EEG combined with TMS of the motor cortex during phoneme perception.

Citation: Moreira et al. (2026), DOI 10.82901/nemar.on006104.

Motivation: This project is a fun way for me to learn EEG preprocessing and decoding on a public dataset, with attention to the validation problems specific to EEG :)

Status: work in progress.

Target question: can the place of articulation of the consonant (bilabial vs alveolar) be decoded from EEG after stimulus onset?

///

(Updating this on the go)

What I've done so far:
1) Data inspection for one participant (sub-P01).

Design of the phoneme task (from the events file)
476 trials, 952 event rows: each trial has a TMS event followed by a stimulus event 50 ms later.
Median spacing between trials is about 4 s, with some long gaps (maximum about 610 s).
Last event onset at about 2933 s, while the recording lasts about 3287 s.
Four TMS targets: lip, tongue, control_lip, control_tongue.
Stimuli are consonant–vowel (CV, 237 trials) or vowel–consonant (VC, 239 trials) pairs made from four consonants (p, b, t, d) and five vowels (a, e, i, o, u).

2) Label construction.

The place and voicing columns in events.tsv do not describe the consonant in every trial. Cross-tabulating them against labels derived from the phoneme pair shows that they appear to reflect only the first phoneme: when the pair starts with a vowel, the trial defaults to "alveolar" / "no".

Original place	Consonant alveolar	Consonant bilabial
alveolar (355 trials)	228	127
bilabial (121 trials)	0	121
Original voicing	Consonant voiced	Consonant voiceless
no (364 trials)	122	242
yes (112 trials)	112	0

This is an inference I'm making from the counts, not from the dataset documentation (the accompanying events.json is generic and does not match the actual column values), so I rebuild the labels from phoneme1 and phoneme2 to balance the classes: place 248 bilabial vs 228 alveolar; voicing 234 voiced vs 242 voiceless; vowels 91–106 trials each. Within the two main TMS targets (lip, tongue), the two place classes are exactly equal.

3) Inspection of the raw EEG file.

2000 Hz sampling rate, 61 EEG channels plus one extra channel named Status, about 3287 s, no annotations in the EDF.
The Status channel is not a usable trigger channel: it has a single transition near the very end of the recording. Timing therefore has to come from events.tsv, and its alignment with the EEG has to be verified from the TMS artefact in the signal itself.
The EDF header reports filter settings (0.0 / 1000 Hz) that differ from the sidecar JSON (0.1 / 350 Hz). I have not yet resolved which is correct; the spectrum will be checked before relying on either.


///

Planned next steps:
- Detect TMS pulses in the continuous signal and check alignment with events.tsv;
- Characterise the TMS artefact (amplitude, duration, recovery) and decide on an artefact-handling strategy and the earliest usable analysis window;
- Preprocessing (filtering, bad channels, re-referencing, downsampling, ICA if appropriate) and epoching around stimulus onset;
- Decoding of consonant place (bilabial vs alveolar) with regularised logistic regression or LDA;
- Validation with blocked cross-validation (not random shuffling);
- Decoding within each TMS target separately, and checking that performance cannot be explained by the artefact or by TMS target;
- Secondary questions: voicing, vowel identity, CV vs VC order, and whether decodability differs across TMS targets.

Planned repository structure:
README.md
notebooks/      inspection and analysis notebooks
src/            reusable functions (labels, epoching, decoding)
requirements.txt

Author
Francesca Pagliara
