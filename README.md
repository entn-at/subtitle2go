# subtitle2go

## Changelog

### 24.10.2020
+ Added punctuation model (with punctuator2)
+ Added a better subtitle segmentation algorithm

## Requirements
+ Ubuntu 18.04
+ Python 3.7

## Installguide
```
#make sure you have Python 3.7 installed and also its dev package:
sudo apt-get install python3.7 python3.7-dev

# Now clone the subtitle2go package somwhere:
mkdir ~/projects/
cd ~/projects/
git clone https://github.com/uhh-lt/subtitle2go
cd subtitle2go/

# create virtual env and install python dependencies:

virtualenv -p /usr/bin/python3.7 subtitle2go_env
source subtitle2go_env/bin/activate
pip install numpy pyyaml ffmpeg-python theano spacy
python -m spacy download de

# Now install PyKaldi
wget http://ltdata1.informatik.uni-hamburg.de/pykaldi/pykaldi-0.1.2-cp37-cp37m-linux_x86_64.whl
pip install pykaldi-0.1.2-cp37-cp37m-linux_x86_64.whl

# Install Kaldi and Intel MKL (see note below if you have a different CPU than Intel)

./install_mkl.sh
./install_kaldi_intel.sh ~/projects/subtitle2go/subtitle2go_env/bin/python3

#OR if you have a non-Intel CPU:
#install_kaldi.sh ~/projects/subtitle2go/subtitle2go_env/bin/python3

# Install punctuator2 for automatic punctuation
git clone https://github.com/ottokart/punctuator2.git

# Patch punctuator2:

Open punctuator2/models.py in a file editor, go to line 54 and replace "from . import models" with "import models"

# Download pretrained models:
./download_models.sh
```
Put a mediafile (eg `mediafile.mp4`) in the directory and then run:

```
source subtitle2go_env/bin/activate
. path.sh
python subtitle2go.py mediafile.mp4
```

The subtitle is then generated as `mediafile.vtt`

The following options are available:

```
usage: subtitle2go.py [-h] [-s {vtt,srt}] [--asr-beam-size ASR_BEAM_SIZE]
                           [--asr-max-active ASR_MAX_ACTIVE]
                           [--segment-beam-size SEGMENT_BEAM_SIZE]
                           [--ideal-token-len IDEAL_TOKEN_LEN]
                           [--len-reward-factor LEN_REWARD_FACTOR]
                           [--sentence-end-reward_factor SENTENCE_END_REWARD_FACTOR]
                           [--comma-end-reward-factor COMMA_END_REWARD_FACTOR]
                           filename

positional arguments:
  filename              The path of the mediafile

optional arguments:
  -h, --help            show this help message and exit
  -s {vtt,srt}, --subtitle {vtt,srt}
                        The output subtitleformat (vtt or srt). Default=vtt
  --asr-beam-size ASR_BEAM_SIZE
                        ASR decoder option: controls the beam size in the beam
                        search. This is a speed / accuracy tradeoff.
  --asr-max-active ASR_MAX_ACTIVE
                        ASR decoder option: controls the maximum number of
                        states that can be active at one time.
  --segment-beam-size SEGMENT_BEAM_SIZE
                        What beam size to use for the segmentation search
  --ideal-token-len IDEAL_TOKEN_LEN
                        The ideal length of tokens per segment
  --len-reward-factor LEN_REWARD_FACTOR
                        How important it is to be close to ideal_token_len,
                        higher factor = splits are closer to ideal_token_len
  --sentence-end-reward_factor SENTENCE_END_REWARD_FACTOR
                        The weight of the sentence end score in the search.
                        Higher values make it more likely to always split at
                        sentence end.
  --comma-end-reward-factor COMMA_END_REWARD_FACTOR
                        The weight of the comma end score in the search.
                        Higher values make it more likely to always split at
                        commas.
```

## FAQ

### Error message ImportError: libkaldi-base.so: cannot open shared object file: No such file or directory

You have to load the path.sh into your shell. You need to run:

. path.sh

Every time you start a new shell session in order to use subtitle2go

### Error message "had nonzero return status 32512"
The path of kaldi is missing or incorrect
Use
```
export KALDI_ROOT=PATH_TO_KALDI
export PATH=$KALDI_ROOT/src/lmbin/:$KALDI_ROOT/../kaldi_lm/:$PWD/utils/:$KALDI_ROOT/src/bin:$KALDI_ROOT/tools/openfst/bin:$KALDI_ROOT/src/fstbin/:$KALDI_ROOT/src/gmmbin/:$KALDI_ROOT/src/featbin/:$KALDI_ROOT/src/lm/:$KALDI_ROOT/src/sgmmbin/:$KALDI_ROOT/src/sgmm2bin/:$KALDI_ROOT/src/fgmmbin/:$KALDI_ROOT/src/latbin/:$KALDI_ROOT/src/nnetbin:$KALDI_ROOT/src/nnet2bin/:$KALDI_ROOT/src/online2bin/:$KALDI_ROOT/src/ivectorbin/:$KALDI_ROOT/src/kwsbin:$KALDI_ROOT/src/nnet3bin:$KALDI_ROOT/src/chainbin:$KALDI_ROOT/tools/sph2pipe_v2.5/:$KALDI_ROOT/src/rnnlmbin:$PWD:$PATH
```
and replace PATH_TO_KALDI with the path to your kaldi binaries (eg. pykaldi/tools/kaldi)
