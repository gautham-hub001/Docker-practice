# Docker-practice from linkedin learning course

Previous solution: virtual machines
Disadvantage:
They are heavy weight
Take a lot of disk space
Apps cannot interact with one another
VMs need to be booted up first before starting the app

apps are packaged into images
images run on containers. containers are created from images

images are created from light weight configuration files

Advantages of docker:
build apps quicly, safely and cost efficiently

1. virtual machines:
   virtualize hardware. Each VM is on top of the hypervisor which is on top of host hardware. Apps run in individual VMs which have seperate guest OS for each app.
2. containers:
   virtualize OS kernels. Docker lies on top of host OS which is on top of host hardware and apps run on top of docker

You can share images on Docker Hub

Containers are composed of Linux control groups and Linux namespaces
control groups restrict resources that the containers can use like memory, processor time,
namespaces allows admins to restrict what processes can be seen on a system

So, Docker is basically designed to work on Linux only. It uses VM to work on Mac and Windows.
So, we need Docker Desktop is used for this. It runs on Apple's own hypervisor(Virtual Kit) and Hyper-V on windows.

## Docker intallation for mac

1. download and Install docker from official page
2. verifying if docker is installed properly:
   i. open terminal
   ii. docker run --rm hello-world
   This will retrieve a docker image called hello-world and create a container from it
   You'll get a hello message
   because of --rm option, docker will automatically delete the container after it is done running the app.

## Docker intallation for windows

1. download and Install docker from official page
2. After installation it will ask if you want to use wsl2 or Hyper-V for running the docker engine. Choose wsl2
3. verifying if docker is installed properly:
   i. open powershell
   ii. docker run --rm hello-world

## Docker intallation for Linux (Ubuntu distribution)

1. open terminal
2. check if curl is installed: sudo apt install curl
3. Download docker's installation script and save it to /tmp/get-docker.sh
   i. curl -o /tmp/get-docker.sh https://get.docker.com
   ii. sh /tmp/get-docker.sh
4. verifying if docker is installed properly:
   i. docker run --rm hello-world
5. Since we don't to use sudo everytime we want to run docker:
   sudo usermod -aG docker $USER
   -a is for adding new user
   G is for specifying group name (docker)
   $USER is username
6. restart terminal
7. run:
   groups -> to see if username is added to the groups

Docker commands:

1. top level commands: ps, run, pull network
2. docker network **--help**
   This gives subcommands list:
   create
   connect
   disconnect
   ls
   rm
3. create docker container (hello-world linux version image :linux is called image tag): docker container create hello-world:linux

After the command gets executed its container id will be displayed too. And the container can be seen in the docker desktop app.

4. listing running containers: **docker ps**
5. listing all containers: docker ps --all or docker ps -a
   In the status column, if we get Exited(0): it's a good thing. It means the entry point has executed successfully.

   Listing only the container ids: docker ps -aq

6. retrieving docker images from image registry if they're not available locally (by default the registry is docker hub) and running them: docker run --rm hello-world
7. run a container: docker container start c6d3562ab828
8. Now check if the container ran successfully using the docker ps --all command
9. To check logs of what happened when the app ran (using first chars of container id): docker logs c6d
10. To get logs directly while running the container, use --attach tag: docker container start --attach c6d

Note: You can also see all the logs in the docker desktop app.

11. Shortcut to create, start, and see logs of containers (in single command): docker run hello-world

docker container run = docker container create + docker container start + (--attach / docker container logs)

12. Building image from our own docker file:
    docker file syntax:
    FROM ubuntu(baseimage)

    LABEL maintainer="Gautham"(extrainfo)

    USER (which user to use for the commands under this. By default it's root)

    COPY ./entrypoint.bash/ (also called as context)(copies files from the directory, to the docker image. It is generally the working directory)

    RUN apt -y update (for customizing the image)

    ENTRYPOINT ["/entrypoint.bash"] (what commands should be run by the container)

    # Creating docker image from docker file: docker build

    docker build -t image-tag-name context --file dockerfilename
    By default, it will check for Dockerfile. If you have a different name, you need to specify it using --file/ -f tag.

    ## docker build -t my-first-image .

    Note: docker creates images for each command in the dockerfile. These are called intermediate images. At the end of the Dockerfile, it squashes all these images into a single images.

    Now create the container
    docker run my-first-image

13. running containers which run for long time/ forever (like servers) without attaching to the logs: docker run -d my-server
14. Stop a running container: docker stop d2d112
15. Removing a stopped container: docker rm d2d112
    Note: rm only removes containers which are already stopped.
    Removing any container (stopping and removing it if it is not already stopped): docker rm -f d2d112
16. Stopping and removing all the existing containers : docker ps -aq | xargs docker rm
    | (pipe) is for taking output on the left and feeding it to the right
    xargs is for feeding the output one-by-one
17. Removing a stopped image: docker rmi my-first-image
18. List of images: docker images
