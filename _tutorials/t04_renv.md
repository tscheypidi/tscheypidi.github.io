---
layout: tutorial
title: Managing renv
shortID: renv
image: assets/images/generic/pic04.jpg
lastUpdated: 2022-11-24
model: MAgPIE
modelVersion: 4.6.0
author: ps
level: 3
requirements:
  - R installed (<https://www.r-project.org/>)
  - MAgPIE model folder cloned
lessonsContent:
  - Understand what renv is, how it works and why and how MAgPIE uses it.
  - Update and restore renvs via piamenv functions.
  - Get an overview of renv functions to get more control.
exercises:
  - task: "Start an interactive `R` session in the main folder and check where `R` is loading packages from via `.libPaths()`."
    solution: "1. Start an R session.\n
               2. .libPaths()\n
                 - [1] '/home/pascal/dev/magpiemodel/renv/library/R-4.2/x86_64-pc-linux-gnu'\n
                 - [2] '/home/pascal/dev/magpiemodel/renv/sandbox/R-4.2/x86_64-pc-linux-gnu/6e5b3dc8'"
  - task: "Check whether `make` is installed and install it if missing. Then get a list of all available make targets."
    solution: "1. Open a terminal and navigate to your main MAgPIE directory\n
               2. run `make`; if this fails install make (on Windows: `winget install gnuwin32.make`) and try again\n
                 - help:    Show this help.\n
                 - docs:    Generate/update model HTML documentation in the doc/ folder.\n
                 - update-renv: Upgrade all pik-piam packages in your renv to the respective latest release and write renv.lock to archive.\n
                 - update-renv-all: Upgrade all packages (including CRAN packages) in your renv to the respective latest release and write renv.lock to archive.\n
                 - archive-renv: Write renv.lock to archive.\n
                 - restore-renv: Restore renv to the state described in interactively selected renv.lock from the archive or a run folder.\n
                 - check:   Check if the GAMS code follows the coding etiquette using gms::codeCheck.\n
                 - check-fix: Check if the GAMS code follows the coding etiquette and offer fixing any problems directly if possible using gms::codeCheck."
  - task: "Run all of the explained functions with arguments that actually work."
    solution: "1. Open an R session within your model's main directory\n
               2. `renv::install('magclass@6.7.1')`\n
               3. `renv::install('pascalfu/madrat', ref = '3224977')`\n
               4. `gert::git_clone('https://github.com/pfuehrlich-pik/madrat.git', './local-madrat'); renv::install('./local-madrat')`\n
               5. `renv::remove('madrat')`\n
               6. `renv::update(exclude = 'renv')`\n
               7. `renv::update('magclass')`\n
               8. `renv::snapshot()`\n
               9. `renv::install('magclass@6.7.1'); renv::status()`\n
               10. `piamenv::archiveRenv()`"
categories: tutorial
published: true
---
# basics
MAgPIE uses [renv](https://rstudio.github.io/renv/) for managing required R packages. When starting R from the MAgPIE folder the corresponding renv is automatically activated. This means that packages are installed to and loaded from the renv subfolder instead of the usual package library. Thus, updates in the global package library do not affect MAgPIE. This is especially important on multi-user systems where packages may be loaded by some users while others are updating these packages. In such a situation, the loaded packages are no longer valid and R crashes. Another advantage of renv is that it can document exactly which packages are currently installed, and it makes it easy to go back to a previous package configuration via renv.lock files.

## MAgPIE renv structure
### main renv
- used when starting R in main folder, e.g. `Rscript start.R` or `Rscript output.R`
- should not have renv.lock to prevent confusion when that gets out of sync with the package library
- modify via `piamenv` functions, see below

> **Exercise**: Start an interactive `R` session in the main folder and check where `R` is loading packages from via `.libPaths()`. (solution at the bottom)

### run renv
- each run has its own run renv and renv.lock documenting the package environment
- when starting a run the main renv is copied to the run folder
- the run renv is used for the run itself and automatic post-processing
- run renvs should ensure reproducibility, so they must never change

> **Exercise**: Start a new run and wait until a new renv in the run folder was created. Then navigate to the new run folder, start an interactive `R` session there and again check where `R` is loading packages from via `.libPaths()`.

## modifying renv
- to modify your renv use `piamenv` functions described below
- alternatively run `make <target>` on the command line
- `make` is pre-installed except on Windows, run `winget install gnuwin32.make` to install it there
- `make help` will print a list of available targets

> **Exercise**: Check whether `make` is installed and install it if missing. Then get a list of all available make targets. (solution at the bottom)

### update renv
- updates all pik-piam packages in main renv
- writes updated renv.lock to renv/archive
- user is asked to run this when starting a new run
- run manually with `piamenv::updateRenv()` (`make update-renv`)

> **Exercise**: Update all pik-piam packages in the main renv manually.

### restore renv
- resets main renv to state recorded in renv.lock file from renv/archive or from existing run folder
- useful for using the same renv as a colleague or for reproducing results of an old run
- run `piamenv::restoreRenv('path/to/renv.lock')`
- or choose renv.lock interactively with `piamenv::restoreRenv()` (`make restore-renv`)

> **Exercise**: Restore the main renv to the state of the renv of the run you started earlier.

# advanced
## renv files
### renv.lock
- text file which lists for each installed package:
	- version
	- source repo (CRAN, GitHub, ...)
- any renv can be reset to state described in renv.lock (see "restore renv")

> **Exercise**: Open an renv.lock file in an editor to see how renv lockfiles look like.

### .Rprofile
- in the main magpie folder (not to be confused with your personal ~/.Rprofile)
- sourced whenever R is started in the same folder as the .Rprofile
- activates the corresponding renv
- one time only: installs all dependencies into renv

### renv folder
- auto-generated and managed by renv, includes the following:
- renv settings
- actual package library
- archive folder for {timestamp}_renv.lock files

> **Exercise**: Navigate to the path where renv loads packages from, and check whether they are actually symlinks into the renv cache.

## renv functions
The `piamenv` functions explained earlier should cover all common tasks, use the following for more control.
- `renv::install("package@2.3.4")` install specific package version
- `renv::install("githubuser/package", ref = "<commit hash>")` install package from GitHub, optionally provide commit hash
- `renv::install("/p/tmp/username/yourpackagefolder")` install package from sources
- `renv::remove("package")` uninstall package
- `renv::update(exclude = "renv")` (`make update-renv-all`) update all packages except renv (please do not update renv itself)
- `renv::update("package")` update package
- `renv::snapshot()` write renv.lock
- `renv::status()` show differences between library and renv.lock
- `piamenv::archiveRenv()` (`make archive-renv`) write renv.lock to renv/archive
- renv documentation: https://rstudio.github.io/renv/

> **Exercise**: Run all of the explained functions with arguments that actually work. (solution at the bottom)

## package development
When testing packages in development use `renv::install("githubuser/package")` to install the package from your fork. Be careful with updates, your custom package might get overwritten.
