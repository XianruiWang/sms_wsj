# SMS-WSJ: A database for in-depth analysis of multi-channel source separation algorithms

![Example ID](doc/images/room.svg)

At the moment this repository is still under construction

This repository includes the scripts required to create the sms_wsj database
and a baseline asr system using KALDI (http://github.com/kaldi-asr/kaldi).

If you are using this code please cite the following paper:

```
@Article{SmsWsj19,
  author    = {Lukas Drude, Jens Heitkaemper, Christoph Boeddeker, Reinhold Haeb-Umbach},
  title     = {{SMS-WSJ}: Database, performance measures, and baseline recipe for multi-channel source separation and recognition},
  year      = {2019},
}
```

## Installation

Does not work with Windows.

Clone this Repo and install the package 
```bash
$ git clone https://github.com/fgnt/sms_wsj.git
$ cd sms_wsj
$ pip install --user -e ./
```

Get the RIR by downloading them (recommended)
```bash

```
To generate them yourself see [here](#q:-i-want-to-generate-the-rirs-myself.-How-can-I-do-that?).

Then set your KALDI_ROOT
```bash
$ export KALDI_ROOT=/path/to/kaldi
```
We assume that the KALDI wsj baseline has been created with the `run.sh` script.
To build the database the structures created during the first stage of
the `run.sh` script are required.
The ASR baseline uses the language models created during the same stage.
Afterwards you can create the database:
```bash
$ make SMS_WSJ_DIR=/path/to/write/db/to WSJ_DIR=/path/to/wsj
```
If desired the number of parallel jobs may be specified using the additonal
input num_jobs. Per default 16 parallel jobs are used.

Use the following command to train the baseline asr model:
```bash
$ python -m sms_wsj.train_baseline_asr with egs_path=$KALDI_ROOT/egs/ json_path=/path/to/sms_wsj.json
```
The script has been tested with the KALDI hash "7637de77e0a77bf280bef9bf484e4f37c4eb9475"


## FAQ
### Q: What does the example ID `0_4k6c0303_4k4c0319` mean?
A: The example ID is a unique identifier for an example (sometime also known as utterance ID).
The example ID is a composition of the sperakers, the utterances and an scenario counter:

![Example ID](doc/images/example_id.svg)
=======

### Q: What to do if kaldi uses python3 instead of python2?
The python code in this repository requires python 3.6. However, Kaldi runs
on python 2.7. To solve this mismatch Kaldi has to be forced to switch the
python version using the path.sh. Therefore, add the follwing line to
the `${KALDI_ROOT}/tools/envh.sh` file:
```
export PATH=path/to/your/python2/bin/:${PATH}
```

### Q: I want to generate the RIRs myself. How can I do that?
To generate the RIRs you can run the following command:
```bash
$ mpiexec -np $(nproc --all) python -m sms_wsj.database.create_rirs with database_path=cache/rirs
```
The expected runtime will be around `1900/(ncpus - 1)` hours.
When you have access to an HPC system, you can replace `mpiexec -np $(nproc --all)` with an HPC command.
It is enough, when each job has access to 2GB RAM.
