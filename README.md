# Decomposing Music

This repository contains the Jupyter notebooks and instrument samples used in my final project at Metis, Decomposing Music.

The goal of this project is to develop a method of isolating notes in a song, and identifying the instrument playing each note. Isolating  notes is acomplshed by leveraging the fact that notes are composed of "clusters" of frequencies (main frequency plus overtones). Such distinct clusters can easily be isolated using Non-negative matrix factorization, and the instruments by which they are played can be identified with a classifier algorithm, such as Random Forest.

## Data
The data used was instrument samples, which were gathered from [freesound.org](https://www.freesound.org/) and the [University of Iowa's music department](http://theremin.music.uiowa.edu/MISpiano.html). I created unique audio files ("songs") with these samples in Audacity. Each song was roughly 2 minutes long, and contained an instrument playing 2 notes with no rhythm to the playing. Each instrument played the exact same notes to prevent the calssifier from using distinct frequencies to identify an instruemnt. The songs and samples can be found in the [music directory](https://github.com/kekatzmann/decomposing_music/tree/master/music) in this repository, and can be played in the music_exploration.ipynb and note_isolation.ipynb notebooks. Currently, the dataset contains two classes, flute and piano.

## Note Isolation
This is the crux of the project. It involves two main steps: signal processing to prepare the audio, and machine learning to separate the notes. This work can be found in note_isolation.ipynb.

<b>Signal Processing</b><br>
The songs were first passed through a butterworth passband filter, filtering out frequencies below 20 Hz and above 16,000 Hz, eliminating the ripple in audio caused by the recording process. They were then parsed into 5 second intervals to simplify the task for machine learning processes. Next, Fourier transforms were performed on 1/4 second windows. This window length is large enough to precicely capture all of the frequencies that are present, and small enough to preserve temporal granularity throughout the 5 second interval. These processes yielded a matrix of m time windows by n frequencies (20 × 5512).

<b>NMF</b><br>
Non-negative matrix factorization was then performed on the matrix to isolate notes. The audio dataset in this project only contained two different notes in each 5 second interval, so the number of 'clusters' to be found by NMF was set to two. NMF approximated the initial matrix by W (20 × 2) and H (2 × 5512), where W represents the magnitude of each note throughout time, and H represents the frequencies that compose each note. A note's temporal information from W was combined with its frequency information from H and the instrument playing it to create one labeled vector. Carrying out this process across all 5 second intervals for all songs created a labeled dataframe on which a classifier can be trained.

## Instrument Identification
A Random Forest classifier was trained and tested on this two-class dataset (960 datapoints, 5532 independent features), and achieved an accuracy of 100.0%. Work for this section can be found in instrument_identification.ipynb.


## Takeaways and Future Work
The two instruments used in this dataset, a flute and a piano, are very distinct instruments, especially with regards to the duration of their notes. The flute requires constant pressure to produce sound, and diminishes very quickly when that pressure is absent. The piano requires a note to be struck only once to produce that sound cannot be sustained and diminishes much more slowly than a flute.

Future work will aim to address this issue by including different instruments, such as the guitar, which are more similar to either instrument and will likely require more precise features that are unique to instruments. Along this vein, I plan to investigate the effects of other features on classifier accuracy, such Mel Frequency Cepstral Coefficients. With the inclusion of different instruments and more features, it is possible that a more complex classifier will be needed, such as a Convoluted Neural Network.

Another issue which needs to be addressed is the automation of determining the number of notes present in each 5 second interval. One possible method which could be used to solve this is Singular Value Decomposition.

