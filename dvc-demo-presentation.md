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
