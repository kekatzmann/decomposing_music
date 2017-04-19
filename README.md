# Decomposing Music

This repository contains the Jupyter notebooks and instrument samples used in my final project at Metis, Decomposing Music.

The goal of this project was to develop a method to isolate notes in a song, and to identify the instrument playing each note. Isolating the notes is acomplshed by taking advantage of the fact that notes are made up of "clusters" of frequencies (main frequency plus overtones).

### Data
The data used was instrument samples, which I gathered from freesound.org and the University of Iowa's music department. I created unique audio files ("songs") with these samples using Audacity. Each song was roughly 2 minutes long, and contained an instrument playing 2 notes with no rhythm to the playing. The songs and samples can be found in the music directory, and can be played in the music_exploration.ipynb and note_isolation.ipynb notebooks. Currently, the dataset contains two classes, flute and piano.

### Note Isolation
This is the crux of the project. It involves two main steps: signal processing to prepare the audio, and machine learning to separate the notes. The work can be found in the note_isolation.ipynb notebook.

<b>Signal Processing</b>
For signal processing, I first passed the song through a butterworth passband filter, filtering out frequencies below 20 Hz and above 16,000 Hz. Then, I parsed the song into 5 second intervals to reduce the burden on machine learning algorithms. Next, I performed a Fourier transform on 1/4 second windows of each interval. 1/4 second windows are large enough to precicely capture all of the frequencies that are present, and small enough to preserve temporal granularity throughout the 5 second interval. These processes yielded a matrix of m time windows by n frequencies (20 × 5512).

<b>NMF</b>
Non-negative matrix factorization was then performed on the matrix to isolate the notes. NMF represented the 
