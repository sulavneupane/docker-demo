#Docker Demo

##What is Docker?
* Virtualization software
* Makes developing and deploying applications much easier
* Packages application with all the necessary dependencies, configuration, system tools and runtime
* Portable artifact, easily shared and distributed

##What problems Docker solves?
###Development process before containers:
* Each developer needs to install and configure all services directly on their OS on their local machine.
* Eg. If your app uses 10 services, each developer needs to install these 10 services
* Problems:
  - Installation process different for each OS environment
  - Many steps, where something can go wrong

###Development process with containers:
* Own isolated environment
* Postgres packaged with all dependencies and configs
* As a developer:
  - Start service as a Docker container using a 1 Docker command (Eg. docker run postgres)
  - Command same for all OS (Eg. docker run postgres)
  - Command same for all services (Eg. docker run postgres, docker run redis, docker run ...)
* Standardizes process of running any service on any local dev environment
* Easy to run different versions of same app without any conflicts

###Deployment process before containers:
* Artifact and instructions handed over to OPS team
* Ops team handles installing and configuring apps and its dependencies
* Problems:
  - Dependency version conflicts, etc.
  - Miscommunications
  - Human errors can happen
  - Back and forth communication

###Deployment process with containers:
* Instead of textual, everything is packaged inside the Docker artifact
* Benefits:
  - No configurations needed on the server (except Docker runtime)
  - Less room for errors
* Install Docker runtime on the server
* Run Docker command to fetch and run the Docker artifacts

##Virtual Machine VS Docker
###How an OS is made up?
* Kernel is at the core of every operating system
* Kernel interacts between hardware and software components

###What parts of the OS do they virtualize?
####Docker:
* Contains the OS application layer
* Services and apps installed on top of that layer
#####Virtual Machine:
* Complete operating system including applications layer and kernel

###What affects has this difference?
* Size: Docker images, couple of MB whereas VM images, couple of GB
* Speed: Docker containers take seconds to start whereas VMs take minutes to start
* Compatibility: Docker compatible only with Linux distros wheres VM is compatible with all OS
* Benefits:
  - Most containers are Linux based
  - Originally built for Linux OS
* Problem:
  - Linux based Docker images, cannot use Windows kernel

*Note: Later Docker built Docker Desktop which allows us to run Linux containers on Windows or MacOS. Docker Desktop uses a Hypervisor layer with a lightweight Linux distro.*

##Install Docker on your local machine:
* Go to the official Docker website
* Follow installation steps
  Note: Always best to refer to latest installation guide in the official documentation.

##Docker Desktop includes:
* Docker Engine
    - A server with a long-running daemon process "dockerd"
    - Manages images & containers
* Docker CLI - Client
    - Command Line Interface ("docker") to interact with Docker Server
    - Execute Docker commands to start/stop/... containers
* GUI Client
    - To manage your containers and images with a graphical user interface

##Docker Images VS Docker Containers
* Docker allows to:
    - Package everything into an artifact
    - Can be easily shared and moved
    - Like a zip or tarfile or jar file
* Docker Image:
    - An executable application artifact
    - Includes app source code, but also complete environment configuration (Application -> eg. JS app, Any services needed -> eg. node, npm, OS Layer -> eg. Linux)
    - Add environment variables, create directories, files, etc.
    - Immutable template that defines how a container will be realized
    - You can run multiple containers from 1 image
* Docker Container:
    - Actually starts the application
    - A running instance of an image
    - That's when the container environment is created

##Docker Registry:
* A storage and distribution system for Docker images
* Official images available for applications like Redis, Mongo, Postgres, etc.
* Official images are maintained by the software authors or in collaboration with the Docker community
* Find and share Docker images
* Docker hosts one of the biggest Docker Registry, called "Docker Hub"
  Note: Docker Hub registry (docker.io) is used by default

###Public and Private Docker Registries:
* Private Docker Registries:
    - We need to authenticate before accessing the registry
    - All big cloud provider offer private registries: Amazon ECR, Google Container Registry, etc.

###Registry VS Repository:
* Docker Registry
    - A service providing storage
    - Collection of repositories
    - Eg. Docker Hub
* Docker Repository
    - Collection of related images with same name but different versions
    - Eg. On Docker Hub we can host private or public repositories for our applications

##Docker Official Images:
* A dedicated team responsible for reviewing and publishing all content in the Docker Official Images repositories
* Works in collaboration with software maintainers, security experts
* However anyone can participate as collaboration takes place openly on GitHub

##Image Versioning:
* Docker images are versioned
* Different versions are identified by tags
* "latest" tag mostly refers to the newest release
  Note: Using a specific version is the best practice in most cases.

##Port Binding:
* Container Port VS Host Port:
    - Application inside container runs in an isolated Docker network
    - We need to expose the container port to the host (the machine the container runs on)
* Bind the container's port to the host's port to make the service available to the outside world
* Container runs on a specific port
* Publish container's port to the host
* Standard to use the same port on your host as container is using

##Create Own Images (Dockerfile)
* Companies create custom images for their applications
* Building own Docker Images:
    - We want to deploy our app as a Docker container
* Dockerfile - Build instruction
    - Dockerfile is a text document that contains commands to assemble an image
    - Docker can then build an image by reading those instructions

###Structure of Dockerfile
* Dockerfiles start from a parent image or "base image"
* It's a Docker image that your image is based on

####FROM
* Build this image from the specified image

####RUN
* Will execute any command in a shell inside the container environment

####COPY
* Copies files or directories from <src> and adds them to the filesystem of the container at the path <dest>
* While "RUN" is executed in the container, "COPY" is executed on the host

####WORKDIR
* Sets the working directory for all following commands
* Like changing into a directory: "cd ..."

####CMD
* The instruction that is to be executed when a Docker container starts
* There can only be one "CMD" instruction in a Dockerfile

*Note: You choose the base image, depending on which tools you need to have available*

### Build Image
* Build docker image by using the "docker build {path}" command.
* Remember: A Docker image consists of layers
* Each instruction in the Dockerfile creates one layer
* These layers are stacked & each one is a delta of the changes from the previous layer

##Docker Commands:
###docker images
    - List all Docker images
###docker ps
    - List running containers
    - "-a or --all" lists all containers (stopped and running)
###docker pull {name}:{tag}
    - Pull an image from a registry
    - "docker pull {name}" will pull the latest image form the registry
    - Examples:
      -> docker pull nginx:1.25
      -> docker pull nginx
###docker run {name}:{tag}
    - Creates a new container from given images and starts it. Doesn't re-use previous container.
    - "-d or --detach" runs container in background and prints the container ID. Eg. "docker run -d nginx:1.25"
    - "-p or --publish {HOST_PORT}:{CONTAINER_PORT}" publish a container's port to the host. Eg. "docker run -p 9000:80 nginx:1.25"
    - "--name" assign a name to the container. Eg. "docker run --name web-app nginx:1.25"
    - Docker generates a random name for the container automatically if we don't specify one
    - Docker pulls images automatically, if it doesn't find it locally
###docker logs {container}
    - View logs from service running inside the container. (which is present at the time of execution)
    - Eg. docker logs 43db4f1693d9
###docker stop {container}
    - Stop one or more running containers
###docker start {container}
    - Start one or more stopped containers

###docker build {path}
    - Builds a Docker image from a Dockerfile
    - "-t or --tag" sets a name and optionally a tag in the "name:tag" format 

###docker login
    - Log into a registry

*Note: "docker build {path}" command can fail with the latest docker so always run "docker login" to log into the registry before trying to build.*