---
layout: tutorial
title:  Starting a MAgPIE run
image: assets/images/generic/pic04.jpg
shortID: start
lastUpdated: 2020-11-20
model: MAgPIE
modelVersion: 4.0.0
author:
  - emb
  - kk
level: 1
requirements:
  - GAMS & R installed
  - MAgPIE model installed
lessonsContent:
  - starting MAgPIE with default settings
  - understanding the stages of model execution
  - initial troubleshooting steps in case of an error
  - cancelling a MAgPIE run
published: true
---

# Introduction

Whereas MAgPIE’s inner core is written in GAMS, it comes with an outer
layer for data handling in R. This also applies to the start of MAgPIE.
Moreover, this nested structure leads to some characteristics in code
execution, that should be understood to do basic troubleshooting.

# Start Scripts

To run the model execute within in terminal (cmd for Windows, shell for
Linux, MacOS) in the main folder of the model:

``` bash
Rscript start.R
```

or from within R

``` r
source("start.R")
```

This will give you a list of available run scripts you can choose from,
looking as follows:

``` bash
Global .Rprofile loaded!

Attaching package: 'gms'

The following objects are masked from 'package:lucode2':

    get_info, getfiledestinations


Main selection of MAgPIE start scripts
 ----------------------------------------------
 -> Scripts in this selection are actively   <-
 ->     managed and work out of the box      <-
 ----------------------------------------------
 1:       default | start run with default.cfg settings
 2:    check code | Checking code for consistency issues
 3: download data | just download default.cfg input data
 4:     test runs | test routine to run for new pull requests

Alternatively, choose a start script from another selection:
 5:         extra | Additional MAgPIE start scripts
 6:      projects | Project-specific MAgPIE start scripts
 7:    deprecated | Deprecated scripts
Choose a start script:
```

To run a **single model run with settings as stated in default.cfg** you
can choose start script **`default`**, which can be done by typing `1`
and confirm via `Enter`. A new selection list to choose the way of
executing the code will show up:

``` bash
Choose submission type:
1: Direct execution
2: Background execution
3: Debug mode
Number:
```

To run a the code within your terminal you choose **`Direct execution`**
(again via `1` and `Enter`).

> **Exercise**: Start a magpie run with the `default` start scrpit as
> `Direct execution`.

## More details

  - **`check code`** will execute a test script within R, that checks
    consistence of the code.
  - **`download data only`** will just execute a script that will only
    download data.
  - **`test runs`** will execute the necessary test runs before a pull
    request is done GitHub.

Also, you will find the following sub-folders with additional starting
scripts:

  - **`extra`** These contains currently used scripts that might require
    some manual adjustments before they are executed. Here you will also
    find the **`recalibrate`** script which will recalculate the yield
    calibration factors. Recalibration will only be necessary if the
    input files change. Nevertheless, the default script will
    automatically run recalibration if the inputs change.

  - **`projects`** This folder contains scripts commonly used on ongoing
    projects where the MAgPIE team takes part, and refer to specific run
    settings from individual MAgPIE developers.

  - **`deprecated`** (Currently empty) Once a script is outdated, it
    will be stored in this folder for an interim time before it is
    permanently deleted.

  - You can add your own run scripts by saving them in the folder
    `scripts/start/[specific sub-folder]/`.

Additional remarks:

  - **`Background execution`** will start the model as a job in the
    background and will keep running in case you close your terminal.
    The output will be written into \[run\_title\].log
  - **`Debug mode`** is similar to normal **`Direct execution`**.
  - If you run the code on a high performance cluster handling jobs with
    `SLURM`, you maybe also get a 4. and 5. option for job execution.
    **\``SLURM [priority/standby]`** will handle job submission to hpc.
    This is customize to PIK-cluster settings and may lead to problems
    on other hpc.

# Phases of model execution

As pointed out before the execution of the GAMS model execution is
nested in pre- and postprosessing framework written R.

## Preprocessing

Preprocessing starts with the execution of `Rscript start.R` and
includes the following steps:

| step                               | tasks:                                                                                                                  | embedded in:                                              |
| :--------------------------------- | :---------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------- |
| 1\. job submission                 | load chosen start script, apply chosen submission type                                                                  | start.R                                                   |
| \- lock model folder -             | create `.lock` file to stop co-execution                                                                              | scripts/start\_functions.R                                |
| 2\. configurate run and code check | load libraries, configure settings, run `gms::settingsCheck()` to check code for consistency                            | scripts/start\_function.R                                 |
| 3\. input data                     | check whether data download is nessessary, download data                                                                | scripts/start\_functions.R (`gms::download_distribute()`) |
| 4\. npi/ndc calculation            | calculate for specific cluster and regional settings the representation of land based npi/ndc policies within the model | scripts/npi\_ndc/start\_npi\_ndc.R                        |
| 5\. yield calibration              | calculates a regional yield calibration factor based on a pre run of magpie to be inline with FAO production data       | scripts/calibration/calc\_calib.R                         |
| 6\. gams code submission           | execute gams command to final run the gams model, start post-processing after run finished                              | scripts/run\_submit/submit.R                              |
| \- unlock model folder -           | delete `.lock` file, be ready for next call of start script                                                           | scripts/start\_functions.R                                |

Several of these steps will generate terminal output.

> **Exercise**: Match the terminal output to steps of preprocessing.

## GAMS model execution

The GAMS code execution is started with submit.R and by default there is
no output on your terminal with regard to the optimizations process. You
can find the output in the output folder of the run:

  - `output/[run_title]/full.lst` - complitation, execution & iteration
    log and summaries
  - `output/[run_title]/full.log` - optimization log (detailed solver
    output)

| step                                                                  | more information in: |
| :-------------------------------------------------------------------- | :------------------- |
| 1\. code complilation                                                 | full.lst             |
| 2\. code execution for each time step:                                | full.lst             |
| 2.1. solve food demand model                                          | full.lst, full.log   |
| 2.2. solve magpie model                                               | full.lst, full.log   |
| 2.3. iterate food demand and magpie model till convergence is reached | full.lst             |

> **Exercise**: Open the `full.lst` and locate the different steps of
> gams model run.

## Postprocessing

Postprocessing starts after gams runs finish. If a `fulldata.gdx` was
created, the following postprocessing steps are executed:

| step                                  | tasks:                                                          | embedded in:                              |
| :------------------------------------ | :-------------------------------------------------------------- | :---------------------------------------- |
| 1\. Submit run statistics             | Submit run statistics repository                                | scripts/run\_submit/submit.R              |
| 2\. Execute configured output scripts | Run output.R in postprocessing mode                             | output.R                                  |
| 2.1. rds report                       | Create rds report with magpie4 library                          | scripts/output/rds\_report.R              |
| 2.2. validation                       | Create based on report-functions (magpie4) a validation.pdf     | scripts/output/validation.R               |
| 2.3. disaggregation                   | Disaggregate land use pattern to 0.5° grid, generate spam-files | scripts/output/extra/disaggregation.R     |
|                                       |                                                                 |                                           |
| 2.4. (others)                         | Several other scripts                                           | scripts/output/\[single/comparison\]/\*.R |

Several of these steps will generate terminal output. More information
in tutorial `5_AnalysingModelOutputs.Rmd`.

# First ideas for troubleshooting

Here we list some troubles and where to find them:

| step     |                                                                  | possible issues:                                                             |
| :------- | :--------------------------------------------------------------- | :--------------------------------------------------------------------------- |
| pre1.    | job submission                                                   | General R issues (missing PATH variables)                                    |
|          | \- lock model folder -                                           | `.lock` folder not deleted after termination of a run                        |
| pre2.    | configurate run and code check                                   | missing libraries, failed code check (after change in the code)              |
| pre3.    | input data                                                       | no internet connection, input data not available (check spelling)            |
| pre4.    | npi/ndc calculation                                              |                                                                              |
| pre5.    | yield calibration                                                | general gams issues (compilation or solver failures, missing PATH variables) |
| pre6.    | gams code submission                                             |                                                                              |
|          | \- unlock model folder -                                         |                                                                              |
| gams1.   | code complilation                                                | general gams issues (compilation or solver failures, missing PATH variables) |
| gams2.   | code execution for each time step:                               |                                                                              |
| gams2.1. | solve food demand model                                          | Infeasibilties                                                               |
| gams2.2. | solve magpie model                                               | Infeasibilties                                                               |
| gams2.3. | iterate food demand and magpie model till convergence is reached |                                                                              |
| post1.   | Submit run statistics                                            | No access to repository (not critical)                                       |
| post2.   | Execute configured output scripts                                |                                                                              |
| post2.1. | rds report                                                       | missing libraries (specially gdx, gdxrrw, magpie4)                           |
| post2.2. | validation                                                       | latexrelated r-extension are not working or missing                          |
| post2.3. | disaggregation                                                   |                                                                              |
| post2.4. | (others)                                                         | r extension are missing (e.g. ncdf)                                          |

> **Exercise**: If your run fails, try to find out with the help of
> terminal output and `full.lst`, `full.log`, what went wrong.

# Cancel a model run

  - The model can be stopped with `Crtl` + `C`.
  - If you run it in `background mode` you have to kill the job over the
    Task Manager or process handler (linux: `top`).
  - Make sure that you delete the `.lock` folder, if it was not deleted
    automatically to unlock the model after a termination of a run.

> **Exercise**: Stop the magpie run with `Crtl` + `C`.
