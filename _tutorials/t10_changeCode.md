---
layout: tutorial
title:  Creating a new realization
shortID: changeCode
image: assets/images/generic/pic10.jpg
lastUpdated:   2022-03-10
model: MAgPIE
modelVersion: 4.4.0
author: fh
level: 4
requirements:
  - Have GAMS installed (https://www.gams.com)
  - Have R installed (https://www.r-project.org/)
  - Have R packages `gms` and `magpie4` installed
  - Have a local copy of the MAgPIE master checked out from https://github.com/magpiemodel/magpie
  - Have downloaded the MAgPIE default data via `Rscript start.R` -> "download data"
lessonsContent:
  - Changing the MAgPIE GAMS code
  - adding In-Code documentation
  - adding a new module realization
  - integrate new realization
published: true
---

## Introduction

MAgPIE has a modular concept. Each module (e.g. `pasture`) can have
several realizations (e.g. `dynamic` and `static`). The purpose of these
realizations is a) to maintain the current default model behavior and b)
to keep the model operational while developing a new realization. A
typical use case is the extension of a realization by a specific
feature. In this case, one would copy the current default realization,
rename it properly, and apply the wanted changes. Then, the model
behavior can be compared between the two realizations. Possibly, the
new realization might become the new default at some point and the old
realization is deleted.

This tutorial shows how to add a new realization to a module in the
MAgPIE model. To illustrate the different steps, we will expand the
`urban` land module. In the current MAgPIE master, the `urban` land
module has a `static` and `exo_nov21` realization. In `static`, 
urban land is just static over time. In `exo_nov21`, future
urban land is prescribed based on existing scenarios of urbanization.
In this tutorial, we will add a new realization called `pop_growth`, 
which changes urban land based on population
growth. Note that this should be only used for illustrative purposes. 

## Adding a new realization

We want to add a new realization to the `urban` land module. The `urban`
land module is located here: `modules/34_urban`.

### Add a new realization by duplicating an existing one

Duplicate the `static` folder and rename it to `pop_growth`. Now we need to edit and add 
files in the `pop_growth` folder. 
In the end, you should have the following files:

#### declarations.gms

``` r
positive variables
 vm_cost_urban(j)			Technical adjustment cost
;

equations
 q34_urban(j)       		urban land (mio. ha)
;

parameters
 p34_pop_growth(t_all,i) 		annual population growth rate (1)
;
```

#### equations.gms

``` r
$ontext
Urban land in the current time step (vm_land) is forced to the value from the previous 
time step (pcm_land) multiplied by 1 + population growth between these time steps.
$offtext

q34_urban(j2)..
 vm_land(j2,"urban") =e= 
 pcm_land(j2,"urban") * (1 + sum((ct,cell(i2,j2)), p34_pop_growth(ct,i2)) * m_timestep_length);
```

#### preloop.gms

``` r
$ontext
#Calculate annual population growth rate
Since the temporal resolution of t_all is 5-year time steps, we have to divide the change 
between time steps by the number of years between these time steps (m_yeardiff) to get 
annual values.
$offtext

loop(t_all$(ord(t_all) > 1),
 p34_pop_growth(t_all,i) = (im_pop(t_all,i)/im_pop(t_all-1,i) - 1) / m_yeardiff(t_all);
);
```

#### presolve.gms

``` r
*fix carbon stocks to zero
vm_carbon_stock.fx(j,"urban",c_pools) = 0;
*Biodiversity
vm_bv.fx(j,"urban", potnatveg) = pcm_land(j,"urban") * fm_bii_coeff("urban",potnatveg) * fm_luh2_side_layers(j,potnatveg);
*fix costs to zero
vm_cost_urban.fx(j) = 0;
```

#### realization.gms

``` r
*' @description In this realization, urban land expands based on population growth.
*' Carbon stocks are assumed zero.

*' @limitations Only for illustrative purpose
```

### Update the code

To include the new realization `pop_growth` properly into the GAMS code we
run a specific R command in the main folder. First navigate in your
command line to the MAgPIE main folder, then open a new R session (type
`R` followed by ENTER), and then copy-paste the following R commands:

``` r
gms::update_fulldataOutput()
gms::update_modules_embedding()
```

These two commands will add a `postsolve.gms` file, and update the
`realization.gms` and `declarations.gms` files. Hint: If you change, add
or delete variables/parameters always run these commands to avoid GAMS
compilation errors.

Run codeCheck to check if all module interfaces exist.

``` r
gms::codeCheck(interactive = TRUE)
```

codeCheck will detect a problem with interfaces in 34\_urban. Follow the
instructions, which will add a `not_used.txt` file.

Now you can quit the R session with `q()`.

## Testing a new realization

### Start a model run

For a quick GAMS test, we simply set the new realization in the file
`main.gms`: `$setglobal urban pop_growth`. We can then
check if the model compiles correctly with this command evoked from the
command line.

``` r
gams main.gms action=C
```

If you get compilation errors, you have to resolve these first. Look
into the `main.lst` file. It will tell you what kind of error occurred.

To make our test run as fast as possible, we reduce the number of time
steps to 3. For this, we set `$setglobal c_timesteps quicktest` in main.gms.

Now we can start a test run with this command. This can take up to 10-15
minutes, depending on the resources of your machine.

``` r
gams main.gms
```

GAMS will create a `fulldata.gdx` file in the main folder.

For starting a productive model run, we would have to change `cfg$gms$urban` in 
the config file `config/default.cfg` (`cfg$gms$urban   <- "pop_growth"`).
We could now start a model run with `Rscript start.R -> 1: default -> 1:
Direct execution`. Or, even better write a start script without changing
`config/default.cfg`.

### Check the results

Start a new R session in the MAgPIE main folder, and execute these
commands.

``` r
options(digits=2)
library(magpie4)
gdx <- "fulldata.gdx"
land(gdx,level="glo",type="urban")
land(gdx,level="reg",type="urban")
```
