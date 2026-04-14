## Ovenbird song recordings from Alberta with individual labels and spatial locations

Creators: Sam Lapp [1], Scott Wilson [2], Chapin Czarnecki [1], Gregroire, Jocelyn [2], Erin Bayne [2], Justin Kitzes [1]

Affiliations: [1] University of Pittsburgh, [2] University of Alberta borial avian monitoring project

Contact: Sam Lapp, sam.lapp@pitt.edu

Version number: v2.0

Date updated: April 13 2026

DOI: https://doi.org/10.6073/pasta/bd39cd5d5c8fcaad64d402e7c20d12b1

Package ID: edi.2049.1

### General characteristics
Audio_format: short (10 second clips centered on 3 second localized events)

Dimensions localized: 2

Number of localization arrays: 13

Array geometry: 5x10 square grid with 33m spacing

Sounds localized: Ovenbird (Seiurus aurocapilla) songs

Number of localization arrays: 13

Number of audio files: 4025

Size: 0.375 GB

> Warning: audio clips are 10 seconds, of which seconds 3.5-6.5 contain 3-second clip with the localized Ovenbird song. The longer clip is provided for flexibility in other analyses of these files. 

## Study description

This dataset includes spatially localized and individually identified Ovenbird songs 

We used autoamted sepecies detection and acoustic localization to localize Ovenbird singing events from microphone arrays in Alberta, Canada. We then hand-annotated songs to individuals based on acoustic characteristics.

This dataset includes the manual annotations and annotations from automated individual identification approaches. 

See Lapp et al. [1] manuscript on Ovenbird individual identification for further details on the study and the individual identification approach.

Personnel: Wilson and Gregroire collected the data, which was published in Wilson and Bayne 2018 [2]. Lapp created this dataset of localized sounds. Czarnecki and Lapp annotated Ovenbird songs to individuals. 


## Files

- localized_events.csv: table with a row describing each acoustically localized sound 
	Columns:
    - event_id: unique identifier within the dataset
    - label: individual identification label (integer) of Ovenbird, produced by two human annotators. Each number represents one individual.
        - note that ID is based on acoustic characteristics, not on visual or capture data
    - start_timestamp: onset time of the event in ISO format 
    - duration: event length in seconds
    - position: localized position coordinate in meters: (utm x, utm y,elevation, utm zone)
        - elevation is NaN since we localize in 2 dimensions
        - note that the arrays are not all in the same UTM Zone
    - file_ids: list of file_id  for audio clips that participated in the localization; matches file_id column of `audio_file_table.csv`
    - file_start_time_offsets: time in seconds from start of each audio file to the the start of the clip 
    - aiid_label_sl: original label from first annotator before label resolution
    - notes_sl: notes from first annotator
    - aiid_label_cc: original label from second annotator before resolution
    - data_split: 'val' if part of validation set, or 'test' if part of test set
    - ovenbird_cluster_labels: automated individual ID label produced using custom Ovenbird feature extractor
    - baseline_resnet18_cluster_labels: automated individual ID label produced using resnet18 trained on ImageNet as feature extractor
    - hawkears_cluster_labels: automated individual ID label produced using HawkEars v0.1.0 as feature extractor
    - birdnet_cluster_labels: automated individual ID label produced using BirdNET as feature extractor
    - mic_distances_m: 2D horizontal distance in meters from the microphone to the estimated bird position, for each file in file_ids list
    - relative_position: position in meters (Easting, Northing) relative to the Western-most coordinate from any microphone in the array and  Southern-most cordinate from any microphone in the array

- labeled_clips.csv: this file contains similar information to localized_events.csv in a different format: instead of each localized event having a single row (corresponding to multiple audio clips that recorded the event), each audio clip has a single row in this table. Noe that bird_position_x and and bird_position_y columns correspond to the `relative_position` from localized_events.csv: that is, the Easting and Northing position of the song location relative to the Western-most coordinate of a recorder on the grid, and the Southern-most position of a recorder on the grid. 

- /localization_metadata/audio_file_table.csv: lists all audio clips and the associated point_id in point_table.csv where the file was recorded. rel_path column provides the relative path from the top level folder of this dataset. file_id is the filename and is unique, and matches values in localized_events.csv file_ids column (which contains a list of file_id participating in the localized event)

- /localization_metadata/point_table.csv: positions of each microphone position across all arrays. 'array' column lists array. 'mic_type' is ARU if on-board aru mic, or 'EXTMIC' if it was the external microphone wired to a SongMeter. Notes are from Scott Wilson's original metadata and describe post-processing of some points. 

file_table and point_table can be merged like this, assuming they are lodaed as pandas DataFrames:
```python
file_table.set_index("point_id").drop(columns="array").join(
    point_table.set_index("point_id")
).reset_index(drop=False)
```
> Note: Since SM3 recorders sync on-board, no post processing was required to sync or resample audio for accurate temporal alignment

> Note: neither the original, full audio dataset analyzed by these scripts, nor their intermediate outputs, are not inluded in this repository, scripts are only inlcuded for reference. 

- /script/1_detect_species_HawkEars.py: python script using the Bioacoustics Model Zoo and the HawkEars bird species CNN classifier to detect Ovenbird primary song ("teacher" song) in audio. Also detects >300 other species.

- /script/2_select_detections_to_localize.ipynb: uses the output of Hawkears detector to select Ovenbird song detections for localization

- /script/3_localize_ovenbird.ipynb: python notebook using OpenSoundscape, used to perform acoustic localization on the selected detections

- /script/4_automated_minspec_review.py: performs an automated check of consistency for the localized events

- /script/5_remove_duplicate_events.py: check for and remove localized events from the same 3-second window and within 10m of each other

- /script/6_select_clips_to_annotate.ipynb: randomly select from all localized clips a subset to annotate

- /script/7_annotate_localized_songs.ipynb: visualize spectrograms of songs to label to indivdiual level, and provide 1/4 speed playback

- /script/python_environment.yml: conda enviornment file; to re-create Python environment used here, run `conda create -f python_environment.yml`


- /audio/: contains audio clips for localized Ovenbird songs. 

Each subfolder in /audio/ contains audio from one recorder array. Within these folders, each second-level subfolder contains the clips associated with one individual Ovenbird. For each microphone used to localize each singing event, a 10-second clip centered on the 3-second localized window is included in this dataset. The time period from 3.5-6.5 seconds of each clip is the 3-second clip that containing the localized Ovenbird song. Other individuals may occur elsewhere in the 10-second clip. 

## Sites

Localization arrays were distributed across Alberta, Canada, typically in boreal forest ecosystems. See Wilson and Bayne 2018 [2] for details. 

## Hardware

- Recorder source: Wildlife Acoustics
- Recorder model: SongMeter 3 with additional wired external microphone recording to 2nd channel
- Firmware version: N/A

## Recording properties

Date range of data: vary by array, 1-5 days per array

### Recording schedule description:
- Times of recording: 05:00-08:30 local time
- sleep-wake schedule: 29 minutes starting every half-hour
- Sample rate: 48000 Hz

The gain setting was 19.5 dB. Combined with a microphone sensitivity of the SMM A1 microphone of  -11 dB (V/Pa) and an ADC with .775V rms = 0 dBFS (=1V peak), the expected end-to-end sensitivity is 8.5 dBFS @ 1 Pa (=94dB SPL re 20 uPa), in other words dB SPL re 20 uPA = dBFS + 85.5 dB. 

### Data aggregation notes: 

We excluded files in the original audio dataset from analysis if they were not aligned with the general recording schedule of 29 minutes starting every half-hour. One grid is missing data from one recorder, and has 49 recorders. All others have 50 recorders. Note that the original dataset contained more than the 13 arrays included in this dataset, we only analyze a subset of 13 here. 

## Recorder positioning

### Placement
Spatial pattern or geometry: 10x5 square grid of recorders
Range of spacing between adjacent mics: avg 33m
Dimensions of array: approximately 130x300

The arrangmenet of microphones was 5x10 in general, though some arrays have fewer than 50. They used 25 SM3 ARUs with an external mic + cable recording on the second channel to effectively record at two points with a single SM3. The SM3 with GPS capability self-synchronizes the audio recordings for precise sample rate.

### Deployment:
Recorders deployed for 1-5 days per array. Each SongMeter recorded with on-board microphone at one point, ad with an external wired microphone at a different point. The stereo recordings were then split into separate mono .wav files. 


### Position measurement
Measured position with RTK GPS when possible, otherwise taken with handheld GPS. See Wilson and Bayne 2018 [2] for details. 

## Synchronization
Synchronization type: GPS, on-board SongMeter synchronization

Recording start/end time trimming: Recordings were not trimmed to the same start/end time


## Sound detection:

This dataset only contains detections of Ovenbird (Seiurus aurocapilla) primary (ie "teacher") song

Detection strategy: convolutional neural network

Detector name and version: HawkEars v0.1.0, using bioacoustics model zoo v0.11.0

Details on the species detection method: see Huus et al. 2025 [3]

Post-processing detector outputs: 
    - threshold score of -1.0 for Ovenbird class to detect Ovenbird clips
    - only clips with detection were included in localization process

Scripts/resources: detect_ovenbird.py

## Localization:
Tools/packages used for localization: OpenSoundscape v0.11.0
Localization algorithm: GCC-PHAT (OpenSoundscape v0.11.0 default)
Time delay calculation algorithm: "soundfinder"[4] (OpenSoundscape v0.11.0 default)

Error rejection parameters: 
min_n_receivers = 5 (minimum number of microphones used to localize song)
max_receiver_dist = 60 (max distance in meters from a reference microphone to microphones used)
cc_threshold = 0.01  # cross correlation threshold, if cc below this the tdoa is not used for localization
Scripts: script/3_localize_ovenbird.py

Other customizations to the localization approach:
Audio was bandpassed to 2-10 kHz before cross correlation for time difference of arrival (tdoa) estimation
We performed an automated error-rejection post processing procedure (4_automated_minspec_review.py), see Lapp et al. manuscript[1] for details. 

### Manual review:
We reviewed clips to ensure Ovenbird song was present and to apply an individual bird label to each song. We manually viewed spectrorams and listened to audio at 1/4 speed to facilitate individual identification. Two reviewers independently reviewed each clip. Annotator agreement rate was 98%. We then resolved conflicts by fixing those due to typing errors and discussing remaining conflicts. 

### Change log

#### V1.3 to v2.0: 
Updated Labels in April 2026
After comparing spatial clusters with song-based labels, we revised the labels for 6 of 717 singing events, creating two new individual labels (bringing the total number of individuals from 45 to 47).
We also removed two audio clips, 1251_oven13.mp3 and 1253_oven13.mp3, because during our additional review process we noted these clips did not contain Ovenbird songs. 

## Observational data
None

## Acknowledgements
Wilson and Gregroire conducted the data collection. Lapp analyzed the acoustic data. Sam Lapp and Chapin Czarnecki annotated songs to individual level. Sam Lapp curated the dataset and repository. Erin Bayne and Justin Kitzes provided mentorship. 

## License
CC-BY "by attribution"  https://creativecommons.org/

## Work Cited and Links

[1] Lapp, S., et al. in review. "Automated identification of individual birds by song enables multi-year individual recapture from passive acoustic monitoring data". 
[2] S. Wilson and E. Bayne, "Use of an acoustic location system to understand how presence of conspecifics and canopy cover influence Ovenbird (<em>Seiurus aurocapilla</em>) space use near reclaimed wellsites in the boreal forest of Alberta," Avian Conservation and Ecology, vol. 13, no. 2, Aug. 2018, doi: 10.5751/ACE-01248-130204.
[3] J. Huus, K. G. Kelly, E. M. Bayne, and E. C. Knight, “HawkEars: A regional, high-performance avian acoustic classifier,” Ecological Informatics, vol. 87, p. 103122, Jul. 2025, doi: 10.1016/j.ecoinf.2025.103122.
[4] Wilson, David R., Matthew Battiston, John Brzustowski, and Daniel J. Mennill. "Sound Finder: A New Software Approach for Localizing Animals Recorded with a Microphone Array." Bioacoustics 23, no. 2 (May 4, 2014): 99–112. https://doi.org/10.1080/09524622.2013.827588.