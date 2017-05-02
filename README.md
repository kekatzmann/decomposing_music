# Decomposing Music

This repository contains the Jupyter notebooks and instrument samples used in my final project at Metis, Decomposing Music. 

The goal of this project is to develop a viable, robust method of isolating the notes in a song, and identifying the instrument playing each note. This process would yield an entirely decomposed song, with each note identified, and information collected on when, how, and by what instrument it was played. Such a method would open up myriad promising opportunities. It would allow us to automate sheet music generation, create immensely powerful visualizations from music and sound, and engineer features of songs for use in music analysis and generation.

However, the solution to this problem has proven quite elusive. Briefly, this is because music is created for human ears and our auditory cortices, which are the results of thousands of years of evolution and courtship and are very complex, and songs that we perceive as pleasant-sounding contain  a convolution of overlapping frequencies. They are pretty much just a mishmash of sounds that are innately very similar, if not the same. I talk more about this in my blog, which you can find [here] (work in progress).

So breaking down music is very challenging. That goes for both humans and computers. Humans because it takes so damn long to record every note played by every instrument, and computers because they aren't equipped with the intricate network of neurons designed to react to sounds as we are. But humans love value and there is a lot of value in this solution, so we use computers to help us instantly break down a song into its component parts. Enter this project. Here is my attempt at solving this problem. 

The method I developed to isolate notes and identify the instrument leverages the fact that notes are composed of "clusters" of frequencies (one main frequency and its overtones). Such distinct clusters can easily be isolated using Non-negative matrix factorization, and, once isolated, the instruments by which they are played can be identified with a classifier algorithm, such as a Random Forest or Convoluted Neural Net.

## Data
The data used for this project were instrument samples, which were gathered from [freesound.org](https://www.freesound.org/) and the [University of Iowa's music department](http://theremin.music.uiowa.edu/MISpiano.html). I created unique audio files ("songs") with these samples in Audacity. Each song was roughly 2 minutes long, and contained an instrument playing 2 notes asynchronously, with no rhythm to the playing. Each instrument played the exact same notes to prevent the classifier from using distinct frequencies to identify an instrument. The songs and samples can be found in the [music directory](https://github.com/kekatzmann/decomposing_music/tree/master/music) in this repository, and can be played in the [music_exploration.ipynb](https://github.com/kekatzmann/decomposing_music/blob/master/notebooks/music_exploration.ipynb) and [note_isolation.ipynb](https://github.com/kekatzmann/decomposing_music/blob/master/notebooks/note_isolation.ipynb) notebooks. Currently, the dataset contains two classes, flute and piano.

## Note Isolation
This is the crux of the project. It involves two main steps: signal processing to prepare the audio, and machine learning to separate the notes. This work can be found in [note_isolation.ipynb](https://github.com/kekatzmann/decomposing_music/blob/master/notebooks/note_isolation.ipynb).

<b>Signal Processing</b><br>
The songs were first passed through a Butterworth passband filter, filtering out frequencies below 20 Hz and above 16,000 Hz, eliminating the "ripple" in audio caused by the recording process. They were then parsed into 5-second intervals in order to simplify the task for machine learning processes. Next, Fourier transforms were performed on 1/4-second windows. This window length is large enough to precisely capture all of the frequencies that are present, and small enough to preserve temporal granularity throughout the 5-second interval. These processes yielded a matrix of m time windows by n frequencies (20 × 5512).

<b>NMF</b><br>
Non-negative matrix factorization was then performed on the matrix in order to isolate notes. Since the audio dataset in this project only contained two different notes in each song, the number of 'clusters' to be found by NMF was set to two. NMF approximated the initial matrix by W (20 × 2) and H (2 × 5512), where W represents the magnitude of each note throughout time, and H represents the frequencies each note is composed of. A note's temporal information from W was combined with its frequency-related information from H and the instrument playing it to create a labeled vector. Carrying out this process across all 5-second intervals for all songs created a labeled dataframe on which a classifier can be trained.

## Instrument Identification
A Random Forest classifier was trained and tested on this two-class dataset (960 datapoints, 5532 independent features), and achieved an accuracy of 100.0%. Work for this section can be found in [instrument_classification.ipynb](https://github.com/kekatzmann/decomposing_music/blob/master/notebooks/instrument_classification.ipynb).


## Takeaways and Future Work
The method developed here is clearly a viable one for isolating notes in a song and identifying the instrument playing those notes, having achieved 100.0% accuracy on this dataset. However, the robustness of this method remains to be tested fully.

The two instruments used in this dataset, a flute and a piano, are very distinct instruments, especially with regards to the duration of their notes. The flute requires constant pressure to produce sound, and diminishes very quickly when that pressure is absent. The piano requires a note to be struck only once to produce that sound cannot be sustained and diminishes much more slowly than a flute.

Future work will aim to address this issue by including different instruments, such as the guitar, which are more similar to either instrument and will likely require more precise features that are unique to instruments. Along this vein, I plan to investigate the effects of other features on classifier accuracy, such Mel Frequency Cepstral Coefficients. With the inclusion of different instruments and more features, it is possible that a more complex classifier will be needed, such as a Convoluted Neural Network.

Another issue that needs to be addressed is the automation of determining the number of notes present in each 5-second interval. One possible method that could be used to solve this is Singular Value Decomposition.

