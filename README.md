

## 🚀 Features

- [x] Creates standard format M4B audiobook file
- [x] Automatic chapter break detection
- [x] Embeds cover art if specified
- [x] Can use MS Edge for free cloud-based TTS
- [x] Easy voice cloning with Coqui XTTS model
- [x] 58 studio quality voices from Coqui AI
- [x] Uses deepspeed if available for faster processing
- [x] Resumes where it left off if interrupted
- [x] NOTE: epub file must be DRM-free



## 📖 Usage
<details>
<summary> Usage instructions</summary>

## Extract epub contents to text:
1. `epub2tts mybook.epub --export txt`
2. **edit mybook.txt**, replacing `# Part 1` etc with desired chapter names, and removing front matter like table of contents and anything else you do not want read. **Note:** First two lines can be Title: and Author: to use that in audiobook metadata.
3. The speaker can be set to change per chapter by appending `% <speaker>` after the chapter name, for instance `# Chapter One % en-US-AvaMultilingualNeural`. See the file `multi-speaker-sample-edge.txt` for an example. **Note:** Only works with Coqui TTS multi-speaker engine (default) or `--engine edge`.

## Default audiobook, fairly quick:
Using VITS model, all defaults, no GPU required:

* `epub2tts mybook.epub` (To change speaker (ex p307 for a good male voice w/Coqui TTS), add: `--speaker p307`)

## MS Edge Cloud TTS:
Uses [Microsoft Edge TTS](https://github.com/rany2/edge-tts/) in the cloud, FREE, only minimal CPU required, and it's pretty fast (100 minutes for 7hr book for instance). Many voices and languages to choose from, and the quality is really good (listen to `sample-en-US-AvaNeural-edge.m4b` for an example).

* List available voices with `edge-tts --list-voices`, default speaker is `en-US-AndrewNeural` if `--speaker` is not specified.
* `epub2tts mybook.txt --engine edge --speaker en-US-AvaNeural --cover cover-image.jpg --sayparts`

## XTTS with Coqui Studio voice:
1. Choose a studio voice, [samples here](https://github.com/rejuce/CoquiTTS_XTTS_Examples)
2. `epub2tts mybook.txt --engine xtts --speaker "Damien Black" --cover cover-image.jpg --sayparts`

## XTTS using your own voice clone:
1. `epub2tts mybook.epub --scan`, determine which part to start and end on so you can skip TOC, etc.
2. Secure 1-3 30 second clips of a speaker you really like (`voice-1.wav``, etc)
3. `epub2tts my-book.epub --start 4 --end 20 --xtts voice-1.wav,voice-2.wav,voice-3.wav --cover cover-image.jpg`

## All options
* -h, --help - show this help message and exit
* --engine [ENGINE] - Which TTS engine to use [tts|xtts|openai|edge]
* --xtts [sample-1.wav,sample-2.wav] - Sample wave/mp3 file(s) for XTTS v2 training separated by commas
* --openai OPENAI_API_KEY - OpenAI API key if engine is OpenAI
* --model [MODEL] - TTS model to use, default: tts_models/en/vctk/vits
* --speaker SPEAKER - Speaker to use (examples: p335 for VITS, onyx for OpenAI, "Damien Black" for XTTS v2, en-US-EricNeural for edge)
* --scan - Scan the epub to show beginning of chapters, then exit
* --start [START] - Chapter/part to start from
* --end [END] - Chapter/part to end with
* --language [LANGUAGE] - Language of the epub, default: en
* --minratio [MINRATIO] - Minimum match ratio between text and transcript, 0 to disable whisper
* --skiplinks - Skip reading any HTML links
* --skipfootnotes - Try to skip reading footnotes
* --skip-cleanup - Do not replace special characters with ","
* --sayparts - Say each part number at start of section
* --bitrate [BITRATE] - Specify bitrate for output file
* --debug  - Enable debug output
* --export txt - Export epub contents to file (txt, md coming soon)
* --parapause - when using `--export txt`, this option inserts `%P%` at each paragraph break. Then when creating audio with `--engine edge`, any time `%P%` is found in the copy a 1.2 second pause in inserted. 
* --no-deepspeed - Disable deepspeed
* --cover image.jpg - jpg image to use for cover


```
</details>

## 📦 Install

Required Python version is 3.11.

<details>
<summary>MAC INSTALLATION</summary>

This installation requires Python < 3.12 and [Homebrew](https://brew.sh/) (I use homebrew to install espeak, [pyenv](https://stackoverflow.com/questions/36968425/how-can-i-install-multiple-versions-of-python-on-latest-os-x-and-use-them-in-par) and ffmpeg). Per [this bug](https://github.com/coqui-ai/TTS/issues/2052), mecab should also be installed via homebrew.

Voice models will be saved locally in `~/.local/share/tts`
```
#install dependencies
brew install espeak pyenv ffmpeg mecab
#install epub2tts
git clone https://github.com/aedocw/epub2tts
cd epub2tts
pyenv install 3.11
pyenv local 3.11
#OPTIONAL - install this in a virtual environment
python -m venv .venv && source .venv/bin/activate
pip install .
```
</details>

<details>
<summary>LINUX INSTALLATION</summary>

These instructions are for Ubuntu 22.04 (20.04 showed some depedency issues), but should work (with appropriate package installer mods) for just about any repo. Ensure you have `ffmpeg` installed before use. If you have an NVIDIA GPU you should also [install CUDA toolkit](https://developer.nvidia.com/cuda-downloads) to make use of deepspeed.

Voice models will be saved locally in `~/.local/share/tts`

```
#install dependencies
sudo apt install espeak-ng ffmpeg
#If you have a CUDA-compatible GPU, run:
sudo apt install nvidia-cuda-toolkit
#clone the repo
git clone https://github.com/aedocw/epub2tts
cd epub2tts
pip install .
```

**NOTE:** If you have deepspeed installed, it may be detected but not work properly, causing errors. Try [installing CUDA toolkit](https://developer.nvidia.com/cuda-downloads) to see if that resolves the issue. If that does not fix it, add `--no-deepspeed` and it will not be used. Also in that case, open an issue with your details and we will look into it.

</details>

<details>
<summary>WINDOWS INSTALLATION</summary>

Runnig epub2tts in WSL2 with Ubuntu 22 is the easiest approach, but these steps should work for running directly in windows.

1. Install Microsoft C++ Build Tools. Download the installer from https://visualstudio.microsoft.com/visual-cpp-build-tools/ then run the downloaded file `vs_BuildTools.exe` and select the "C++ Buld tools" checkbox leaving all options at their default value. **Note:** This will require about 7 GB of space on C drive.
2. Install espeak-ng from https://github.com/espeak-ng/espeak-ng/releases/latest
3. [Install chocolaty](https://chocolatey.org/install)
4. Install ffmpeg with the command `choco install ffmpeg`, make sure you are in an elevated powershell session.
5. Install python 3.11 with the command `choco install python311`
6. Install git with the command `choco install git`.
7. Decide where you want your epub2tts project to live, documents is a common place. Once you've found a directory you're happy with, clone the project with `git clone https://github.com/aedocw/epub2tts` and cd epub2tts so you're now in your working directory.
8. There are probably a few different ways you can go here, I personally opted for a venv to keep everything organized. Create a venv with the command `python -m venv .venv`
9. Activate the venv, on windows the command is slightly different as you issue `.venv\scripts\activate`
10. Install epub2tts along with the requirements with the command `pip install .`

11. If all goes well, you should be able to call epub2tts from within your venv and update it from this directory going forward. To update, use `git pull` and then `pip install . --upgrade`


