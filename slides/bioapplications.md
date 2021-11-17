---
theme: csc-2019
lang: en
---

# Containerised Bio Applications in HPC Environment {.title}
In this section, you will learn how to manage different disk areas in HPC environment at CSC

<div class="column">
![](https://mirrors.creativecommons.org/presskit/buttons/88x31/png/by-sa.png)
</div>
<div class="column">
<small>
All material (C) 2020-2021 by CSC -IT Center for Science Ltd.
This work is licensed under a **Creative Commons Attribution-ShareAlike** 4.0
Unported License, [http://creativecommons.org/licenses/by-sa/4.0/](http://creativecommons.org/licenses/by-sa/4.0/)
</small>
</div>

# Outline
- Basic introduction to (Bio)container 
- Searching pre-existing biocontainers 
- Deploying (running) biocontainers in HPC environment
   - Containers available as modules
   - Custom-made containers
- Mounting/binding volumes
- Bioapplication examples

# Topic 

Basic Introduction to (Bio)containers

# Some Basic Terminology

- **Image**: self-contained snapshot of your full application,packages and dependencies
- **Container**:  A running instance of an image
- **Container Engine**:Creates, ships and runs  containers deployable on a physical or  virtual, host locally, in a datacenter or cloud service provider
- **Registry Service**: Cloud or server based storage and distribution service for your images

# Some Basic Terminology

![](./img/terminology.png)

# Moving data between and to/from supercomputers

- Puhti and Mahti have their own disk systems
- Data can be moved between the supercomputers 
    - [directly with rsync](https://docs.csc.fi/data/moving/rsync/) 
    - via [Allas object storage](https://docs.csc.fi/data/Allas/)
- There are [many ways to transfer data to/from CSC and your local computer](https://docs.csc.fi/data/moving/)

# Central Dogma of Containerisation

![](./img/dogma.png)

- Two containerisation platforms
    - Docker image built from dockerfiles
    - Singularity image built from singularity recipes (deffiles)

# Topic 

Biocontainers and Related Registries

# Biocontainers: Bioinformatics Containers
- A community-driven effort 
- Focus is to create and manage bioinformatics software containers
- Focus on popular Omics’ methods (Genomics, proteomics, metagenomics, metabolomics)
- Can be integrated into bioinformatics pipelines and different architectures
- Provides ready-made containers for bioinformatics community
   - [QUAY registry](https://quay.io)
   - [DockerHub](https://hub.docker.com/)

# Biocontainers Registry (1/2)

- A hosted registry of all BioContainers images that are ready to be used 
- The interface to search BioContainers across all the registries.
- Source of Biocontainer image can be conda recipe or dockerfile
  ![](./img/bioc_home.png)

# Biocontainers Registry (2/2)

- Growing number tools
- current status: 10.4K tools; 44.2K versions and 213.2K containers and packages 
- Explore more at Biocontainers Registry(https://biocontainers.pro)
- ![](./img/biocregistry2.png)

# DockerHub
 - [A registry from Docker](https://hub.docker.com/bio) 
 - A centralized management of user accounts and image chesums 
 - Hosts both public/private repositories
 - Not all images can work smoothly with Singularity
     - Applications with root access
     - Applications with entrypoints
     
# DockerHub Screen Shot

 ![](./img/dockerhub.png)

# QUAY Container Registry
- Quay.io is a container registry from Red Hat
- A scalable open source platform to host container images across any size organization
- Create your own public repositories
- Provides CI support for automated builds for BioConda GitHub
- All Biocontainers are docker-based and are publicly available for free 

# Cloud Library from Sylabs

- Cloud Library is the official image
     registry provided by Sylabs.io
- [A singularity hub](https://cloud.sylabs.io/library 
- Images should work normally on HPC systems
![](./img/sylabs.png)


# Other Singularity-based resources
 - [SingularityHub](https://singularityhub.github.io/singularityhub-docs/): no longer online as a builder service, but exists as a read only archive
More information: 
 - Pulling an image from the registry:  Singularity pull shub://
 - Biocontainers repositories
    - [biocontainers singualrity](https://containers.biocontainers.pro/s3/SingImgsRepo/)
    - [Galaxy singularity](https://depot.galaxyproject.org/singularity/)


# Topic 

Deploying Biocontainers in HPC Environment


# Qualified Reference URI for Image
- A qualified image name consists of three main components:
     - Image prefix: library/shub/library
     - a registry location (hostname)
     - a username (namespace)
     - a image name (reponame)
     
- URI Prefix://hostname[:port]/username/imagename[:tag]
    -  For DockerHub registry, it is  docker://username /image name[:tag]
    -  For QUAY registry, it is docker://quay.io/username/image name[:tag]

# Working with Containers in CSC HPC Environment

 - Singualrity is installed on Puhti  (no need to load any modules)
 -  Available options	
     - Using modularised container (pre-installed for you in Puhti)
        - Examples: Rstudio, Chip-Seq-Pipeline,CrossMap, Cutadapt,EAGER,QIIME 1,Jupyter,BRAKER,aTRAM, METABOLIC
 - Using custom-made container (your own image or dowloaded from container registry)
      - Any BioContainer, Deepvariant, GATK ..etc

# Getting Started with a Modularised Container

- Load a module on Puhti/Mahti
    - e.g., module load Cutadapt
- Module command sets  some environment variables on host  machine
    - e.g., SING_IMAGE and SING_FLAGS 
- Use singularity_wrapper  which has advantages than plain singularity command
    - singularity_wrapper exec command_to_run
- Mounting datasets with SquashFS
   - when input files are too big in numbers

# Getting Started with a Custom-made Container

- Either you pull an image from registry or prepare one by yourself
- Pull/Build an image from registry repositories using singularity command
     - singularity pull hello-world.sif shub://vsoch/hello-world
     - singularity build r-base-latest.sif docker://r-base
- Note:
     - URI beginning with library:// to build from the Container Library.
     - URI beginning with docker:// to build from Dockerhub/Quay.io. 
     - URI beginning with shub:// to build from Singularity Hub.
- Executing a command
     - singularity exec -B /path/inside/container/://path/on/host/ singularity_image  actual_command

# Topic 

Mounting/binding volumesBinding external directories

# Why Mounting/Binding Host Volumes (1/2)
 - No data persistence in container file systems
 - Can’t share any data with other containers/volumes 
 - Containers are stateless 
 - Decoupling container from storage

# Why Mounting/Binding Host Volumes (2/2)
 - Volume: It is a storage. This name comes from the Enterprise use-case. Volumes are like Directories.
 - Note that you can only mount few  directories on HPC systems
      - HOME, PROJAPPL, SCRATCH
 - Binding/Mapping
      - You can bind/map directories from the Host machine into a Guest container
      - singularity run -B /guest/path:/host/path  singularity_image.simg
