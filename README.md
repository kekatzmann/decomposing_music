# Decomposing Music

This repository contains the Jupyter notebooks and instrument samples used in my final project at Metis, Decomposing Music.

The goal of this project is to develop a method of isolating notes in a song, and identifying the instrument playing each note. Isolating  notes is acomplshed by leveraging the fact that notes are composed of "clusters" of frequencies (main frequency plus overtones). Such distinct clusters can easily be isolated using Non-negative matrix factorization, and the instruments by which they are played can be identified with a classifier algorithm, such as Random Forest.

### Data
The data used was instrument samples, which were gathered from freesound.org and the University of Iowa's music department. I created unique audio files ("songs") with these samples in Audacity. Each song was roughly 2 minutes long, and contained an instrument playing 2 notes with no rhythm to the playing. The songs and samples can be found in the music directory in this repository, and can be played in the music_exploration.ipynb and note_isolation.ipynb notebooks. Currently, the dataset contains two classes, flute and piano.

### Note Isolation
This is the crux of the project. It involves two main steps: signal processing to prepare the audio, and machine learning to separate the notes. This work can be found in note_isolation.ipynb.

<b>Signal Processing</b><br>
The songs first were passed through a butterworth passband filter, filtering out frequencies below 20 Hz and above 16,000 Hz, eliminating the ripple in audio caused by the recording process. Songs were then parsed into 5 second intervals to reduce the completixy of the . Next, I performed a Fourier transform on 1/4 second windows of each interval. 1/4 second windows are large enough to precicely capture all of the frequencies that are present, and small enough to preserve temporal granularity throughout the 5 second interval. These processes yielded a matrix of m time windows by n frequencies (20 × 5512).

<b>NMF</b><br>
Non-negative matrix factorization was then performed on the matrix to isolate the notes. NMF represented the 
