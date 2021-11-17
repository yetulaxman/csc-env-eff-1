---
theme: csc-2019
lang: en
---
# Containerised Bio Applications in HPC Environment {.title}

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
- Getting started with containerised applications
- Biocontainers and related registries
- Deploying (running) biocontainers in HPC environment
   - Containers available as modules
   - Custom-made containers
- Mounting/binding volumes

#  
<br><br><br>
<p align="center" clor="green"> **Getting Started With Containerised Applications** </p>

# Some Basic Terminology

![](./img/terminology.png)


# Central Dogma of Containerisation

![](./img/dogma.png)

- Two containerisation platforms
    - Docker image built from dockerfiles
    - Singularity image built from singularity recipes (deffiles)


# Benefits of Running a Software as  Container

![](./img/benefits.png)


#  
<br><br><br>
     <p align="center"> **Biocontainers and Related Registries** </p>

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
- Source of Biocontainer image can be conda recipe or dockerfile <br>
  ![](./img/biocregistry.png) <br> 
           <p align="right"> <small> Bai J. et al. (2021) J Proteome Res. 2021 Apr 2;20(4):2056-2061 </small> </p>

# Biocontainers Registry (2/2)

- Growing number tools
- current status: 10.4K tools; 44.2K versions and 213.2K containers and packages 
- Explore more at [Biocontainers Registry](https://biocontainers.pro)


# DockerHub
 - [A registry from Docker](https://hub.docker.com/bio) 
 - A centralized management of user accounts and image chesums 
 - Hosts both public/private repositories
 - Not all images can work smoothly with Singularity
     - Applications running under *root* 
     - Applications running with *entrypoints*
     
# DockerHub Screen Shot

 ![](./img/dockerhub.png)

# QUAY Container Registry
- Quay.io is a container registry from Red Hat
- A scalable open source platform to host container images across any size organization
- Create your own public repositories
- Provides CI support for automated builds for BioConda GitHub
- All Biocontainers are docker-based and are publicly available for free 

# Cloud Library from Sylabs

- [A singularity registry](https://cloud.sylabs.io/library)
- Cloud Library is the official image registry provided by Sylabs.io
- Provides container service including remote building
- The images should work normally on HPC systems


# Other Singularity-based resources
 - [SingularityHub](https://singularityhub.github.io/singularityhub-docs/): no longer online as a builder service, but exists as a read only archive
More information: 
 - Pulling an image from the registry:  Singularity pull shub://
 - Biocontainers repositories
    - [Biocontainers SingImgRepo](https://containers.biocontainers.pro/s3/SingImgsRepo/)
    - [Galaxy singularity](https://depot.galaxyproject.org/singularity/)


#  

<br><br><br>
     <p align="center"> **Deploying Biocontainers in HPC Environment** </p>



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
        - Examples: *Rstudio, Chip-Seq-Pipeline,CrossMap, Cutadapt,EAGER,QIIME1,Jupyter,BRAKER,aTRAM and METABOLIC*
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
     - *singularity pull hello-world.sif shub://vsoch/hello-world*
     - *singularity build r-base-latest.sif docker://r-base*
- Note:
     - URI beginning with library:// are from Container Library.
     - URI beginning with docker:// are from Dockerhub/Quay.io. 
     - URI beginning with shub:// are from Singularity Hub.
- Executing a command
     - use *singularity* or *[singularity_wrapper](https://docs.csc.fi/computing/containers/run-existing/)* 

#
<br><br><br>
     <p align="center">**Mounting/Binding Host Volume**</p>

# Why Mounting/Binding Host Volumes 
 - No data persistence in container file systems
 - Can’t share any data with other containers/volumes 
 - Containers are stateless 
 - Decoupling container from storage

# Mounting/Binding Host Volumes
 - Volume: Volumes are like Directories. This name comes from the Enterprise use-case. 
 - Note that you can only mount few  directories on HPC systems
      - HOME, PROJAPPL, SCRATCH
 - Binding/Mapping
      - You can bind/map directories from the host machine into a guest container
      - *singularity run -B /guest/path:/host/path  singularity_image.simg*

#
<br><br><br>
     <p align="center">**Time for Tutorials:<br>BLAST <br> DeepVariant**</p>
