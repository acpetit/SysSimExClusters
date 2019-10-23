# SysSimExClusters

This repository provides a comprehensive model for Clustered Planetary Systems for SysSim. The details of the specific models for exoplanetary systems are described in our paper ([arXiv link to paper](https://arxiv.org/abs/1907.07773); it is currently in the referee stage).

<center><img src="/best_models/Clustered_P_R_observed.gif" alt="Best fit clustered models" width="800"/></center>  



## How to use our Clustered model for studying exoplanetary system populations:

We provide a large set of simulated catalogs from our models directly in this repository. We refer to a *physical catalog* as a set of intrinsic, physical planetary systems, and an *observed catalog* as a set of transiting and detected planet candidates that a Kepler-like mission might produce. If you simply wish to use these simulated catalogs as examples of our models, then no installation is required! Simply download any of these tables and use them for your own science.
* Go to the [SysSimExClusters Simulated Catalogs](https://psu.box.com/s/v09s9fhbmyele911drej29apijlxsbp3) folder.
* Navigate into the "Clustered_P_R/"\* directory, and then into either the KS or AD subdirectories. (Use KS if you don't know the difference or have trouble deciding.) 
* Download a "physical_catalogX.csv" file (X = an index/number) for a table including all the physical planets in a simulated catalog.

| target_id | star_id | planet_mass    | planet_radius | clusterid | period     | ecc      | star_mass |
|-----------|---------|----------------|---------------|-----------|------------|----------|-----------|
|           |         | (solar masses) | (solar radii) |           | (days)     |          | (solar masses) |
| 1.0       | 17593.0 | 3.3830e-7      | 0.0061        | 1.0       | 159.0465   | 0.0107   | 0.992     |
| ...       | ...     | ...            | ...           | ...       | ...        | ...      | ...       |

* Download an "observed_catalogX.csv" file (X = an index/number) for a table including all the observed planets that a simulated Kepler mission would detect given the true planetary systems listed in "physical_catalogX.csv".

| target_id | star_id | period    | depth   | duration | star_mass      | star_radius |
|-----------|---------|-----------|---------|----------|----------------|-------------|
|           |         | (days)    |         | (days)   | (solar masses) | (solar radii) |
| 75.0      | 66728.0 | 52.7541   | 0.00083 | 0.1090   | 0.928          | 0.7874      |
| ...       | ...     | ...       | ...     | ...      | ...            | ...         |

In each of these files, the header contains all the parameters of the model used to generate that catalog.

For each planet (row), "**target_id**" refers to the index of the star in the simulation (e.g. 1 for the first star in the simulation), "**star_id**" refers to the index of the star based on where it is in the input stellar catalog (i.e. the row number in the "q1_q17_dr25_gaia_fgk_cleaned.csv" catalog, which can be found in the "plotting/" directory), and "**clusterid**" is a cluster identifier (i.e., which "cluster" in the system the planet belongs to). Note that indexing starts at 1 in Julia. Stars without any planets are not included in these tables.

\*Note: We also provide simulated catalogs from two other models, "Clustered_P" and "Non_Clustered", in their respective subdirectories. These models are also described in the paper linked above but we do not recommend using these models for science as they are not as good of a description of the data as our "Clustered_P_R" model.



## How to simulate catalogs (physical and observed) from our Clustered model on your own:

### Installation:

* You will need to first install the [ExoplanetsSysSim](https://github.com/ExoJulia/ExoplanetsSysSim.jl) package and set up some additional repositories; follow the instructions listed in the README of that page.
* Clone this repository.
```
git clone git@github.com:ExoJulia/SysSimExClusters.git
```
* Switch to the "He_Ford_Ragozzine_2019" branch of the repository.
```
git checkout He_Ford_Ragozzine_2019
```

### Usage:

To generate one simulated catalog (physical and observed) from the Clustered model with a user defined set of model parameters, do the following:

1. Move into the "src/" directory and edit the file "param_common.jl". Set a value for each of the model parameters. For example, to change the mean number of clusters, planets per cluster, and period power-law:
```julia
add_param_active(sim_param,"log_rate_clusters", log(1.6))            # Set the (log) mean number of clusters
add_param_active(sim_param,"log_rate_planets_per_cluster", log(1.6)) # Set the (log) mean number of planets per cluster
add_param_active(sim_param,"power_law_P", 0.)                        # Set the period power-law index (-1 = flat in log-period)
```
**NOTE:** for most accurate results, you should also add/uncomment the line:
```julia
add_param_fixed(sim_param,"osd_file","dr25fgk_relaxcut_osds.jld2")
```
However, this file requires about 8gb of memory to read.

2. Move into a directory where you want your simulated catalogs to be saved and run the script "generate_catalogs.jl" in "examples/" in Julia. For example, you can navigate to "examples/", start Julia, and run:
```julia
include("generate_catalogs.jl")
```
This will generate the following files:
* Physical and observed catalogs of planets (and stars) in table format:
  * "physical_catalog.csv"
  * "physical_catalog_stars.csv"
  * "observed_catalog.csv"
  * "observed_catalog_stars.csv"

These files are analogous to the simulated catalogs we provide as described above.

In addition, the following files will also be generated:
* An individual file for the true cluster id's, periods, orbital eccentricities, planet radii, planet masses, stellar radii, and stellar masses, of all the planets per system (and stars with planets) in the physical catalog:
  * "clusterids_all.out"
  * "periods_all.out"
  * "eccentricities_all.out"
  * "radii_all.out"
  * "masses_all.out"
  * "stellar_radii_with_planets.out"
  * "stellar_masses_with_planets.out"

The data in these files are the same as those in "physical_catalog.csv", just organized in a different format.
* An individual file for the observed periods, transit depths, transit durations, stellar radii, and stellar masses of all the planets (and stars with observed planets) in the observed catalog:
  * "periods.out"
  * "depths.out"
  * "durations.out"
  * "stellar_radii_obs.out"
  * "stellar_masses_obs.out"

The data in these files are the same as those in "observed_catalog.csv", just organized in a different format (sorted into systems with 1, 2, 3, ..., and 8 observed planets).



## If you wish to make similar plots as those included in our paper:

While the core ExoplanetsSysSim and SysSimExClusters code is written in Julia, almost all of the figures produced for the paper are generated from Python (2.7) code that was written by Matthias He. We provide these Python scripts in the "plotting/" directory but do not fully maintain or document them.



## If you wish to reproduce the results and methodology described in our paper:

First read the paper, especially Section 2 (Methods), in detail.
