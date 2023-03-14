## Goal: 

Classify 6 different electric guitar chords (3 distinct chords, each with 2 finger positions, in total, 6 target classes) from audio.

## Dataset:

### 6 classes:

1. Open C major chord
1. Bar C major chord (root note at 5th string)
1. Open G major chord
1. Bar G major chord (root note at 6th string)
1. Open A major chord 
1. Bar A major chord (root note at 6th string)

### Characteristic:

1. 40 audio clips (.m4a) per class (240 in total)
1. Length: 
   1. 3~6 seconds, length varied.
   1. Guitar chord was strummed after recording had started (there are various small moments of quiet at the beginning of every audio).
1. 3 kinds of strumming patterns appeared in each of the 40 clips. There are roughly ⅓ of down stroking, ⅓ of up stroking, and ⅓ of finger picking. This is to increase the robustness of the model.
1. C major: C, E, G; G major: G, B, D; A major: A, C#, E.
1. Open vs bar chord: 
   1. Both chords consist of the same notes, but in different octaves or on different strings (different strings have slight differences in sound). 
   1. This is to test if the model can distinguish between similar chords (open A major and bar A major chord can be difficult to distinguish by ear sometimes due to 5 notes of them being the same notes, in the same octave, in the same order, only on different strings).

### Source:

- Self recorded
- Tools: Iphone microphone, Stratocaster Guitar, Fender Amp (clean tone)

## Preprocessing:
   1. Librosa used for importing and sampling.
   1. MFCC performed using Librosa.
   1. Below are spectrogram from each target:
      1. Chords with the same name (C\_open, C\_bar, G, A\_open, A\_bar) have almost identical frequency distribution.
      1. Open vs. bar chords have very similar frequency distribution due to the harmonic nature of sounds. There are slight differences in the strength for each frequency between open and bar chords.
      1. I'm Curious about the ability of models to detect slight differences in strength, while overlooking timing differences.


## Models/Algorithms: 
   1. KNN - Pool from K nearest data points. 
      1. Scikit-learn: KNeighborsClassifier
   1. Random Forest - Pool from “tree num” of decision trees.
      1. Scikit-learn: RandomforestClassifier
   1. CNN - Deep learning with convolutional layer. (architecture as below)  (complex)
      1. Keras: Sequential

## Evaluation and Analysis: 5-fold cross validation, stratified.

### i.	 Smaller dataset vs Bigger dataset

1. For CNN, even with 4 training data (1 val), the model still produces high accuracy. Which I find very impressive, but reasonable, since each chord has a distinct spectrogram (for the same chord, the spectrogram should be nearly identical in their frequency axis), which CNN can easily learn to recognize.
1. What’s very interesting is in Random Forest when performances were not perfect yet. For 4 training data, Random forest has a bit of trouble distinguishing between “C\_bar” and “C\_open”, “G\_bar” and “G\_open”, perhaps because of the fact that their spectrograms look more similar. 
1. The same is true for KNN, but we can see that for 8, 16 training data for KNN, it has some problem distinguishing the same chord with different finger position (the circled area). 

||CNN|Random Forest|KNN|
| :- | :- | :- | :- |
|5 audio per class|<p></p><p>0\.93</p>|<p></p><p>0\.9</p>|<p></p><p>0\.633</p>|
|10 audio per class|<p>(Identical to the 1.00 matrix)</p><p>1\.0</p>|<p>(Identical to the 1.00 matrix)</p><p>0\.95</p>|<p>(Similar to the matrix below)</p><p>0\.683</p>|
|20 audio per class|<p></p><p>1\.0</p>|<p></p><p>0\.975</p>|<p></p><p>0\.7749</p>|
|40 audio per class|<p>(Identical to the 1.00 matrix)</p><p>1\.0</p>|<p>(Identical to the 1.00 matrix)</p><p>0\.979</p>|<p>(Similar to the matrix above)</p><p>0\.804</p>|
### ii.	CNN hyperparameter:

|CNN|Epoch: 1|Epoch: 3|Epoch: 10|
| :- | :- | :- | :- |
|Batch: 8|0\.941|0\.991|0\.995|
|Batch: 16|0\.712|0\.991|1\.0|
|Batch: 32|0\.816|0\.995|1\.0|
### iii.	Random Forest tree num

- The accuracy gradually improves with more trees indicating that combining predictions improves generalization.

||Tree num: 1|Tree num: 2|Tree num: 5|Tree num: 10|Tree num: 50|
| :- | :- | :- | :- | :- | :- |
|ACC|0\.791|0\.81|0\.929|0\.941|0\.979|
### vi.	KNN k number

- Increasing K seems to lead to more noise and overfitting, causing a decrease in KNN classifier performance.

||K: 1|K: 2|K: 5|K: 10|
| :- | :- | :- | :- | :- |
|ACC|0\.866|0\.821|0\.808|0\.804|

### vi.	Using open chord for training, bar chord for testing

Initially, my model was trained on open G, D, C chords. Curiosity led me to test whether it can label bar chords. To my surprise, the model achieved a 1.00 accuracy. 

This inspired me to include both bar and open chords in my final dataset and the model was able to differentiate between them.

## Discussion: 
   1. Evaluation showed that even with only 4 training data, the CNN model produced high accuracy, while Random Forest and KNN had some difficulty distinguishing similar chords.
   1. Increasing the dataset size improved model performance for Random Forest and KNN.
   1. Increasing epoch has a positive effect on performance for CNN. Increasing tree numbers also has a positive effect on Random Forest. However, bigger K value led to overfitting for KNN.
   1. The model trained on open chords was able to successfully classify bar chords, leading to the inclusion of both open and bar chords in the final dataset.
   1. I would love to test out trimming the quiet(low db) part of the audio, and try out different sampling frequency and rate for spectrograms. I tried but it messed with every aspect of the data. I failed to get a complete result.. 
   1. If I had more time to collect data, I’d love to train a model to distinguish chords from any instrument, then use it as an auto chord labeling tool for any song of my choice! (Currently available software usually costs 5 dollars per month!)
