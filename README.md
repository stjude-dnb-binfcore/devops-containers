<p align="center";">
  <img src="figures/img/DNB-BINF-Core-logo.png" alt="DNB-BINF-Core logo" width="560px" />
</p>
<p align="center";">
  <a href="https://www.repostatus.org/#active">
    <img src="https://www.repostatus.org/badges/latest/active.svg?style=for-the-badge" alt="The project has reached a stable, usable state and is being actively developed." />
  </a>
  <a href="https://github.com/stjude-dnb-binfcore/devops-containers">
    <img src="https://img.shields.io/badge/version-1.0.0-brightgreen" alt="Version" />
  </a>
</p>



# DevOps Containers

This repository hosts a collection of **reusable container images** designed to support various stages of data processing pipelines, with a focus on **single-cell analysis** workflows such as scRNA-seq. Support for additional modalities like **scATAC-seq** is currently in development — stay tuned!

These containers are built to be **modular**, **consistent**, and **reproducible**, ensuring reliable execution across a wide range of environments, including local development, cloud platforms, and CI/CD systems.

Although primarily used within automated pipelines — such as  
 - [sc-rna-seq-snap](https://github.com/stjude-dnb-binfcore/sc-rna-seq-snap)
 - [sc-rna-seq-snap-10x-Flex](https://github.com/stjude-dnb-binfcore/sc-rna-seq-snap-10x-Flex)
 - [sc-epigenie](https://github.com/stjude-dnb-binfcore/sc-epigenie) 

each container is **self-contained and portable**, making it easy to use them independently in other projects or custom workflows. This flexibility makes the containers valuable tools for both **bioinformatics** and **DevOps** tasks where reproducibility and scalability are key.


## 🚀 Key Features

- **Reusable**: Designed to be used across multiple pipelines and projects
- **Versioned & Maintainable**: Each container is built with version control in mind
- **Isolated**: Minimal dependencies ensure consistent and portable environments
- **Well-Documented**: Each container folder includes its own `README.md` with build/run instructions




## Table of Contents
1. [Using the Container for a scRNA-seq project](##using-the-container-for-a-scrna-seq-project)
2. [Accessing the Container](#accessing-the-Container)
3. [Running the Container on HPC](#running-the-container-on-hpc)
   - [1. Start an Interactive Session](#1-start-an-interactive-session)
   - [2. Load the Singularity Module](#2-load-the-singularity-module)
   - [3. Pull the Singularity Container](#3-pull-the-singularity-container)
   - [4. Start the Singularity Container](#4-start-the-singularity-container)
     - [a. Running from the Terminal](#a-running-from-the-terminal)
     - [b. Running from RStudio](#b-running-from-rstudio)




## 🔧 Using the Container for a scRNA-seq project

We provide a Dockerfile and Definition file that include all tools, packages, and dependencies necessary for running a scRNA-seq project analysis modules. These are customized for `Rstudio/R v4.4.0` and `Seurat v4.4.0`.


### Accessing the Container

Users can clone the `devops-containers` repository to their local machine to access and run the container of their preference. 

```
git clone https://github.com/stjude-dnb-binfcore/devops-containers.git
```


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

1. Pull the singularity container from the `devops-containers` root_dir

```
singularity pull docker://achronistjude/rstudio_4.4.0_seurat_4.4.0:latest
```


#### 4. Start the Singularity Container

##### a. Running from the Terminal

User can run analysis module while on interactive node after executing the container:

```
bash run-terminal-sc-rna-seq.sh
```

Then user can add their own code and navigate to their module of interest, `./devops-containers/analyses/sc-rna-seq/<name_of_project>`. For example:

```
cd ./devops-containers/analyses/sc-rna-seq/my_project
```

and run their code within the container.


##### b. Running from RStudio

User can also run analyses via Rstudio OnDemand after executing the container:

```
bash run-rstudio-sc-rna-seq.sh
```

The `run-rstudio-sc-rna-seq.sh` is running at `IP_ADDR:PORT`. When RStudio launches, please click "Session" -> "Restart R" (at the RStudio web session). Again, the user can navigate to their module of interest and explore/run their analyses.





### Below is the main directory structure listing the analyses and data files used in this repository

```
├── analyses
|  ├── sc-atac-seq
|  └── sc-rna-seq
├── figures
├── LICENSE
├── README.md
├── run-container
├── run-rstudio-sc-rna-seq.sh
├── run-terminal-sc-rna-seq.sh
└── SECURITY.md
```

## Authors

### Container: rstudio_4.4.0_seurat_4.4.0:latest

Antonia Chroni, PhD ([@AntoniaChroni](https://github.com/AntoniaChroni)) and 
Walid Abu Al-Afia ([@walidabualafia](https://github.com/walidabualafia)).


## Contact

Contributions, issues, and feature requests are welcome! Please feel free to check [issues](https://github.com/stjude-dnb-binfcore/devops-containers/issues).

---

*These tools and pipelines have been developed by the Bioinformatic core team at the [St. Jude Children's Research Hospital](https://www.stjude.org/). These are open access materials distributed under the terms of the [BSD 2-Clause License](https://opensource.org/license/bsd-2-clause), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
