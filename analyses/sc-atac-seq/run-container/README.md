# Running the Container for scATAC-seq project analysis Workflow

We provide a Dockerfile file that include all tools, packages, and dependencies necessary for running **sc-atac-seq** analysis. These are customized for `Rstudio/R v4.4.0`, `Seurat v4.4.0`, and `Signac v1.16.0`.


## Add your own code for sc-atac-seq

User can add their own code to their module of interest here: `./devops-containers/analyses/sc-atac-seq/<my_project>`.


## 🔧 Using the Container for a scATAC-seq project


### Running the Container on HPC

#### 1. Start an Interactive Session

Open an interactive node on the HPC and adjust memory/resources as needed:

```
bsub -P hpcf_interactive -J hpcf_interactive -n 2 -q standard -R "rusage[mem=16G]" -Is "bash"
```

#### 2. Load the Singularity Module

Please note that a version of Singularity is installed by default on all the cluster nodes at St Jude HPC. Otherwise the user needs to ensure and load Singularity module by running the following on HPC:

```
module load singularity/4.1.1
```

#### 3. Pull the Singularity Container

1. Pull the singularity container from the `devops-containers/analyses/sc-atac-seq` directory:

```
singularity pull docker://achronistjude/singlecell-r4.4-seurat4.4-signac1.16:latest
```


#### 4. Start the Singularity Container

##### a. Running from the Terminal

User can run analysis module while on interactive node after executing the container from `/devops-containers/analyses/sc-atac-seq/`:

```
bash run-terminal.sh
```

Then user can navigate to their module of interest, `./devops-containers/analyses/sc-atac-seq/<name_of_project>`. For example:

```
cd ./devops-containers/analyses/sc-atac-seq/<my_project>
```

and run their code within the container.


##### b. Running from RStudio

User can also run analyses via Rstudio OnDemand after executing the container:

```
bash run-rstudio.sh
```

The `run-rstudio.sh` is running at `IP_ADDR:PORT`. When RStudio launches, please click "Session" -> "Restart R" (at the RStudio web session). Again, the user can navigate to their module of interest and explore/run their analyses.



## Authors

### Container: singlecell-r4.4-seurat4.4-signac1.16:latest

Antonia Chroni, PhD ([@AntoniaChroni](https://github.com/AntoniaChroni)).



## Contact

Contributions, issues, and feature requests are welcome! Please feel free to check [issues](https://github.com/stjude-dnb-binfcore/devops-containers/issues).

---

*These tools and pipelines have been developed by the Bioinformatic core team at the [St. Jude Children's Research Hospital](https://www.stjude.org/). These are open access materials distributed under the terms of the [BSD 2-Clause License](https://opensource.org/license/bsd-2-clause), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
