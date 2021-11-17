---
theme: csc-2019
lang: en
---
# Converting Docker Images to Singularity {.title}

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
- Brief introduction to Docker vs. Singualrity  images
- Deploying singularity containers from docker images
- HPC disk Systems and efficient image conversion 
- Good practice tips while converting images


#  
<br><br><br>
<p align="center" clor="green"> **Brief introduction to Docker *vs.* Singualrity  images** </p>

# Why do we care for pre-built Docker Images 

- The docker is a leading container platform  and is widely adopted in industry/cloud applications
- Large number of images are archived in docker registries
- GPU-accelerated solutions to the HPC community
- One needs root privileges to create docker containers from the docker images
- Directly building Singularity image seems more difficult
-  Good news:  Singularity nicely integrates with docker images
- Challenge: Not all docker images will work smoothly  with singularity 


# Docker vs. Singularity Images
- Docker image
     - Image is a layered structure (OCI blobs)
     - Stored in the local image cache  when using docker client
     - Saves some space by sharing some layers across different local images when storing / caching the build layers can speed up the build process
- Singularity image
     - Single image file (.sif) / images are flat 
     - Stored as a normal file
     - Easy portability : transferring and sharing images across a cluster is very easy
     - Image can be used as binary (./image.sif)

#  
<br><br><br>
     <p align="center"> **Deploying Singularity Containers from Docker Images ** </p>

# Running Singularity Containers from Public Docker Images
- Use Singularity subcommands
    - singularity pull …
    - singularity build …
    - Build is functionnally equivalent to pull but does more (like making a writable image) 
- Examples: 
    - singularity pull docker://biocontainers/comet:v2016011_cv5
    - singularity build docker://biocontainers/comet:v2016011_cv5
    - singularity build --sandbox mycomet_sandbox  \ docker://biocontainers/comet:v2016011_cv5


# Running Singularity Containers from Private Docker Images
- One can have a create a private repository in docker registry
- In private repo, one has to authenticate before pulling an image

- singularity pull --docker-login docker://privaterepo/image:tag
      - export SINGULARITY_DOCKER_USERNAME=username
      - export SINGULARITY_DOCKER_PASSWORD=password

- Finally test that image works :  ./imagename.sif

# Running Singularity Containers from  Local Docker Images
- Sometimes pre-built docker images may not work or does not meet our needs 
- First, save your local docker image from your machine (VM, laptop)
     -  Check image using the following: sudo docker images
     - sudo docker save image_id -o local_docker.tar
- Copy tar file  to HPC environment 
- Finally, build a singularity image from the local_docker.tar 
     - singularity build local_singularity_image.sif docker-archive://local_docker.tar
     - Using docker-archive bootstrap agent.

# Sinularity Cache in HPC environment
- Default location: $HOME/.singularity
     - Amount of available space in $HOME in HPC systems is limited by a quota
     - Your home can be quickly filled up and end up disk space errors 
- CSC users can configure the Singularity cache using two environment variables: 
     - SINGULARITY_CACHEDIR: Cache folder for images from a container registry.
     - SINGULARITY_TMPDIR: Temporary directory to build container file-systems.
- Useful tips:
     - singularity cache list  # show storage capacity used by the cache
     - singularity cache clean # clean up everything 

#  

<br><br><br>
     <p align="center"> **HPC Disk Systems and Efficient Image Conversion** </p>



# Disk and Storage Overview
 ![](./img/disk-systems.svg){width=90%}

# Use NVMe disks for faster image conversion
- Working with bigger images for your needs 
- Not all compute nodes have these NVMe disks
- You can request these resources in batch script /interactive node
- Use the environment variable $LOCAL_SCRATCH to access local storage on each node.
- In batch jobs, remember to copy the files to back to your scratch folders

#  

<br><br><br>
     <p align="center"> **Good Practices Tips While Converting Images** </p>

# Good Practices in Image Conversion

- Maintain tags of the image (reproducibility !)
     - generic URI for docker :docker://<user>/<repo-name>[:<tag>]
     - developers to release several different versions of the same container with different tags
- Hashes  are even more unique 
     - library://debian:sha256.b92c7fdfcc615
- Pay attention to special tag: latest
     - Always pull same image: singularity pull library://debian:sha256.b92c7fdfcc615

#Good Practices in Image Conversion

- Don’t use any image that would be running under root
     - Works fine in docker world and results in bugs in singularity
- If you modify the image,  don’t install to $HOME or $TMP
- Docker images with ENTRYPOINT scripts are usually broken
           - ENTRYPOINT [ "entrypoint_script.sh" ]

#
<br><br><br>
     <p align="center">**Time for Tutorials **</p>
