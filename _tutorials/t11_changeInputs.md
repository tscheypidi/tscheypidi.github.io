---
layout: tutorial
title:  Input data repositories & Patches
image: assets/images/generic/pic11.jpg
shortID: changeInput
lastUpdated:   2022-02-28
model: MAgPIE
modelVersion: 4.4.2
author:
  - ms
  - fh
level: 4
requirements:
  - Local copy of the MAgPIE model (https://github.com/magpiemodel/magpie)
  - Have R installed (https://www.r-project.org/)
  - Have R package `gms` installed
lessonsContent:
  - Create a local input data repository.
  - Package a patch file.
  - Include a patch in the model configuration
published: true
---

## Introduction

The input data for MAgPIE is prepared by a set of pre-processing
routines that take the data from original sources (e.g. FAO, LPJmL…),
execute additional calculations and convert it to the required MAgPIE
parameter format. These pre-processing routines are not accessible as
open source at the moment. A user is instead provided with a ready-made
inputs that are necessary for the model execution.

The input files are setup in the config file `config/default.cfg`,
usually at the beginning of the settings. Currently, the input data is
set as:

``` r
cfg$input <- c(regional    = "rev4.65_h12_magpie.tgz",
               cellular    = "rev4.65_h12_1998ea10_cellularmagpie_c200_MRI-ESM2-0-ssp370_lpjml-8e6c5eb1.tgz",
               validation  = "rev4.65_h12_validation.tgz",
               additional  = "additional_data_rev4.07.tgz",
               calibration = "calibration_H12_sticky_feb18_free_30Nov21.tgz")
```

Once specified in the configuration as the input data, the data is
automatically downloaded (if needed) when the model run is started.

The prepared input data are compressed tar archive files “`.tgz`”, which
can be opened with software such as [7-Zip](https://www.7-zip.org/), or
in terminal by `tar` and `untar` commands. The data archive files
contain the following types of data:

  - Cellular input data (e.g. land area, crop yields, water
    requirements, carbon density):
      - `cellular    = "rev4.65_h12_1998ea10_cellularmagpie_c200_MRI-ESM2-0-ssp370_lpjml-8e6c5eb1.tgz"`
  - Regional input and validation data (e.g. food demand):
      - `regional    = "rev4.65_h12_magpie.tgz"`
      - `validation  = "rev4.65_h12_validation.tgz"`
  - Calibration data:
      - `calibration = "calibration_H12_sticky_feb18_free_30Nov21.tgz"`
  - Global and other input data (e.g. conversion factors, national
    policies):
      - `additional  = "additional_data_rev4.07.tgz"`

## Patch input data

There is a specific procedure on how to handle the changing of the input
data. It will be demonstrated by the example of changing the USA NDC
policy on afforestation target at 2030.

### Example: Change national land-based NDC policies

Once the input data is downloaded to the local MAgPIE repository in
forms of different input files in designated input folders (in the core,
scripts, modules and module realizations), a user can update or change
these input data files. This can be done directly by manipulating the
files, but this approach carries a risk that such changes are not
documented and that in certain cases the made changes can be overwritten
by the repeated download of the data from the declared repositories. In
order to avoid this risk, it is recommended to create a local folder
that serves as a repository for the patch files that will apply changes
to the data by overwriting the original data.

### Create a local data repository

The folder for local input data repository can be created anywhere and
it’s path must be provided to the settings in `config/default.cfg` file.

Let us assume that the patch folder (`patch_inputdata`) is created in
the main MAgPIE repository. One can do it in R:

``` r
dir.create("./patch_inputdata")
```

or in the command line:

``` cmd
mkdir patch_inputdata
```

Once the directory is created, provide its location to the configuration
file. It’s important to keep the list structure of the repository
information:

``` r
cfg$repositories <- append(list("https://rse.pik-potsdam.de/data/magpie/public"=NULL,
                                "./patch_inputdata"=NULL),
                           getOption("magpie_repos"))
```

### Create a patch and package it

Create a sub-directory in the `./patch_inputdata` which is going to be
used for packaging of the patched files.

``` r
dir.create("./patch_inputdata/patch_ndc_usa")
```

Copy the original file the `policy_definition.csv` in the patch folder.
In R:

``` r
file.copy(from="./scripts/npi_ndc/policies/policy_definitions.csv",
          to="./patch_inputdata/patch_ndc_usa/.")
```

or in the command line:

``` cmd
cp scripts/npi_ndc/policies/policy_definitions.csv patch_inputdata/patch_ndc_usa/.
```

Edit the content, in this case update the USA afforestation NDC policy
(`affore`) with a more ambitious target of 15 MHa of afforested area
starting in 2020 and reaching the target at 2030:

``` txt
USA,affore,ndc,1,2020,2030,15
```

After saving the file, package it with the tardir function in R
environment and delete the file patch folder:

``` r
gms::tardir(dir="patch_inputdata/patch_ndc_usa",
               tarfile="patch_inputdata/patch_ndc_usa.tgz")

unlink("patch_inputdata/patch_ndc_usa", recursive=TRUE)
```

### Add the patch file to the configuration

Finally, the configuration file should be informed about the change in
the input data and the existing patch file that replaces the existing
input data. For this, edit the `config/default.cfg` file from:

``` r
cfg$input <- c(regional    = "rev4.65_h12_magpie.tgz",
               cellular    = "rev4.65_h12_1998ea10_cellularmagpie_c200_MRI-ESM2-0-ssp370_lpjml-8e6c5eb1.tgz",
               validation  = "rev4.65_h12_validation.tgz",
               additional  = "additional_data_rev4.07.tgz",
               calibration = "calibration_H12_sticky_feb18_free_30Nov21.tgz")
```

to:

``` r
cfg$input <- c(regional    = "rev4.65_h12_magpie.tgz",
               cellular    = "rev4.65_h12_1998ea10_cellularmagpie_c200_MRI-ESM2-0-ssp370_lpjml-8e6c5eb1.tgz",
               validation  = "rev4.65_h12_validation.tgz",
               additional  = "additional_data_rev4.07.tgz",
               calibration = "calibration_H12_sticky_feb18_free_30Nov21.tgz",
               patch       = "patch_ndc_usa.tgz")
```

It is very important to add the patch file at the end of the listings in
the `cfg$input` listings, because every next `.tgz` archive will
overwrite the files previously imported by the files that are contained
in it.

At the next start of the model by `Rscript`, the new patch will place
the file with change inputs according to the changes in the settings.

## Alternative way of adding a local repository

As an alternative to the steps described above, one can add a local
repository to `getOption("magpie_repos")`. For this, one has to add an
entry in the `.Rprofile` file. Typically `.Rprofile` is located in the
users’ home directory `(~/.Rprofile)`.

``` r
options(magpie_repos=list("~/input_data/"=NULL))
```

The local repository `input_data` can be located anywhere on your
filesystem. You could then add `patch_ndc_usa.tgz` in this
folder.

Hint: If you can’t locate your `.Rprofile`, you can use
`usethis::edit_r_profile()`.

`cfg$repositories` in `default.cfg` can be reverted back.

``` r
cfg$repositories <- append(list("https://rse.pik-potsdam.de/data/magpie/public"=NULL),
                           getOption("magpie_repos"))
```

With this setup, the download script (`Rscript start.R -> 3 Download
data`) will first look into the public repo and secondly into your local
repo for downloading the files specified in `cfg$input`.

## Exercices 

Write your own starting script that will test the scenario with changed
NDC policy for the USA described above. None of the changes should
actually occur in the `default.cfg`, but instead the starting script
should introduce them to the loaded cfg object.

Add a MAgPIE start script here:
`scripts/start/projects/name_of_your_script.R`


Solution: 
``` r
# |  (C) 2008-2020 Potsdam Institute for Climate Impact Research (PIK)
# |  authors, and contributors see CITATION.cff file. This file is part
# |  of MAgPIE and licensed under AGPL-3.0-or-later. Under Section 7 of
# |  AGPL-3.0, you are granted additional permissions described in the
# |  MAgPIE License Exception, version 1.0 (see LICENSE file).
# |  Contact: magpie@pik-potsdam.de

# ----------------------------------------------------------
# description: Test USA NDC
# ----------------------------------------------------------


######################################
#### Script to start a MAgPIE run ####
######################################

library(lucode2)
library(magclass)
library(gms)

# Load start_run(cfg) function which is needed to start MAgPIE runs
source("scripts/start_functions.R")

#start MAgPIE runs
source("config/default.cfg")

#cfg$force_download <- FALSE

cfg$results_folder <- "output/:title:"

cfg$output <- c("rds_report")


cfg$title <- "SSP2_NDC_default"
cfg <- gms::setScenario(cfg,c("SSP2","NDC"))
start_run(cfg,codeCheck=FALSE)

cfg$title <- "SSP2_NDC_USA"
cfg <- gms::setScenario(cfg,c("SSP2","NDC"))
cfg$input <- c(regional    = "rev4.65_h12_magpie.tgz",
               cellular    = "rev4.65_h12_1998ea10_cellularmagpie_c200_MRI-ESM2-0-ssp370_lpjml-8e6c5eb1.tgz",
               validation  = "rev4.65_h12_validation.tgz",
               additional  = "additional_data_rev4.07.tgz",
               calibration = "calibration_H12_sticky_feb18_free_30Nov21.tgz",
               patch       = "patch_ndc_usa.tgz")
start_run(cfg,codeCheck=FALSE)
```
