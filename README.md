# AudioHit

-----

## About 

AudioHit is a CLI written in Rust and designed to help batch process audio samples in many different ways to make it easier to use them in software and hardware samplers. 

Available modes:

- **trim** (Default) : Detects the start and end points of the main content of the audio sample based on amplitude (whatever is above a certain threshold), trims the audio file to those start / end points, and apply a linear fade in/out. This mode can perform a simple silent strip to split recording of multiple one-shots into separate files (with fades). 
- **scale** : Generates 12 copies of the original sample and transposes each of them by +1 semitone.
- **ref** : Generates a stereo file that has the original sample on the left side and a reference tone (tuned to C3) on the right side. This mode is designed to make it easier to record samples to tape machines and re-tune (using the tape machine's speed control) as the right channel can be used as a pitch reference. 

AudioHit also implements the [ot_utils](https://github.com/icaroferre/ot_utils) library to make it easier to concatenate the generated samples into a single .wav file (sample chain) and automatically generate a .ot slice file for the Elektron Octatrack.

## Limitations

- AudioHit can only process 44.1kHz 16-bit mono wave files (but it auto-converts files in different formats to save time).

## How to Install / Dependencies

If you're running **macOS**: 

- [Download the latest release version](https://github.com/icaroferre/AudioHit/releases). 
- Move the binary file to a safe location.
- Create an alias for AudioHit: open the Terminal, type ```alias audiohit=```, drag and drop the binary file on top of the Terminal window and press enter.

If you're running other OS, you'll need to install the latest version of Rust + Cargo and compile the code for your own system (cd into the folder and ```cargo build --release```).

AudioHit uses [Sox](http://sox.sourceforge.net) to convert audio files to different formats in high quality therefore make sure you have SoX installed on your system. If you're on macOS, I recommend installing it via [HomeBrew](https://brewinstall.org/Install-sox-on-Mac-with-Brew/).  
It is possible to use AudioHit without SoX by disabling the automatic file convertion using the ```--autoconvert false``` argument (only in ```trim``` mode).

## How to Use

To use AudioHit, open the terminal and use the ```audiohit``` command to process files or folder.
 
The new files will placed inside an output folder (which will be created if it doesn't exist).

### Arguments
	
- ```--file```: process a single file.
- ```--folder```: batch process an entire folder (sub-folders found in the specified --folder argument will also be processed).
- ```--mode```: sets the main AudioHit processing mode (trim / scale / ref / pass).
- ```--autoconvert```: enables or disables automatic file conversion (via SoX).

**Arguments for trim mode:**

- ```--split true``` : Splits processed file into multiple files based on the threshold (minimum length of 250ms per file). 
- ```--fadein``` : Sets the length of the fade in in **ms**.
- ```--fadeout``` : Sets the length of the fade out in **ms**.
- ```--thresh``` : Sets the threshold for the peak detector in **decibels** (default: -48).
- ```--speedup``` : Speeds up audio files by x times the original speed.
- ```--slowdown``` : Slows down audio files to 1/x the original speed.
- ```--reducesr``` : Divides the sample rate of audio files by x (note that this will only affect the samples themselves and will not change the sample rate of the generated .wav file).

**Arguments for trim mode:**

- ```--pitch_range``` : Sets the pitch range / number of files to be generated (default: 12).
- ```--pitch_offset``` : Sets the pitch offset (root note) of the original sample (default: 0).

**Arguments for Octatrack .ot files:**

- ```--ot_file true``` : Automatically concatenate all the files generated by audiohit and generate a .ot slice file for the Elektron Octatrack (via my [ot_utils](https://github.com/icaroferre/ot_utils) Rust library). The final file will be generated to the output folder and will both .wav and .ot files will have the same name.  If more than 64 samples are found within that folder then multiple OT files will be created, each containing 64 samples / slices. 
- ```--ot_random true```:  generates a single chain (concatenated wav + ot file for the Octatrack) using 64 samples randomly picked from the folder specified in the ```--folder``` argument. AudioHit will make sure not to pick the same file more than once.
- ```--ot_evenspace true``` : Ensures that samples added to the concatenated .wav file are evenly spaced (based on the length of longest file). 

## Example workflows

• Auto-convert, trim files and create a sample chain for the Octatrack:  
```audiohit --ot_file true --folder path/to/folder``` 

• Split recording of multiple one-shots, trim and fade files, and create a sample chain for the Octatrack:  
```audiohit --split true --ot_file true --file path/to/file.wav``` 

• Create 48 semitone (4 octaves) variations of a sample starting 2 octaves below:  
```audiohit --mode scale --pitch_range 48 --pitch_offset 24 --file path/to/file.wav```  

• Trim files, speed them up 4x, and create a sample chain for the Octatrack (useful for saving RAM on hardware samplers):  
```audiohit --speedup 4 --ot_file true --folder path/to/folder```

• Add reference tone to audio file:  
```audiohit --mode ref --file path/to/file.wav``` 

• Create Octatrack sample chain by selecting 64 random files from folder (without trimming):  
```audiohit --mode pass --ot_file true --ot_random 64 --folder path/to/folder```  

 
## Roadmap / To-Do

- Normalization option to ```trim``` mode.
- Support for stereo files.
- Support for 24 and 32-bit audio files.
- Support for creating .WAV and PADINFO.BIN files for the Roland SP-404.
- Built-in workflows.

## Compiling instructions for different platforms

While a compiled binary for macOS is available on the [Releases page](https://github.com/icaroferre/AudioHit/releases), compiling binaries for different platforms is quite easy with Rust.

Here's a step by step instruction:

1 - [Download the zip file for this repository](https://github.com/icaroferre/AudioHit/archive/master.zip) and unzip it

2 - [Install Rust on your device](https://www.rust-lang.org/tools/install)

3 - Open the terminal and navegate to the the code folder of the repository: ```cd path/to/AudioHit-master/code/```. If you're unsure about the path just enter ```cd ``` and drag and drop the code folder on the terminal window.

4 - Use the command ```cargo build --release``` to build a binary for your system. The cargo system will take care of downloading all the necessary dependencies and compile everything automatically. The final binary will be available at the target / release folder.


----

Created by Ícaro Ferre  
@icaroferre  
[spektroaudio.com](http://spektroaudio.com)
 
 