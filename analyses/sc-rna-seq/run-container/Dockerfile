# -------------------------------------------------------------------
# Base image
# -------------------------------------------------------------------
FROM rocker/rstudio:4.4.0

# Set default locale
ENV LC_ALL=C

# -------------------------------------------------------------------
# Environment variables for R and virtualenv
# -------------------------------------------------------------------
# Ensure virtual env is on the PATH so we're using the right venv binaries
ENV VIRTUAL_ENV=/opt/container-venv
ENV PATH=/opt/container-venv/bin:$PATH
# Ensure virtual env is on the PATH for LSF TO load the Package with a Specific Library Path
# This fixes the issue with the cc libraries
ENV R_LIBS_USER=/home/user/R/x86_64-pc-linux-gnu-library/4.4


# -------------------------------------------------------------------
# Install Cloudflare certificates so Docker build isn't interrupted by
# certificate errors. Combination of certificates should be valid until 2029.
#

#COPY "./cloudflare_certs/original-certificate.pem" /usr/local/share/ca-certificates/2024-certificate.crt
#COPY "./cloudflare_certs/SJ-2025-2029-CF-WARP-certificate.pem" /usr/local/share/ca-certificates/2025-2029-certificate.crt
#RUN update-ca-certificates
#RUN apt-get update && apt-get install -y --no-install-recommends  ca-certificates gnupg
# -------------------------------------------------------------------


# -------------------------------------------------------------------
# Python virtual environment
# -------------------------------------------------------------------
RUN apt-get -y update && apt-get -y install gdebi-core python3-pip python3-venv
RUN python3 -m venv /opt/container-venv
RUN . /opt/container-venv/bin/activate
# Avoids BiocManager errors in Rhtslib; "fatal error: lzma.h: No such file or directory" etc
RUN apt-get -y install liblzma-dev libbz2-dev

# Avoids BiocManager errors in bluster; "libglpk.so.40: cannot open shared object file: No such file or directory"
RUN apt-get -y install libglpk-dev

# Avoids BiocManager errors in infercnv: "libjags.so.4: cannot open shared object file: No such file or directory"
RUN apt-get update && \
    apt-get install -y r-cran-rjags

# Avoids errors in "Rscript -e 'devtools::install_github("welch-lab/RcppPlanc")'"
RUN apt-get -y install cmake libhdf5-dev

# Needed for pipeline stages
RUN apt-get update && \
    apt-get install -y openjdk-8-jre-headless fastqc curl

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Upgrade pip and install Python deps
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir \
      pandas==2.2.3 \
      numpy==1.23.5 \
      scipy==1.14.1 \
      leidenalg \
      multiqc==1.25
      
# -------------------------------------------------------------------
# R setup
# -------------------------------------------------------------------
# RUN echo "options(repos = c(CRAN = 'https://packagemanager.posit.co/cran/__linux__/jammy/latest'))" >> /usr/local/lib/R/etc/Rprofile.site

# -------------------------------------------------------------------
# install unix utils required by R libraries and not already installed above
# Needed for R libraries
RUN apt-get update && apt-get install -y libfontconfig1-dev \
    libharfbuzz-dev \
    libfribidi-dev \
    libfreetype6-dev \
    libpng-dev \
    libtiff5-dev \
    libjpeg-dev \
    libcairo2-dev \
    libxml2-dev \
    libgsl-dev \
    libgit2-dev \
    libudunits2-dev \
    libgdal-dev \
    gdal-bin \
    libhdf5-dev \
    g++ \
    git \
    libboost-all-dev \
    && rm -rf /var/lib/apt/lists/*

## Core R package managers
RUN Rscript -e 'install.packages(c("BiocManager","devtools","usethis"))'

# Pin Bioconductor version to 3.20
RUN Rscript -e 'BiocManager::install(version="3.20")'

# -------------------------------------------------------------------
# R packages: core dependencies
# -------------------------------------------------------------------

# install dependencies from Guangchuang Yu's lab dependencies and libraries
RUN Rscript -e 'remotes::install_version("ggplot2", version = "3.5.0", repos="https://packagemanager.posit.co/cran/__linux__/jammy/latest")'
RUN Rscript -e 'install.packages("yulab.utils", repos="https://packagemanager.posit.co/cran/__linux__/jammy/latest")'
RUN Rscript -e 'install.packages("ggfun", repos="https://packagemanager.posit.co/cran/__linux__/jammy/latest")'
RUN Rscript -e 'install.packages("ggtangle", repos="https://packagemanager.posit.co/cran/__linux__/jammy/latest")'
RUN Rscript -e 'BiocManager::install("enrichplot", ask=FALSE, update=FALSE, verbose=TRUE)'
RUN Rscript -e 'BiocManager::install("clusterProfiler", ask=FALSE, update=FALSE, verbose=TRUE)'

# Double-check after installation
# https://github.com/bioconda/bioconda-recipes/blob/master/recipes/bioconductor-enrichplot/meta.yaml
RUN Rscript -e 'if (!requireNamespace("enrichplot", quietly = TRUE)) stop("enrichplot not installed!")'
RUN Rscript -e 'if (!requireNamespace("clusterProfiler", quietly = TRUE)) stop("clusterProfiler not installed!")'

#
# -------------------------------------------------------------------
#
# install bioconductor packages
RUN Rscript -e 'install.packages("matrixStats", repos="https://packagemanager.posit.co/cran/__linux__/jammy/latest")'
RUN Rscript -e 'BiocManager::install("celldex", ask=FALSE, update=FALSE, verbose=TRUE)'

#
# -------------------------------------------------------------------
RUN Rscript -e 'BiocManager::install(c( \
  "infercnv","scDblFinder","numbat","miQC","scater", \
  "SingleCellExperiment","SingleR","ggbio","TxDb.Hsapiens.UCSC.hg19.knownGene", \
  "org.Hs.eg.db","org.Mm.eg.db"), ask=FALSE, update=FALSE, verbose=TRUE)'

# -------------------------------------------------------------------
# GitHub packages (pinned where necessary)
# -------------------------------------------------------------------
# October 11, 2025 — last known date where Seurat v4 was compatible with SeuratObject 5.0.0
#
#
# 1. SeuratObject
RUN Rscript -e 'remotes::install_github("satijalab/seurat-object@v5.2.0", upgrade = "never")'
RUN Rscript -e 'if (!requireNamespace("SeuratObject", quietly = TRUE) || packageVersion("SeuratObject") != "5.2.0") {stop("SeuratObject 5.2.0 is not installed!")}'


# How to find the commit SHA
# git clone https://github.com/satijalab/seurat.git
# cd seurat
# git log --oneline --date=short --pretty=format:"%h %ad %s"
# Last commit on October 10, 2025: 914c0f18   

#
# 2. Seurat
RUN Rscript -e 'remotes::install_version("Seurat", version = "4.4.0", repos = "https://packagemanager.posit.co/cran/2023-10-01", upgrade = "never")' 
RUN Rscript -e 'stopifnot(as.character(packageVersion("Seurat")) == "4.4.0")'


# 4. SeuratWrappers
# Install wrappers WITHOUT pulling Seurat 5
RUN Rscript -e 'remotes::install_github("satijalab/seurat-wrappers@community-vignette", dependencies = FALSE, upgrade = "never")'
#

#
#
# -------------------------------------------------------------------
# zlskidmore:update-docker
# RcppPlanc requires c++ headers for hdf5 which are not available in jammy by default, 
# I have modified things such that they are downloaded via an r-package, but in order to do so a specific cmake version is required, 
# which is installed also --no-install-recommends

# install newer cmake, needed for RcppPlanc
RUN apt-get update && apt-get install -y wget g++ make \
   && wget https://cmake.org/files/v3.28/cmake-3.28.3.tar.gz \
   && tar -zxvf cmake-3.28.3.tar.gz \
   && cd cmake-3.28.3 && ./bootstrap && make -j$(nproc) && make install \
   && cd .. && rm -rf cmake-3.28.3*
 
RUN Rscript -e 'devtools::install_github("theAeon/HighFive", upgrade="never")' # needed for RcppPlanc
RUN Rscript -e 'devtools::install_github("welch-lab/RcppPlanc@5744d5f351f3dd1d45e4a59b9d2e4a0e8fdab6e7", upgrade="never")'
# https://github.com/welch-lab/RcppPlanc/issues/3
#RUN Rscript -e 'devtools::install_github("welch-lab/RcppPlanc", force = TRUE, verbose = TRUE)'
# -------------------------------------------------------------------
#
#


RUN Rscript -e 'devtools::install_github("welch-lab/liger", dependencies=TRUE, force=TRUE, upgrade="never")'
#RUN Rscript -e 'devtools::install_github("YuLab-SMU/ggtree", dependencies=TRUE)'
#RUN Rscript -e 'devtools::install_github("kharchenkolab/numbat", dependencies=TRUE)'
RUN Rscript -e 'devtools::install_github("SGDDNB/ShinyCell",  upgrade="never")'
RUN Rscript -e 'devtools::install_github("igordot/scooter", upgrade="never")'

# -------------------------------------------------------------------
# Additional R CRAN packages
# -------------------------------------------------------------------
RUN Rscript -e 'install.packages(c( \
  "clustree", "cowplot", "data.table", "flexmix", "forcats", "fs", "future", "GGally", \
  "ggh4x", "ggpmisc", "ggrepel", "ggthemes", "harmony", "igraph", "irlba", \
  "knitr", "optparse", "patchwork", "purrr", "RColorBrewer", "remotes", "reshape2", "rlist", "R.utils", \
  "shiny", "SoupX", "stringr", "tidytext", "tidyverse", "tinytex", "yaml", \
  "shinyhelper", "DT", "ggdendro", "ragg", "pkgdown"), \
  repos="https://packagemanager.posit.co/cran/__linux__/jammy/latest", upgrade="never")'


RUN Rscript -e 'install.packages("flextable", repos="https://packagemanager.posit.co/cran/__linux__/jammy/latest", upgrade="never")'
RUN Rscript -e 'if (!requireNamespace("flextable", quietly = TRUE)) stop("flextable not installed!")'


# https://github.com/omarwagih/ggseqlogo
RUN Rscript -e 'devtools::install_github("omarwagih/ggseqlogo", ref="master", upgrade="never")'
RUN Rscript -e 'if (!requireNamespace("ggseqlogo", quietly = TRUE)) stop("ggseqlogo not installed!")'

#
# # Docker: Install numbat
# https://github.com/kharchenkolab/numbat/blob/main/docker/Dockerfile
RUN apt-get update --yes && apt-get install --yes build-essential \
  libcurl4-gnutls-dev libxml2-dev libssl-dev libbz2-dev zlib1g-dev \
  libfontconfig1-dev libharfbuzz-dev libfribidi-dev \
  libncurses5-dev libncursesw5-dev liblzma-dev libgit2-dev \
  libfreetype6-dev libpng-dev libtiff5-dev libjpeg-dev \
  libglpk-dev git autoconf gettext libtool automake  \
  samtools sudo

RUN cd /usr/bin && \
  wget https://github.com/samtools/htslib/releases/download/1.15.1/htslib-1.15.1.tar.bz2 && \
  tar -vxjf htslib-1.15.1.tar.bz2 && cd htslib-1.15.1 && make && sudo make install

RUN Rscript -e 'install.packages(c("ragg", "pkgdown"))'

#RUN R -e 'chooseCRANmirror(ind=42); install.packages("ragg")'
#RUN R -e 'chooseCRANmirror(ind=42); install.packages("pkgdown")'

RUN R -e 'devtools::install_github("YuLab-SMU/ggtree", dependencies=TRUE)'

RUN R -e 'devtools::install_github("kharchenkolab/numbat", dependencies=TRUE)'

RUN git clone https://github.com/kharchenkolab/numbat.git

RUN mkdir -p /tmp && chmod 777 /tmp

RUN chmod 777 /numbat/inst/bin/pileup_and_phase.R 

RUN mkdir data

# hg 38
# RUN cd /data && wget -q https://sourceforge.net/projects/cellsnp/files/SNPlist/genome1K.phase3.SNP_AF5e2.chr1toX.hg38.vcf.gz && gzip -d genome1K.phase3.SNP_AF5e2.chr1toX.hg38.vcf.gz 

## Note: not run; the user can follow these commands if they want hg19
# hg19
## RUN cd /data && wget https://sourceforge.net/projects/cellsnp/files/SNPlist/genome1K.phase3.SNP_AF5e2.chr1toX.hg19.vcf.gz && gzip -d genome1K.phase3.SNP_AF5e2.chr1toX.hg19.vcf.gz

# hg38
# RUN cd /data && wget -q http://pklab.med.harvard.edu/teng/data/1000G_hg38.zip && unzip 1000G_hg38.zip 

# hg19
##RUN cd /data && wget http://pklab.med.harvard.edu/teng/data/1000G_hg19.zip && unzip 1000G_hg19.zip

RUN apt-get -y update

RUN git clone https://github.com/single-cell-genetics/cellsnp-lite.git && cd cellsnp-lite && \
  autoreconf -iv && ./configure && make && sudo make install    

#RUN apt-get -y update

RUN wget -q https://storage.googleapis.com/broad-alkesgroup-public/Eagle/downloads/Eagle_v2.4.1.tar.gz && cd .. && tar -xvzf Eagle_v2.4.1.tar.gz && cd /Eagle_v2.4.1 && cp eagle /usr/bin

# Debugging
RUN Rscript -e 'if (!requireNamespace("numbat", quietly = TRUE)) stop("numbat not installed!")'
#


#
#
# ------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Install texlive full version
# This step can break easily. The code is correct, user needs to keep bulding until it passes this step (very time consuming).
# I do not know why it breaks, but there must be some issue from Cloudflare that interferes with permissions and proper installation.
# -------------------------------------------------------------------
# zlskidmore:update-docker
# The texlive installation required some modification, it works on my end, however critically the cpu architecture matters here, I've compiled my image on an arm cpu, 
# if this image is to be used on intel chips you'll need to build this image on an intel machine and modify line 286 to point to an x86_64 path, 
# tex is automatically checking the cpu architecture during the image build and is only compiling for one based on that. 
# (note you can probably emulate x86 via docker if you need to).
# -------------------------------------------------------------------
ENV TEXLIVE_INSTALL_NO_CONTEXT_CACHE=1 \
    NOPERLDOC=1

# Install prerequisites
RUN apt-get update && \
    apt-get install -y wget unzip tar make fontconfig perl openjdk-8-jre \
    libgetopt-long-descriptive-perl libdigest-perl-md5-perl libncurses5 \
    python3-pygments && \
    rm -rf /var/lib/apt/lists/*

# Install TeX Live 2024 with full scheme
RUN wget http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz -O /tmp/install-tl-unx.tar.gz && \
    tar -xzf /tmp/install-tl-unx.tar.gz -C /tmp && \
    cd /tmp/install-tl-* && \
    echo "selected_scheme scheme-full" > texlive.profile && \
    echo "TEXDIR /usr/local/texlive/2024" >> texlive.profile && \
    ./install-tl --profile=texlive.profile && \
    rm -rf /tmp/install-tl-* /tmp/install-tl-unx.tar.gz

# Set PATH for TeX Live 2024
ENV PATH="/usr/local/texlive/2024/bin/x86_64-linux:$PATH"

# Optional: update tlmgr (not strictly needed for scheme-full, but still good practice)
RUN tlmgr option repository http://mirror.ctan.org/systems/texlive/tlnet && \
    tlmgr update --self

# ------------------------------------------------------------------------------------------------------------------------------------------------------------------
#
#

#
# Install CellRanger v8.0.1
#
# You will need to download the desired version from here: https://www.10xgenomics.com/support/software/cell-ranger/downloads/previous-versions
# Save on the same folder where Dockerfile is located
# You should also replace the link with the one from the most recent download as the url expires after 1-2 days
# Set environment variables
#ENV PATH=/opt/cellranger-8.0.1/bin:${PATH}
#ENV LD_LIBRARY_PATH=/opt/cellranger-8.0.1/lib

#RUN curl -o cellranger-8.0.1.tar.gz "https://cf.10xgenomics.com/releases/cell-exp/cellranger-8.0.1.tar.gz?Expires=1738835653&Key-Pair-Id=APKAI7S6A5RYOXBWRPDA&Signature=HtHQnp-gnFU0b5lZlGj8uzZN29pVn5ij7Zoaf1YA6260lqfCqwfaWBH7dU0O8Zsi-C-gY~UPyYGRztoymb5fqR6h0o-FIyYG0KcX5WVAox2cPe1u0k4ZJlq4MrpUmTMbJQ7vpF3abinkB8~y~IzkinoKTuu5Ibvq6ZAZN1MkGBpRZ8hyu-y0dy2KyRru9e3vFWpzRkrEGeUMcx89OMBnM~-MBPRWtc7xrLrhDMAhD9WS4-YGPAQce3qsaq-8OXGnprhpG4hNbnkSzSKcj97p64HmSqW5pddObza7T4bRzLx~e3zYqrLv6bw-YZv~-cRZxgWr2d~fUYWKjEkcikQ32w__"
#RUN tar -xvzf cellranger-8.0.1.tar.gz -C /opt
#

#
# -------------------------------------------------------------------
# Run virtual end on container startup- adjust as needed.
# ENTRYPOINT . /opt/container-venv/bin/activate
# -------------------------------------------------------------------
#
#