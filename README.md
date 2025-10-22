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



# DevOps Containers, and mor
This repository hosts a collection of **reusable container images** designed to support various stages of data processing pipelines, with a focus on **single-cell genomics analysis** workflows, and more specifically for **scRNA-seq** and **scATAC-seq**. 

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
1. [Accessing the Container](#accessing-the-Container)
2. [Using the Container for a scRNA-seq project](#using-the-container-for-a-scrna-seq-project)
3. [Using the Container for a scATAC-seq project](#using-the-container-for-a-scatac-seq-project)



## Accessing the Container

Users can clone the `devops-containers` repository to their local machine to access and run the container of their preference.

```
git clone https://github.com/stjude-dnb-binfcore/devops-containers.git
```

or they can fork the repository. 


## Using the Container for a scRNA-seq project

Users can add their own code to the module of interest at `./devops-containers/analyses/sc-rna-seq/<my_project>`. Afterward, follow the instructions in the [README.md](https://github.com/stjude-dnb-binfcore/devops-containers/tree/main/analyses/sc-rna-seq/run-container/README.md) to pull and access the container.


## Using the Container for a scATAC-seq project

Users can add their own code to the module of interest at `./devops-containers/analyses/sc-atac-seq/<my_project>`. Afterward, follow the instructions in the [README.md](https://github.com/stjude-dnb-binfcore/devops-containers/tree/main/analyses/sc-atac-seq/run-container/README.md) to pull and access the container.


### Below is the main directory structure listing the analyses and data files used in this repository

```
├── analyses
|  ├── sc-atac-seq
|  |   ├── myproject
|  |   └── run-container
|  └── sc-rna-seq
|      ├── myproject
|      └── run-container
├── figures
├── LICENSE
├── README.md
└── SECURITY.md
```

## Authors

Antonia Chroni, PhD ([@AntoniaChroni](https://github.com/AntoniaChroni))


## Contact

Contributions, issues, and feature requests are welcome! Please feel free to check [issues](https://github.com/stjude-dnb-binfcore/devops-containers/issues).

---

*These tools and pipelines have been developed by the Bioinformatic core team at the [St. Jude Children's Research Hospital](https://www.stjude.org/). These are open access materials distributed under the terms of the [BSD 2-Clause License](https://opensource.org/license/bsd-2-clause), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
