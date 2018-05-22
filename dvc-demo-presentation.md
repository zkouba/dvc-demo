---
title: Data Version Control (DVC)
author: Zdenek Kouba
patat:
    incrementalLists: true
...

```
______      _          _   _               _               _____             _             _ 
|  _  \    | |        | | | |             (_)             /  __ \           | |           | |
| | | |__ _| |_ __ _  | | | | ___ _ __ ___ _  ___  _ __   | /  \/ ___  _ __ | |_ _ __ ___ | |
| | | / _` | __/ _` | | | | |/ _ \ '__/ __| |/ _ \| '_ \  | |    / _ \| '_ \| __| '__/ _ \| |
| |/ / (_| | || (_| | \ \_/ /  __/ |  \__ \ | (_) | | | | | \__/\ (_) | | | | |_| | | (_) | |
|___/ \__,_|\__\__,_|  \___/ \___|_|  |___/_|\___/|_| |_|  \____/\___/|_| |_|\__|_|  \___/|_|
                                                                                             
```
---

# Agenda
> 1. **Motivation**
> 1. **What is DVC**
> 1. **Demo**

---

# Motivation
> - development of ML is **iterative**

- **typical workflow:**
    1. extract compressed data
    1. preprocess data
    1. extract features
    1. train model
    1. evaluate model
        > _Oops.. bug in data-preprocessing_
    1. fix data preprocessing script
    1. re-run preprocessing step
    1. train model
    1. evaluate model
        > _Yep, forgot to re-run feature-extraction_
    1. re-run feature-extraction
    1. _..._

---

# DVC comes to rescue
> - Data **Version Control** (DVC)
- keeps track of the sequence of operations over data
- helps to _reproduce_ the steps
    \* DVC evaluates which steps need to be re-run and which can be skipped
- goes hand-to-hand with _git_

. . .

https://dataversioncontrol.com/

---

# How do I use it
## Record a new step
```
dvc run \
    -d <input file> \
    -d <my data transforming script> \
    \ # etc.
    -o <output file of this step> \
    <the bash command it-self>
```
> - definition of a step in the pipeline
- specifies: 
    \* the **dependencies** (inputs) of this step
    \    **-** input data files
    \    **-** scripts used in this step
    \    **-** _etc._
    \* the **results** of this step
    \    **-** output data files
    \    **-** trained models
    \    **-** _etc._
    \* the **command** executed in this step

. . .

## Reproduce the recorded steps
```
dvc repro
```
> - checks what the current state of the tracked files is
- determines which steps are up-to-date and which steps are affected by some changes
- _re-runs only the affected steps_

---

```
 ______   ________  ____    ____   ___    
|_   _ `.|_   __  ||_   \  /   _|.'   `.  
  | | `. \ | |_ \_|  |   \/   | /  .-.  \ 
  | |  | | |  _| _   | |\  /| | | |   | | 
 _| |_.' /_| |__/ | _| |_\/_| |_\  `-'  / 
|______.'|________||_____||_____|`.___.'  
```

- Initialize VDC
```
dvc init
tree -aL 2
dvc add data/Posts.xml.tgz
git add . && git commit -am 'Add source dataset'
cat data/Posts.xml.tgz.dvc
```

- Step 1 - decompress data
```
dvc run \
    -d data/Posts.xml.tgz \
    -o data/Posts.xml \
    tar zxf data/Posts.xml.tgz -C data/
du -sh data/*
git add . && git commit -am 'Unarchive'
```

- Step 2 - preprocess data and prepare training and test sets
```
dvc run \
    -d data/Posts.xml -d code/xml_to_tsv.py -d code/conf.py \
    -o data/Posts.tsv \
    python code/xml_to_tsv.py
```
```
dvc run \
    -d data/Posts.tsv -d code/split_train_test.py -d code/conf.py \
    -o data/Posts-test.tsv -o data/Posts-train.tsv \
    python code/split_train_test.py 0.33 0
```
```
echo '**.pyc' >> .gitignore
git add . && git commit -am 'Process to TSV and separate test and train'
```

---

```
 ______   ________  ____    ____   ___    
|_   _ `.|_   __  ||_   \  /   _|.'   `.  
  | | `. \ | |_ \_|  |   \/   | /  .-.  \ 
  | |  | | |  _| _   | |\  /| | | |   | | 
 _| |_.' /_| |__/ | _| |_\/_| |_\  `-'  / 
|______.'|________||_____||_____|`.___.'  
```


- Final step - extract features, train and evaluate model
```
dvc run \
    -d code/featurization.py -d code/conf.py \
    -d data/Posts-train.tsv -d data/Posts-test.tsv \
    -o data/matrix-train.p -o data/matrix-test.p \
    python code/featurization.py
```
```
dvc run \
    -d data/matrix-train.p -d code/train_model.py -d code/conf.py \
    -o data/model.p \
    python code/train_model.py 0
```
```
dvc run \
    -d data/model.p -d data/matrix-test.p -d code/evaluate.py -d code/conf.py \
    -o data/eval.txt \
    -f Dvcfile \
    python code/evaluate.py
```
```
git add . && git commit -am 'Evaluate'
cat data/eval.txt
```
    - _(0.635427? We can do better...)_

- Step 'Final + 1' - tune training parameters
```
vim +27 code/train_model.py
dvc repro
cat data/eval.txt
```

