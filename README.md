# Zooniverse Pipeline for Long Format Recordings

A pipeline to create, upload and analyse long format recordings using the Zooniverse citizen science platform.

We have an open project aimed at adding vocal maturity labels to segments LENA labeled as being key child in Zooniverse (https://www.zooniverse.org/projects/chiarasemenzin/maturity-of-baby-sounds).

If you would like your data labeled, here is what you'd need to do.
1. Get in touch with us, so we know you are interested! (authors' contact information at the bottom of this README)
2. Have someone trustworthy & with some coding skills (henceforth, the RA) process your LENA data using these instructions.
3. When you are done, this will have generated a meta-data file, containing the linkage between the original .its and the ~250 500-ms clips/child. Keep the metadata safe - without it, you won't be able to interpret your results!
4. Have the RA create an account on Zooniverse (top right of zooniverse.org) for them and yourself, & provide us with both handles. This person should first update the team section to add you (have ready a picture and a blurb). They can also add your institution's logo if you'd like. Both of these are done in the [lab section](https://www.zooniverse.org/lab/10073)
5. They will then follow the instructions in this README to create subjects and push up your data -- see below.
6. We also ask the RA to pitch in and help answer questions in the [forum](https://www.zooniverse.org/projects/chiarasemenzin/maturity-of-baby-sounds/talk), at least one comment a day. 
7. You can visit the [stats section](https://www.zooniverse.org/projects/chiarasemenzin/maturity-of-baby-sounds/stats) to look at how many annotations are being done.
Since the process of data generation takes up Zooniverse resources, we'll manage requests and try to not make very many in succession. **Please do NOT ask for data to be generated.**
8. When we get data back (every month or so), we'll update it in an OSF repo and let you know. We provide code to derive key analyses, which involve using your meta-data to remove data belonging to other people.

## Description

This project comprises three folders:
* /create_subjects contains scripts to segment daylong recordings into small chunks (500ms) and write a metadata file.
* /upload_subjects contains a script to convert and upload data on the zooniverse platform, using the [Panoptes API](https://panoptes-python-client.readthedocs.io/en/v1.0/user_guide.html).
* /zoon_analysis contains scripts to convert Zooniverse data to json format and perform analysis in R.

This README assumes you know your way around a Terminal. If you don't, follow a tutorial, e.g. https://swcarpentry.github.io/shell-novice/

## Prerequisites

Minimum requirements: 1.6 gHz intel core (dual) 4GB 1600 MHz DDR3; 30GB of memory

* Python (preferably 3.7)

Python packages:

* lxml
* pandas
* pydub

all packages can be installed with pip:
```
pip install pydub
```


## Getting started

Clone or download the repository on your local machine by running:
```
$ git clone https://github.com/psilonpneuma/Zooniverse.git
```

## Setting up the working environment


Open the configuration file `config.py` that you will find at the top level of this repository in any text editor (e.g., Sublime Text), and specify the local paths:
* "python": python interpreter. You can find which version of python you're running by typing `python -V` on your terminal.  If you only have one version of python and it's Python 3, you can write `"python":"python"`. If you have several, including python3, you may be able to call it with `"python":"python3"`. If using a virtual env, enter the path to the environment Python executable (e.g. `/home/attie/projects/thing/venv/bin/python3`).
* "working_dir": this is the present working directory, created when you cloned the repo, and where the `create_subjects` scripts are contained
* "infolder": the folder with its and wav files. Make sure corresponding its and wav files are named in the same way, e.g. if the .its file is called e1234.its, the .wav file should be called e1234.wav
* "outfolder": this is the folder where you want this script to extract final clips and the metadata.
* "metadata_fn": name of the metadata file; choose anything you'd like since it will be created in the process
* "batch_name": name that will be used to label the batch during uploading, choose anything you want as long as there are no spaces and it is unique (i.e., it will not have been used by you or any others in the past). NOTE! You cannot use the same name twice (i.e., for two subsequent uploads).

See the configuration file for examples. Don't forget to put a "/" at the end of each path, and make sure the path does not include spaces.


## Executing our scripts

We foresee three phases and explain each in turn.

### 1. Creating files

First, short clips need to be created from the long recording files.

You may need to navigate to the relevant folder by doing:
```
cd Zooniverse/create_subjects/
```

Then simply run a command like the following to get started:

```
python pipeline.py
```

The anonymized clips and a metadata file with clip name, child ID, age and the original recording name will be saved in the directory specified in the `outfolder`.


### 2. Upload subjects 

Warning: Although there is in principle no limit to the number of long files that can be processed in the first stage, the Zooniverse platform guidelines recommend uploading 1000 files or less at a time. 

For this reason, the creation of clips and their upload are kept here as two separate stages. As a result, you might need to run the steps in this section more than once, separating your clips into batches of 1,000 files, and changing the batch name in your config.py file each time.

First, navigate to the relevant folder by doing:
```
cd Zooniverse/upload_subjects/
```

Or, if you just created your subjects with the instructions above, then do:
```
cd ../upload_subjects/
```

Next convert your clips to mp3 format by running:
```
$ ./convert_2_mp3.sh
```
if you encounter a permission error, run:
```
$ chmod +r convert_2_mp3.sh
$ ./convert_2_mp3.sh
```

If that still doesn't work, try
```
$ bash convert_2_mp3.sh
```

Once your files are in mp3 format, run:

```
$ python upload_data.py
```
There will be some text written out, and eventually, the script will pause and you will see something like what follows:

```
$ python upload_data.py 
Started.
Your settings:
/Users/acristia/Documents/PNG-data-for-upload/
LAAC_2019png_postCristia1c


configure zooniverse credentials: 
username []: 

```

Enter your zooniverse username. Next it will ask for your password. Next time, it will remember both pieces of informaiton and you'll be able to press enter without re-entering them.

Then it will pause again and you will see something like:

```
Create new subject set
82707 LAAC_2019png_postCristia1c
What is the subject set number?
```
At this point, enter the number printed right above the question; for instance here, it's 82707. (This is the unique subject set number generated by the panoptes API.)

Finally, you will see the following progress bar:

```
Uploading subjects  [####################################]  100%          
Uploading subjects
```

At this point you are done! Celebrate as you wish.

* Analysis

(This section has not yet been beta-tested.)

After having downloaded the CSV file from the shared OSF repository, launch the notebook Convert_Zooniverse.ipynb to obtain a classification files of type:

```
Idx, UserID, AudioData, Answer, Dataset, Question
```

## Authors

Chiara Semenzin
chiara.semenzin@gmail.com
Alex Cristia
alecristia@gmail.com